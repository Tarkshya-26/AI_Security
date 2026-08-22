# AI Security Documentation

> **From building AI systems to understanding how they can be attacked, exploited, and secured.**

This repository documents my journey from **AI/LLM Engineering → AI Security**, combining foundational security knowledge, AI security research, hands-on labs, and structured notes from my learning paths.

The goal is to build a strong understanding of the security problems introduced by modern AI systems — especially **LLMs, RAG pipelines, AI agents, model supply chains, and AI infrastructure**.

---

## 🧭 Journey

My AI journey started with building and understanding AI systems:

**Python → ML → LLMs → RAG → Agentic AI → AI Security**

This repository represents the next stage of that journey:

```text
AI Engineering
      │
      ├── LLMs
      ├── RAG
      ├── Agentic AI
      └── AI Infrastructure
              │
              ▼
        AI Security
              │
              ├── AI Threat Modeling
              ├── LLM Security
              ├── Prompt Injection
              ├── Jailbreaking
              ├── AI Reconnaissance
              ├── Model Security
              ├── AI Supply Chain Security
              ├── Agent Security
              └── Defensive Engineering
```

---

# 📚 What's Inside

## 1. Security Foundations

Before attacking AI systems, I am building the underlying security knowledge required to understand their attack surface.

Topics include:

- Networking fundamentals
- Firewalls
- VPNs
- Routers
- DNS
- HTTP
- HTTP methods
- Virtualisation
- Containers
- Cloud computing
- Linux CLI
- Windows CLI
- Operating System Security
- Offensive Security fundamentals

---

## 2. AI & LLM Security

The core of this repository focuses on understanding how modern AI systems can fail from a security perspective.

### LLM Security

Topics covered include:

- OWASP LLM Top 10
- MITRE ATLAS
- NIST AI Risk Management Framework
- Prompt Injection
- Indirect Prompt Injection
- Jailbreaking
- System Prompt Leakage
- Sensitive Information Disclosure
- Improper Output Handling
- Excessive Agency
- Unbounded Consumption
- Context Overflow
- Memory Poisoning
- Training Data Extraction
- Membership Inference
- Model Extraction
- Model Inversion

---

## 3. AI Threat Modeling

Understanding AI security requires more than simply applying traditional application-security frameworks.

This repository explores:

- STRIDE
- STRIDE for AI systems
- MITRE ATLAS
- OWASP LLM Top 10
- AI attack surfaces
- Threat modeling AI components
- AI-specific attack paths
- Security boundaries in LLM applications

### AI Threat Modeling Flow

```text
AI Application
      │
      ├── User Input
      │
      ├── LLM
      │
      ├── System Prompt
      │
      ├── RAG / Vector DB
      │
      ├── Tools
      │
      ├── Agents
      │
      ├── External APIs
      │
      └── Infrastructure
              │
              ▼
        Threat Modeling
              │
              ├── STRIDE
              ├── OWASP LLM Top 10
              └── MITRE ATLAS
```

---

# 🔎 AI Reconnaissance

A major section of the notes focuses on understanding how attackers discover AI infrastructure.

Topics include:

- HTTP header fingerprinting
- API response signatures
- Error-message fingerprinting
- Endpoint naming conventions
- gRPC fingerprinting
- TLS fingerprinting
- MLflow enumeration
- Inference-server metadata
- Vector database enumeration
- Prometheus metrics
- Jupyter notebook enumeration
- Debug interfaces
- Model registries
- AI infrastructure reconnaissance
- Supply-chain reconnaissance

The objective is to understand **what information an attacker can gather before exploitation begins**.

---

# 💥 AI Attack Techniques

The repository contains detailed notes on offensive techniques against AI systems.

## Prompt Injection

Topics include:

- Instruction boundaries
- Direct prompt injection
- Indirect prompt injection
- Prompt manipulation
- Context manipulation
- Instruction hierarchy
- Prompt injection case studies

## Jailbreaking

Topics include:

- Safety alignment
- Model manipulation
- Gradual safety erosion
- Jailbreak techniques
- DAN
- Real-world jailbreak examples

## AI Data Attacks

Including:

- Data poisoning
- Training data extraction
- Membership inference
- Model inversion
- Model extraction
- Memory poisoning

---

# 🛡️ AI Defence

The goal isn't only to understand how AI systems are attacked.

I am also studying how to **design systems that remain secure under adversarial interaction**.

Defensive concepts include:

- Defence in depth
- Least privilege
- Input validation
- Output validation
- Input sanitisation
- Guardrails
- Structured prompts
- Tool restrictions
- Rate limiting
- Logging
- Monitoring
- Observability
- Secure agent design
- Secure AI supply chains

---

# 🔗 AI Supply Chain Security

Modern AI applications depend on a large ecosystem:

```text
Data
 │
 ▼
Training
 │
 ▼
Base Model
 │
 ▼
Fine-Tuning
 │
 ▼
Model Registry
 │
 ▼
RAG / Vector Database
 │
 ▼
AI Application
 │
 ▼
Agent + Tools
 │
 ▼
Production Infrastructure
```

Every layer introduces potential attack surfaces.

The repository therefore covers:

- AI data supply chains
- Model registries
- Pre-trained models
- Fine-tuning
- Model inheritance
- Model poisoning
- Dependency risks
- Supply-chain reconnaissance
- AI infrastructure security

---

# 🧪 Hands-On Learning

This repository is not intended to be purely theoretical.

It combines:

- Certification notes
- TryHackMe learning paths
- Hands-on security labs
- Attack analysis
- Case studies
- AI security experiments
- Threat-modeling exercises
- Research notes

The practical objective is:

> **Understand the vulnerability → reproduce the attack → understand the root cause → design the defence.**

---

# 📖 Learning Sources

This documentation is primarily built around two major parts of my AI Security journey:

### AI / LLM Engineering Foundation

Notes from my pre-security AI journey covering concepts necessary to understand modern AI systems, including:

- Machine Learning
- LLMs
- RAG
- Agentic AI
- AI infrastructure
- Model behaviour

### AI Security Path

Hands-on security learning through **TryHackMe** and related AI security material, covering:

- Security fundamentals
- LLM security
- AI reconnaissance
- Prompt injection
- Jailbreaking
- AI supply-chain security
- AI threat modeling
- Defensive AI engineering

---

# 🎯 Objective

The long-term objective of this repository is to develop the ability to approach AI systems from **both sides of the security boundary**.

```text
        BUILD
          │
          ▼
    AI / LLM Systems
          │
          ▼
      UNDERSTAND
          │
          ▼
     ATTACK / TEST
          │
          ▼
       ANALYZE
          │
          ▼
       DEFEND
          │
          ▼
    SECURE AI SYSTEMS
```

Rather than treating AI Security as a completely separate discipline, I am approaching it as the intersection of:

**AI Engineering + Cybersecurity + Adversarial Thinking**

---

# 🚀 Current Focus

My current focus is on developing deeper expertise in:

- LLM Security
- Agent Security
- AI Threat Modeling
- Prompt Injection
- AI Supply Chain Security
- AI Reconnaissance
- Secure RAG
- Secure Tool-Calling
- Agentic AI Security
- AI Red Teaming
- Defensive AI Engineering

---

## ⚠️ Disclaimer

This repository is maintained for **educational, research, and defensive security purposes**.

Techniques documented here should only be used against systems where you have explicit authorization to test.

---

## 📈 Journey

This repository will continue evolving as I move from:

**AI Engineer → AI Security Engineer**

and eventually toward building AI systems that are not only capable, but **secure by design**.
