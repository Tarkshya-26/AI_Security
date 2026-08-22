# Command Cheat Sheet — SupplySecLab

> **Quick-reference for every command in *Securing The AI Supply Chain*.** Grouped by task. Each block is copy-ready — click the code and paste straight into the VM terminal.
> 

`analyst@tryhackme-2204:~$` is the VM prompt. Copy only the command that follows it.

---

## 🗺️ At a glance

| **Stage** | **Tool / Command** | **Catches** |
| --- | --- | --- |
| Safe serialisation | `safetensors`, `weights_only=True` | Pickle code execution at load time |
| Integrity | `sha256sum`, `checksums.json` | Tampered / swapped model files |
| Static pickle scan | `fickling` | Malicious pickle bytecode |
| Multi-format scan | `modelscan` | Unsafe operators (PyTorch/TF/Keras) |
| Architecture | `inspect_h5_model.py`, `modelscan` | Malicious Keras Lambda layers |
| Dependencies | `pip-audit`, `pip-compile` | Vulnerable / confused packages |
| SBOM | `syft` | Unknown components in the build |
| API providers | Checklist + behaviour baseline | Black-box model / prompt drift |

---

## 1 · Safe Serialisation (Task 2)

**Use case:** Eliminate pickle code-execution by moving weights to SafeTensors and restricting `torch.load`.

Convert an existing pickle model to SafeTensors:

```python
import torch
from safetensors.torch import save_file, load_file

# 1. Load the pickle model safely (weights_only restricts pickle)
model_weights = torch.load("model.pkl", weights_only=True)

# 2. Save as SafeTensors
save_file(model_weights, "model.safetensors")

# 3. Load the SafeTensors model — always safe
safe_weights = load_file("model.safetensors")
```

Restrict PyTorch's unpickler (default since PyTorch 2.6, set it explicitly on older versions):

```python
import torch

# UNSAFE — pickle can execute embedded code
model = torch.load("model.pt")

# SAFE — pickle limited to tensor reconstruction only
model = torch.load("model.pt", weights_only=True)
```

> ⚠️ SafeTensors only stops code execution **at load time**. It does not stop architecture-level attacks (see Task 7).
> 

---

## 2 · Integrity & Provenance (Task 3)

**Use case:** Prove a model file has not been tampered with or swapped since its checksum was published.

List the lab directory:

```bash
ls -la /opt/supply-chain/{folder_name}
```

Read the expected hashes:

```bash
cat /opt/supply-chain/models/checksums.json
```

Compute actual SHA-256 hashes and compare against `checksums.json` (one will not match — that file is tampered):

```bash
sha256sum /opt/supply-chain/models/product_recommender.safetensors /opt/supply-chain/models/model_review_v2.pkl /opt/supply-chain/models/product_recommender.pkl
```

> 💡 A checksum proves the file didn't change. A **digital signature** additionally proves *who* published it — prefer signed artefacts when available.
> 

---

## 3 · Static Scanning — Fickling & ModelScan (Task 5)

**Use case:** Scan a model *before* it ever loads, so a malicious payload never executes.

### Fickling — decompile pickle bytecode

Decompile a pickle to readable Python (reveals `os.system` / `curl` payloads):

```bash
fickling /opt/supply-chain/models/model_review_v2.pkl
```

Decompile a clean model (a plain dict → nothing flagged):

```bash
fickling /opt/supply-chain/models/product_recommender.pkl
```

Automated safety check, printed to the terminal (`-p`). Flags **overtly malicious**:

```bash
fickling --check-safety -p /opt/supply-chain/models/model_review_v2.pkl
```

Safety check on the clean model — **no output means no issues**:

```bash
fickling --check-safety -p /opt/supply-chain/models/product_recommender.pkl
```

> Without `-p`, results are written silently to `safety_results.json`.
> 

### ModelScan — multi-format scanner (pickle, PyTorch, TF, Keras)

Scan the tampered pickle (expect **CRITICAL — unsafe operator `system` from `os`**):

```bash
modelscan -p /opt/supply-chain/models/model_review_v2.pkl
```

Scan the SafeTensors model (expect **No issues found 🎉**):

```bash
modelscan -p /opt/supply-chain/models/product_recommender.safetensors
```

**Severity → action:** CRITICAL = quarantine now · HIGH = no use without review · MEDIUM = review carefully · LOW = note & monitor.

---

## 4 · Architecture-Level Threats (Task 7)

**Use case:** Catch malicious Keras **Lambda layers** that run at *inference* time — invisible to load-time scans and surviving format conversion.

ModelScan's Lambda detector on the suspicious `.h5` (expect **MEDIUM — unsafe operator `Lambda`**):

```bash
modelscan -p /opt/supply-chain/models/image_classifier_v2.h5
```

Inspect layer architecture with `h5py` (reads structure without loading/executing). Clean baseline model:

```bash
python3 /opt/supply-chain/tools/inspect_h5_model.py /opt/supply-chain/models/image_classifier.h5
```

Suspicious model — compare layer count, look for `[WARNING]` vs `[OK]`:

```bash
python3 /opt/supply-chain/tools/inspect_h5_model.py /opt/supply-chain/models/image_classifier_v2.h5
```

> ⚠️ Attackers disguise Lambda names as `normalize_output`, `apply_scaling`, etc. Any Lambda/custom layer you didn't build warrants investigation.
> 

---

## 5 · Dependency Auditing & SBOMs (Task 8)

**Use case:** Treat every dependency with the same scrutiny as the model — pin versions, lock hashes, scan for CVEs, and produce an ingredient list.

### Version pinning — `requirements.txt`

```
# BAD — allows any version
numpy
requests

# BETTER — pins major.minor, allows patches
numpy>=1.24,<1.25
requests>=2.31,<2.32

# BEST — pins exact version
numpy==1.24.3
requests==2.31.0
```

### Lockfiles (version + cryptographic hash)

```bash
# pip-tools → requirements.txt with hashes
pip-compile --generate-hashes

# Poetry → poetry.lock
poetry lock
```

### pip-audit — vulnerability scan

Check project dependencies against known-vulnerability databases:

```bash
pip-audit -r /opt/supply-chain/project/requirements.txt
```

### Private package index — block dependency confusion

```
# ~/.pip/pip.conf
[global]
index-url = https://your-private-pypi.company.com/simple/
extra-index-url = https://pypi.org/simple/
```

> `index-url` = primary (checked first) · `extra-index-url` = fallback. Internal packages then always resolve privately.
> 

### Syft — generate an SBOM

CycloneDX JSON, saved for scanners/compliance tools:

```bash
syft /opt/supply-chain/project/ --exclude './venv/**' -o cyclonedx-json > /tmp/sbom.json
```

Human-readable table of everything Syft found:

```bash
syft /opt/supply-chain/project/ --exclude './venv/**' -o table
```

Explore the SBOM JSON (arrow keys to scroll):

```bash
cat /tmp/sbom.json | python3 -m json.tool | less
```

> 📦 SBOM formats: **SPDX** (Linux Foundation, licence-focused, ISO/IEC 5962:2021) vs **CycloneDX** (OWASP, security-focused, includes vuln data).
> 

---

## 6 · API Provider Assessment (Task 9)

**Use case:** When you call a hosted LLM there is no file to scan — the file-based tools above don't apply. Govern the *provider* and the *behaviour* instead.

Four defences (there are no shell commands here — this is a governance gate):

1. **Provider due diligence** — verify data handling / retention & opt-out, versioned endpoints & changelogs, SOC 2 / ISO 27001, incident-response policy, transparency (model cards).
2. **Behaviour monitoring** — run a fixed prompt battery periodically; track accuracy, format, refusal rate, latency. Drift = the model behind the endpoint may have changed. *(This is the API equivalent of checksum verification.)*
3. **System prompt governance** — treat external prompt templates as supply-chain artefacts: version-control, review, and test against your baseline before deploy.
4. **Sandboxed evaluation** — before production, run known-answer prompts, adversarial probes, and baseline comparison in an isolated environment. Any fail → reject.

---

### 🔒 Golden rule

No single check is sufficient. Quarantine → verify source → check integrity → scan (Fickling + ModelScan + pip-audit + Syft) → inspect architecture → approve or reject. Every artefact passes the same gate before production.

> *Source: [Securing The AI Supply Chain](../Securing%20The%20AI%20Supply%20Chain%203a823d6a56fc80c7b899fef4f90dcd9d.md) · SupplySecLab, TryTrainMe.*
>