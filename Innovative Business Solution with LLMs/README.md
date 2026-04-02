# 🤖 RoboAdvisor — AI-Powered Expert Advisor for Industrial Robotics

> **Course:** AI Product Management Nanodegree — Project 3  
> **Scenario:** C — AI-Powered Expert Advisor  
> **Role:** Product Manager, Industrial Robotics Division

---

## 📋 Table of Contents

1. [Problem Statement](#-problem-statement)
2. [What Needs to Be Done](#-what-needs-to-be-done)
3. [Key Definitions](#-key-definitions)
4. [Solution Pattern — How It Gets Solved](#-solution-pattern--how-it-gets-solved)
5. [System Architecture Deep Dive](#-system-architecture-deep-dive)
6. [LLM Configuration](#-llm-configuration)
7. [Success Metrics](#-success-metrics)
8. [Output](#-output)

---

## 🚨 Problem Statement

### The Company

A global industrial robotics manufacturer supports **thousands of robot configurations** across factories on every continent, in multiple local languages, across 15 robot models and 45,000 parts.

### The Users

Field technicians — aged 20–60, 85% with a high school education — who configure, maintain, and troubleshoot these robots daily. They work across 5 languages: English, Spanish, Cantonese, Mandarin, and Urdu. 95% are on mobile devices on the factory floor.

### The Core Problem

> **Technicians cannot quickly and reliably get the guidance they need — production stops, senior staff burn out, and non-English speakers are left entirely behind.**

The current system is a knowledge base of **7,500 English-only articles** that technicians must search manually. Here's what that looks like in practice:

| Pain Point | Impact |
|------------|--------|
| Average query takes **15–30 minutes** | Production line sits idle |
| Complex issues require escalating to senior staff | Senior engineers become bottlenecks |
| Knowledge base is **English only** | ~30%+ of technicians face a language barrier |
| No conversational guidance — just keyword search | Wrong or incomplete results are common |
| Production downtime costs **$10,000–$50,000/hour** | Every unresolved minute is expensive |

The existing approach is a patchwork of manual search + waiting for experts + hoping the right article exists in English. It does not scale and it is not safe.

---

## ✅ What Needs to Be Done

The rubric for this project requires four major deliverables, each with specific requirements:

### 1. Product Definition
- [ ] **Describe the problem** — summarize the business problem, how users solve it today, and the biggest issues with the current approach
- [ ] **Describe the solution** — 1–3 sentences, precise language, focused on benefit + features, better than current approach
- [ ] **Identify risks and mitigations** — 3–5 risks that could block success, with creative and realistic mitigations

### 2. System Details
- [ ] **System Attributes** — primary goal of the system + 1–2 secondary goals (benefits, not features)
- [ ] **System Architecture** — list components, draw a diagram of relationships, highlight what must be encrypted/private and why
- [ ] **LLM Configuration** — explicit choices for license type, deployment model, temperature, Top K, and other settings with justification for each

### 3. Industry Best Practices
- [ ] **Success Metrics** — 3–5 measurements that define success or failure, including both *outcome* metrics and *usage/engagement* metrics

### 4. Bonus (Stand Out)
- [ ] Sample system prompt and user prompt
- [ ] Reference data plan (what data, how much, where from, how often updated, bias checks)
- [ ] Architecture diagram with labeled components and secure connections

---

## 📖 Key Definitions

Understanding the terminology used throughout this project:

### LLM (Large Language Model)
A type of AI trained on vast amounts of text that can understand and generate human language. In this project, the LLM is the "brain" of the advisor — it reads retrieved documents and composes a helpful, safe, step-by-step response for the technician.

### RAG (Retrieval-Augmented Generation)
A system design pattern where the LLM does **not** answer from memory alone. Instead:
1. The user's question is used to **retrieve** the most relevant documents from a knowledge base
2. Those documents are **given to the LLM as context**
3. The LLM **generates** its answer grounded in those documents

This is critical for industrial robotics because it prevents hallucination of part numbers, procedures, or safety steps that don't exist in official documentation.

### Vector Database
A specialized database that stores text as **mathematical embeddings** (numerical representations of meaning). When a technician asks a question, it is also converted to an embedding and compared against the database to find the **most semantically similar** document chunks — not just keyword matches. This is the retrieval engine inside RAG.

### Temperature (LLM Setting)
Controls how creative or random the LLM's output is, on a scale of 0 to 1. **Higher = more creative, lower = more predictable.** For safety-critical industrial guidance, we want **low temperature (0.1)** so that identical questions always produce consistent, reliable answers.

### Top K (Retrieval Setting)
The number of document chunks returned by the vector database and fed to the LLM as context. **Top K = 3** means only the 3 most relevant chunks are retrieved. Fewer chunks reduce the risk of contradictory guidance appearing in the response.

### Private Cloud Deployment
Rather than sending queries to a shared public API, the LLM runs on **dedicated servers inside the company's own Virtual Private Cloud (VPC)**. No technician query data, fault codes, or machine configurations ever leave the company's network perimeter.

### Safety Layer
A validation step that sits **between the LLM's raw output and the technician**. It checks that every response cites a source document, flags or blocks responses that contain no verifiable citation, and routes to human escalation when the system is uncertain.

### System Prompt
A set of hidden instructions given to the LLM before every conversation that define its identity, rules, and constraints. The technician never sees this — it shapes how the model behaves for every query.

### Fine-tuning
Optional process of continuing to train an LLM on domain-specific data to improve its tone and phrasing. Distinguished from RAG: RAG updates the *knowledge* the model can access; fine-tuning updates the model's *behavior patterns*. Planned for Phase 2 after 6 months of real query data.

---

## 🔁 Solution Pattern — How It Gets Solved

### The Solution: RoboAdvisor

> A multilingual, RAG-powered conversational assistant that gives industrial robot technicians **immediate, step-by-step guidance** grounded in official manuals, safety protocols, and parts catalogs — on any device, in any of 5 languages, at any time.

### Why an LLM (not rule-based AI or traditional search)?

| Approach | Problem |
|----------|---------|
| **Keyword search (current)** | Requires the technician to know the right search terms; English only; no synthesis |
| **Rule-based AI** | Can't handle the combinatorial complexity of 15 robot models × 45,000 parts × multiple fault types × multiple languages |
| **LLM without RAG** | Hallucinates part numbers and procedures not in official documentation — dangerous |
| **LLM + RAG (chosen)** | Grounds answers in verified documents, handles natural language in any language, adapts to each query's specific context |

### The Pattern in 6 Steps

```
TECHNICIAN QUERY
      │
      ▼
┌─────────────────────┐
│   1. USER INTERFACE │  Mobile/desktop app in technician's language
│      (PWA)          │  Offline cache for top 500 procedures
└─────────┬───────────┘
          │  (TLS 1.3 encrypted)
          ▼
┌─────────────────────┐
│  2. QUERY PROCESSOR │  Detects language → translates to English
│                     │  Classifies intent → routes to correct corpus
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐     ┌──────────────────────────┐
│  3. VECTOR DATABASE │────▶│   KNOWLEDGE BASE          │
│  Similarity search  │     │   7,500 articles          │
│  Returns Top K=3    │     │   45,000 parts catalog    │
│  document chunks    │     │   15 robot model manuals  │
└─────────┬───────────┘     │   Safety & regulatory docs│
          │                 └──────────────────────────┘
          ▼
┌─────────────────────┐
│  4. PRIVATE CLOUD   │  LLM receives: query + Top 3 retrieved chunks
│     LLM             │  Generates response grounded only in those chunks
│  (Temperature 0.1)  │  Cites source document + section for every claim
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  5. SAFETY LAYER    │  Checks: does response cite a source?
│                     │  Blocks: any response without verified citation
│                     │  Escalates: when system signals uncertainty
└─────────┬───────────┘
          │  (TLS 1.3 encrypted)
          ▼
┌─────────────────────┐
│  6. RESPONSE TO     │  Delivered in technician's language
│     TECHNICIAN      │  Step-by-step with safety warnings first
└─────────────────────┘
```

### Why This Pattern Solves Each Pain Point

| Original Pain Point | How RoboAdvisor Fixes It |
|---------------------|--------------------------|
| 15–30 min per query | Conversational Q&A returns answer in <2 min |
| English-only KB | Query Processor translates; LLM responds in technician's language |
| Senior staff bottleneck | 70%+ of queries resolved without human escalation |
| No safety guardrails | Safety Layer enforces citation; system prompts warn about hazards first |
| No mobile experience | Mobile-first PWA with offline cache for core procedures |
| Hallucinated part numbers | Parts catalog is structured data; LLM retrieves, never generates |

---

## 🏗️ System Architecture Deep Dive

### Components

| Component | What It Is | Why It's Here |
|-----------|-----------|---------------|
| **User Interface (PWA)** | Mobile-first Progressive Web App | Technicians are 95% mobile; PWA enables offline cache for no-connectivity scenarios |
| **Query Processor** | NLP service for language detection, intent classification, routing | Ensures multilingual queries reach the right document corpus and are handled consistently |
| **Vector Database** | Embedding store with similarity search | Finds semantically relevant documents even when query wording differs from source text |
| **Knowledge Base** | 7,500 articles, 45,000 parts, 15 model manuals, safety docs | Single source of truth; version-controlled; weekly ingestion pipeline keeps it current |
| **Private Cloud LLM** | Dedicated LLM server inside company VPC | Proprietary fault data never leaves company network; dedicated server = no shared infrastructure risk |
| **Safety Layer** | Citation enforcement + rules engine | Every response must cite a source before reaching technician; blocks or escalates otherwise |

### What Must Be Encrypted and Why

```
User Interface ──[TLS 1.3]──▶ Query Processor   (query contains location, machine ID, fault codes)
Safety Layer   ──[TLS 1.3]──▶ User Interface    (response contains proprietary procedure steps)
All internal services run inside company VPC — no public internet exposure
Vector DB and Knowledge Base: encrypted at rest (AES-256)
```

Encryption is non-negotiable because technician queries contain **proprietary machine configurations, fault histories, and operational data** that would be valuable to competitors and must meet industrial data protection regulations.

---

## ⚙️ LLM Configuration

| Property | Value | Rationale |
|----------|-------|-----------|
| **License type** | Proprietary (e.g., Azure OpenAI GPT-4o) | Enterprise SLA + EU AI Act compliance. Open-source ruled out: industrial liability requires audited model provenance and vendor accountability |
| **Deployment model** | Dedicated private-cloud server (company VPC) | Queries contain proprietary fault data. Dedicated server = data never reaches shared inference infrastructure |
| **Temperature** | `0.1` | Near-deterministic output. Safety-critical instructions must not vary across identical queries. Prevents "creative" but incorrect answers |
| **Top K** | `3 document chunks` | Returns only the 3 most relevant procedure chunks. Fewer = lower hallucination risk; more chunks risk introducing contradictory guidance |
| **Max output tokens** | `512` | Sufficient for step-by-step instructions. Prevents verbose responses that overwhelm technicians on mobile screens |
| **Context window** | `16k tokens` | Accommodates full retrieval context (3 × ~400-token chunks) plus conversation history for multi-turn diagnostics |
| **Fine-tuning** | Optional — Phase 2 | Initial RAG handles knowledge via retrieval. Fine-tuning planned after 6 months of real query data to improve tone/domain phrasing |
| **System prompt persona** | Safety-first advisor | Always cite source, refuse speculation, escalate when uncertain, never generate part numbers from memory, respond in user's language |

---

## 📊 Success Metrics

Metrics span four dimensions — outcome, safety, adoption, and performance — so the team can distinguish between "the tool is well-built" and "technicians are actually using it."

| Metric | Target | Type | What It Tells Us |
|--------|--------|------|-----------------|
| Average issue resolution time | < 2 min | Outcome | Core value prop: speed vs. 15–30 min baseline |
| Escalation-free resolution rate | > 70% | Outcome | Whether the advisor is actually handling queries end-to-end |
| User satisfaction score | > 4.2 / 5 | Outcome | Perceived helpfulness and trust from technicians |
| Safety incidents from AI guidance | 0 | Safety | Hard line — any incident triggers immediate review |
| **Weekly active technicians** | > 80% of eligible users | **Adoption** | Are technicians actually using the tool or reverting to old methods? |
| **Median response latency** | < 3 seconds | **Performance** | Slow responses = technicians stop waiting and go elsewhere |

---

## 📄 Output

The full product plan — including the system architecture diagram, LLM configuration table, sample prompts, and reference data plan — is presented in the slide deck below:

> 📎 **[RoboAdvisor_Product_Plan.pdf](./RoboAdvisor_Product_Plan.pdf)**

The deck covers:
- Slides 1–2: Title + Product Definition section divider
- Slides 3–4: Customer profile and problem breakdown
- Slide 5: Solution overview (RoboAdvisor)
- Slide 6: Risks and mitigations
- Slides 7–8: System Details section + System Attributes
- Slide 9: System Architecture diagram (RAG pipeline)
- Slide 10: LLM Configuration table
- Slides 11–12: Measurement section + Success Metrics
- Slide 13: Sample system prompt and user prompt
- Slide 14: Reference data plan
- Slide 15: Closing — the opportunity

---

*Built as part of Udacity's AI Product Management Nanodegree, Project 3: Innovative Business Solutions with LLMs.*
