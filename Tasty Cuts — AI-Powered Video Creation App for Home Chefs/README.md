# 🍳 Tasty Cuts — AI-Powered Video Creation App for Home Chefs

---

## 📌 Problem Statement

**TastyTube** is a massively popular short-form video platform for home chefs. While millions of viewers consume cooking content daily, a critical bottleneck exists on the **creator side**.

A dedicated group of technical content creators has produced a few thousand high-quality cooking videos — but they represent only a fraction of the potential creator base. The barrier isn't passion or skill in the kitchen. It's the **complexity and time cost of video production itself**:

- Splitting raw footage into logical, step-by-step recipe clips is done **entirely by hand**
- Adding captions requires manual transcription or third-party tools
- Writing a structured recipe summary from a video is a separate, tedious task
- Publishing to multiple platforms involves repetitive formatting and metadata entry

The result: most home chefs who *could* create great content simply **don't**, because the process demands too much technical effort. This directly limits the volume of new videos available to viewers, which in turn suppresses overall platform engagement.

> **The core problem:** The effort required to turn a raw cooking video into a polished, captioned, published recipe video is too high for the average home chef.

---

## ✅ What Needs to Be Done

To solve this, we need to ship **Tasty Cuts** — a mobile app that uses AI to dramatically reduce the time and effort of creating cooking videos. The work spans four domains:

### 1. Data Foundation
Before any AI model can be trained, we need high-quality labeled training data. This means:
- Curating a dataset of 1,000 suitable TastyTube videos (single recipe, single camera, portrait, US English)
- Assembling and instructing a human annotation team
- Manually splitting those videos into individual recipe-step clips
- Labeling each clip with cooking equipment, ingredients, quantities, and techniques

### 2. Machine Learning Models
Two core ML models must be trained and deployed:
- **Video Clipping Model** — automatically splits a raw cooking video into individual step-by-step clips
- **Recipe Step Classifier** — identifies *what* cooking step is happening in each clip (e.g., chopping, sautéing, plating)

### 3. The Mobile App
A user-facing mobile app must be built that:
- Presents AI-generated clips for the creator to review and reorder
- Allows editing of clip text, video title, description, and recipe summary
- Enables one-tap publishing to TastyTube

### 4. Validation
Product decisions must be grounded in real user behavior:
- Conduct usability testing on the MVP before launch to catch blockers

> **Note on exploratory research:** User pain points are already well-defined from the existing creator base. Conducting a separate exploratory research phase before building would add delay without meaningfully changing the MVP scope. Usability testing (included in the MVP) covers the validation needs more efficiently.

---

## 🧩 Solution — Defining the Pieces

### The RICE Prioritization Framework

To decide *what to build first*, every feature was scored using the **RICE method**:

| Dimension | What it measures | Scale |
|---|---|---|
| **Reach** | How many users will this feature affect? | 1 (few) → 5 (all) |
| **Impact** | How meaningfully does it move the needle? | 1 (minimal) → 5 (transformative) |
| **Confidence** | How certain are we it will deliver value? | 1 (speculative) → 5 (proven) |
| **Effort** | How much resource does it consume? | 1 (quick) → 5 (massive) |

**RICE Score = (Reach × Impact × Confidence) / Effort**

Higher scores = more value per unit of effort. Features are sorted by score, and only **9 of 18 features** (exactly half) are included in the MVP — the minimum set without which the product cannot deliver its core value.

---

### The Pattern: How It All Connects

The solution follows a clear dependency chain. Each phase unlocks the next:

```
Phase 1 — Data Foundation
│
├── Specify dataset               (defines what we train on)
├── Select & instruct labeling team
│
▼
Phase 2 — Data Preparation
│
├── Split videos into clips       (creates training examples)
└── Apply labels to clips         (gives models ground truth)
│
▼
Phase 3 — ML Model Training
│
├── Build ML model: Video Clipping
└── Build ML model: Step Classification
│
▼
Phase 4 — App & UX
│
├── Create app to edit clips and text
└── Conduct usability testing
│
▼
Phase 5 — Distribution
│
└── Publish to TastyTube          (closes the creator loop)
```

**The pattern is sequential by nature.** You cannot train a model without labeled data. You cannot label data without splitting clips. You cannot test an app that hasn't been built. Each foundational layer must be solid before the next can succeed — which is why data tasks are prioritized early, even though they produce no user-visible output on their own.

---

### MVP Features (9 of 18)

These are the features without which the app cannot deliver its core value. The MVP is capped at 9 features — exactly half of the 18 total — following the "ruthless" prioritization principle:

| # | Feature | RICE Score | Rationale |
|---|---|---|---|
| 1 | Specify dataset | 62.5 | Nothing works without training data |
| 2 | Select & instruct labeling team | 62.5 | Prerequisite to all data prep |
| 3 | Publish to TastyTube | 62.5 | Core distribution — closes the creator loop |
| 4 | Conduct usability testing | 50.0 | Required to ship confidently |
| 5 | Prepare data: Split videos into clips | 41.7 | Training data for video clipping model |
| 6 | Prepare data: Apply labels | 41.7 | Ground truth for both ML models |
| 7 | Create app to edit clips and text | 31.3 | The product itself |
| 8 | Build ML model: Video clipping | 25.0 | Core AI differentiator |
| 9 | Build ML model: Step classification | 25.0 | Required for step-level UX |

### Post-MVP Features (Deferred)

These are valuable but not essential for launch. They extend the product once the core loop is validated:

- **Conduct exploratory user research** — user pain points are already defined from the existing creator community; usability testing covers validation needs more efficiently
- **Generate video captions** — creators can caption manually; use third-party STT (speech-to-text) models post-MVP
- **Generate video title, description & recipe summary** — can be achieved with a third-party LLM on caption text post-MVP
- **Publish to YouVideo, TikTak, Fastgram** — multi-platform support after TastyTube is proven
- **Translate into other languages** — US English focus is sufficient; localization is a V2 growth feature
- **Create Recipe Library** — marketing asset for a polished post-MVP product
- **Build Extensions API** — premature before product-market fit

---

### MVP Roadmap — Sprint Plan

| Seq | Feature | Sprints | Team | Depends On |
|---|---|---|---|---|
| 1 | Specify dataset | 1 | Data Science team | — |
| 2 | Select & instruct labeling team | 1 | Product team, Data Science team | Specify dataset |
| 3 | Conduct usability testing (planning) | 1 | Product team, QA team | — (parallel) |
| 4 | Prepare data: Split clips | 3 | Data Engineering team | Labeling team |
| 5 | Prepare data: Apply labels | 4 | Data Science team, Data Engineering team | Split clips |
| 6 | Build ML model: Video clipping | 4 | ML/AI Engineering team, Data Science team | Apply labels |
| 7 | Build ML model: Step classification | 4 | ML/AI Engineering team, Data Science team | Apply labels |
| 8 | Create app to edit clips and text | 5 | Engineering team, Design team | Both ML models |
| 9 | Conduct usability testing (execution) | 1 | Product team, QA team | App built |
| 10 | Publish to TastyTube | 2 | Engineering team, Product team | App built |

**Total: ~25 sprints** across 9 MVP features, structured so no team is blocked waiting on another.

---

## 📎 Output

The full prioritization table and MVP roadmap are available in the attached PDF below.

> `Tasty_Cuts_Report.pdf`
