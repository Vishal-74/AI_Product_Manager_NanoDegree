# 🎁 AI-Enabled Gift Recommendation System
### Product Requirements Document — MVP

> *A fast-fashion retail platform's strategic initiative to unlock gifting as a new growth channel, powered by AI.*

---

## Table of Contents

1. [Problem Statement](#1-problem-statement)
2. [What Needs to Be Done](#2-what-needs-to-be-done)
3. [Solution Architecture](#3-solution-architecture)
   - [How the System Thinks](#31-how-the-system-thinks)
   - [The Pattern: Observe → Infer → Recommend → Learn](#32-the-pattern-observe--infer--recommend--learn)
   - [AI Models Defined](#33-ai-models-defined)
   - [Data Inputs Defined](#34-data-inputs-defined)
   - [Success Metrics Defined](#35-success-metrics-defined)
   - [User Stories Defined](#36-user-stories-defined)
4. [Output / Deliverable](#4-output--deliverable)

---

## 1. Problem Statement

### Background

A fast-fashion retail company has seen **10x revenue growth** over three years, fueled by a highly engaging mobile app where U.S. teenagers browse, shop, and socialize — essentially a digital mall. The app already has:

- A large, loyal user base (NPS of **75**, higher than Amazon)
- Strong repeat purchase behavior (**35% of users made 2+ purchases in 12 months**)
- A built-in **social graph** (each user connected to ~2–3 friends/family)
- Stored payment info and addresses → **low commerce friction**
- Rich historical data on browsing and purchasing behavior

### The Problem

**Growth is stalling.** The core U.S. teenage self-purchase market is approaching saturation. The company explored several expansion routes:

| Option | Why Rejected |
|---|---|
| Sell to older customers | Requires retraining algorithms + risks brand dilution |
| Expand product categories | Expensive buying team expansion + inventory costs |
| Create store-branded products | Years-long effort, enormous upfront capital |
| International expansion | Legal, logistical, and localization complexity |

All of these cost too much, take too long, or introduce too much risk.

### The Gap

Despite having a **social platform built for sharing and discovery**, the app has never explicitly supported **buying for others**. Every purchase flow is designed for the buyer themselves. There is no gifting infrastructure, no gift recommendation engine, and no way for the AI to distinguish "I'm buying this for me" from "I'm buying this for a friend."

This means the company is leaving a natural, high-frequency revenue opportunity completely untapped — especially during **holiday seasons**, **birthdays**, and other gifting occasions that its teenage users already celebrate.

---

## 2. What Needs to Be Done

To launch the MVP within **6 months** (before the holiday season), the following must be built:

### 2.1 Data Foundation
- [ ] Add a **checkout question** to tag every order as a gift or self-purchase
- [ ] Collect **gift occasion** (birthday, holiday, just because) at checkout
- [ ] Enable **recipient selection** from a user's in-app social connections
- [ ] Collect **birthdates** (or horoscope signs as a fun proxy) from users for their connections
- [ ] Build a **data pipeline** that feeds labeled gift order data into the AI training loop

### 2.2 AI Recommendation System
- [ ] Train a **Hybrid Recommendation Model** that combines:
  - Collaborative filtering (what do people like this user gift?)
  - Content-based filtering (what does the recipient like?)
- [ ] Surface recommendations **inside the app** on a dedicated "Gift Ideas" screen
- [ ] Surface recommendations **via triggered email** (e.g., 7 days before a friend's birthday)

### 2.3 Gift Checkout Flow
- [ ] Add a **"Gift This Item"** button on every product detail page
- [ ] Build a gift checkout flow: select recipient → add message → confirm address → purchase
- [ ] Flag all gift orders in the order management system

### 2.4 Forecasting & Analytics
- [ ] Train a **Time Series Forecasting Model** to predict gift order volume and GMV by week
- [ ] Build a **web-based internal dashboard** with actuals vs. forecasts and key KPI charts
- [ ] Set up a **weekly automated stakeholder email** with performance summary

### 2.5 Stakeholder Alignment
- [ ] Engineering Lead: estimate effort, identify technical dependencies
- [ ] Data Scientist: define training schema, labeling standards, retraining cadence
- [ ] Design Lead: design the Gift Ideas surface, gift checkout flow, email templates, dashboard UI
- [ ] AI Product Manager (this PRD): own requirements, track KPIs, drive iterations

---

## 3. Solution Architecture

### 3.1 How the System Thinks

The gift recommendation system works by answering one core question:

> **"Given who this shopper is, who they want to buy for, and what occasion it is — what product should we recommend?"**

To answer that, the AI pulls together three types of knowledge:

```
WHO IS THE SHOPPER?          WHO IS THE RECIPIENT?         WHAT'S THE OCCASION?
─────────────────────        ──────────────────────        ─────────────────────
Their purchase history        Their in-app interests         Birthday?
Their gifting history         Their social activity          Holiday?
Their style preferences       Their demographics             Just because?
Their social connections      Their wish signals             Recurring event?
         │                           │                              │
         └───────────────────────────┼──────────────────────────────┘
                                     ▼
                      HYBRID RECOMMENDATION ENGINE
                      ┌──────────────────────────────┐
                      │  Collaborative Filtering     │  ← "People like you gifted..."
                      │  Content-Based Filtering     │  ← "They tend to like..."
                      │  Occasion-Aware Ranking      │  ← "For birthdays, people choose..."
                      └──────────────────────────────┘
                                     │
                      ┌──────────────┼──────────────┐
                      ▼              ▼               ▼
                  In-App         Birthday        Stakeholder
               Gift Ideas        Email           Dashboard
                  Screen         Trigger        (Forecasts)
```

---

### 3.2 The Pattern: Observe → Infer → Recommend → Learn

Every successful recommendation system follows a cycle. Here is how this system implements it:

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   OBSERVE          INFER            RECOMMEND        LEARN      │
│                                                                 │
│   User buys    →  "She likes    →  "For her      →  Did they   │
│   hoodies for     streetwear"      friend, show     buy it?    │
│   herself                          these 5 items"   Click?     │
│                                                                 │
│   User connects → "His friend   →  "7 days before →  Did they  │
│   with a friend   turns 17 in      his birthday,    open the   │
│   in-app          30 days"         send an email"   email?     │
│                                                                 │
│   Gift order   →  "Holiday      →  Forecast: 3x  →  Was the   │
│   data spikes     season peak      spike coming      forecast  │
│   in Dec          incoming"        in 4 weeks"       accurate? │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                                                    │
                                          Feedback loops back
                                          into model retraining
```

The system gets smarter with every interaction. Every click, every purchase, every skipped recommendation, and every gift occasion tag is signal that improves the next prediction.

---

### 3.3 AI Models Defined

#### Model 1: Hybrid Recommendation System

**What it is:** A recommendation engine that combines two filtering approaches into one model.

| Sub-Model | What It Means | Example |
|---|---|---|
| **Collaborative Filtering** | "People like you also did X" | Shoppers who bought similar items for themselves also gifted brand Y hoodies for birthdays |
| **Content-Based Filtering** | "Based on what the recipient likes" | Recipient browses Y2K aesthetic content → recommend Y2K accessories |
| **Hybrid** | Both combined, weighted by data availability | New recipients with little data → lean content-based; established users → lean collaborative |

**Why Hybrid?** A pure collaborative filter fails when a recipient is new to the platform (cold-start problem). A pure content filter ignores the wisdom of the crowd. Hybrid handles both gracefully.

**Where it's used:** In-app Gift Ideas surface + birthday email campaigns.

---

#### Model 2: Time Series Forecasting (Prophet / ARIMA)

**What it is:** A statistical model that looks at past patterns over time and predicts future values.

| Term | What It Means |
|---|---|
| **Time Series** | Data measured at regular intervals over time (e.g., weekly gift orders) |
| **Autoregressive** | The model uses its own past values to predict future ones (history predicts future) |
| **Exogenous Input** | External factors fed into the model that it can't observe on its own (e.g., upcoming holidays) |
| **Seasonality** | Repeating patterns at fixed intervals (gifting always spikes at Christmas, Valentine's Day) |
| **Confidence Interval** | A range around the forecast showing how uncertain the prediction is |

**Why Prophet/ARIMA?** Both are interpretable (stakeholders can understand *why* a spike is predicted), handle seasonality naturally, and are proven in retail demand forecasting.

**Where it's used:** Internal stakeholder dashboard + weekly automated email.

---

### 3.4 Data Inputs Defined

Every data source used by the AI is defined below, with its type and role.

#### What "Autoregressive" vs. "Exogenous" means

| Term | Plain English | Example |
|---|---|---|
| **Autoregressive** | The model's own history; past values predict future values | Last December's gift order volume helps predict this December's |
| **Exogenous** | Outside information injected into the model as additional context | "Christmas is in 10 days" is not something the model observes — it must be told |

---

#### Data for the Recommendation Model (all Exogenous)

| Data Source | Where It Comes From | Why the Model Needs It |
|---|---|---|
| Shopper's own purchase history | App order history | Reveals personal style and brand preferences |
| Past gift purchases by the shopper | App order history + gift flag | Shows what categories and price ranges they gift at |
| Most-gifted products (platform-wide) | Aggregated order data | Provides crowd-sourced signals; solves cold-start for new recipients |
| Recipient's in-app social activity | Social graph + posts | Infers the recipient's tastes and aesthetic preferences |
| Gift occasion (birthday, holiday, etc.) | Checkout question | Enables occasion-specific ranking and filtering |
| Recipient's birthdate / horoscope sign | User profile (asked during onboarding) | Enables proactive birthday reminders; horoscope is a fun, low-friction proxy |
| Recipient's location (zip code) | Billing address | Surfaces hyperlocally trending products |
| Personal interests (hobbies, media) | Inferred from browsing + social | Feeds content-based filtering with richer taste signals |

---

#### Data for the Forecasting Model

| Data Source | Type | Why the Model Needs It |
|---|---|---|
| Historical gift order volume (weekly) | **Autoregressive** | The model's core signal; learns seasonal patterns from its own history |
| Historical GMV by order type | **Autoregressive** | Enables revenue-level forecasting, not just volume |
| U.S. holiday calendar | **Exogenous** | Explains and predicts the biggest demand spikes |
| Email / push campaign dates | **Exogenous** | Separates organic gifting demand from marketing-driven lifts |
| Aggregated birthday data (monthly) | **Exogenous** | Creates predictable micro-peaks as birthday reminders fire at scale |
| New social connection rate (weekly) | **Exogenous** | Growing social graph = more gifting occasions = rising baseline demand |

---

### 3.5 Success Metrics Defined

#### What is a KPI?
A **Key Performance Indicator (KPI)** is a high-level category of business performance. Metrics are the specific, measurable numbers that tell us how that KPI is doing.

---

#### KPI 1: Gifting Frequency
*Are people actually using the gifting feature, and are they coming back to do it again?*

| Metric | What It Measures |
|---|---|
| **Gift Order Rate** | % of all orders flagged as gifts — tells us baseline adoption |
| **Repeat Gifting Rate** | % of gifting users who gift again within 90 days — tells us habit formation |
| **Recommendation CTR** | % of recommendation impressions clicked — tells us if suggestions feel relevant |
| **Recommendation Conversion Rate** | % of clicks that become purchases — tells us if the full funnel is working |

---

#### KPI 2: Gifting Value
*Is gifting generating real revenue, and is it incremental (new money) or just shifting existing spend?*

| Metric | What It Measures |
|---|---|
| **Average Gift Order Value (AGOV)** | Mean order value for gift purchases — gifts often have higher basket sizes |
| **Gift Revenue as % of Total Revenue** | How much of GMV is now driven by gifting |
| **Incremental Revenue per Gifting User** | Additional spend by gifters vs. matched non-gifters — isolates true incrementality |

---

#### KPI 3: Overall Marketplace Value
*Is the gifting feature making the whole platform healthier and bigger?*

| Metric | What It Measures |
|---|---|
| **Total GMV Growth Rate (YoY)** | Year-over-year growth during the holiday period with gifting active |
| **New Gift-Driven Customer Acquisitions** | First-time buyers who entered the platform by *receiving* a gift |
| **Customer Retention Rate (Gifters vs. Non-Gifters)** | Whether gifters are more loyal — validates gifting as a retention driver |

---

### 3.6 User Stories Defined

#### What is a User Story?
A **User Story** is a one-sentence description of a feature from the perspective of the person who benefits from it. Format:

> *"As a **[who]**, I want **[what]** so that **[why/outcome]**."*

User stories keep the team focused on *who benefits* and *why*, rather than jumping straight to technical implementation. They are the bridge between business goals and engineering tasks.

---

| # | User Story | Feature Area |
|---|---|---|
| 1 | As a **teenage shopper**, I want to see AI gift recommendations for my friends near their birthday so that I can find a great gift without leaving the app. | In-App Recommendations |
| 2 | As a **shopper who found a gift**, I want to add a message and ship it to my friend in one checkout flow so that I can complete a gift purchase in under 2 minutes. | Gift Checkout Flow |
| 3 | As a **shopper with connected friends**, I want a personalized birthday reminder email 7 days before my friend's birthday so that I never miss an important moment. | Email Trigger |
| 4 | As a **Product Manager**, I want a web dashboard with forecasted vs. actual gift GMV and KPI trends so that I can make data-driven decisions about campaigns and features. | Internal Dashboard |
| 5 | As a **senior business stakeholder**, I want a weekly Monday email with gifting performance vs. forecast so that I can stay informed without logging into a dashboard. | Stakeholder Email |
| 6 | As a **Data Scientist**, I want every checkout to ask "Is this a gift?" so that the model has clean, labeled training data to improve accuracy over time. | Data Foundation |

---

## 4. Output / Deliverable

The full Product Requirements Document (PRD) is attached below. It includes all sections above in a formatted Word document ready for stakeholder review:

> 📄 **`PRD_Gift_Recommendation_MVP.docx`**

The PRD covers:
- Executive summary (Context, MVP Goal, Target Segment, Timeframe)
- Success Metrics with tables (KPIs 1–3)
- AI Model selection and rationale
- Full data source tables with type labels and enhancement explanations
- 6 User Stories with acceptance criteria
- Stakeholder responsibility matrix

---

*Document authored as part of the AI Product Management Nanodegree — Course 1 Project.*
*PRD Version 1.0 | April 2026*
