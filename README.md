<div align="center">

# F3Next

**The Autonomous ERP for the Factory Floor**

*Upload your messy files. Get a live, structured ERP. No consultants. No months of configuration.*

[![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![Next.js](https://img.shields.io/badge/Next.js-15-000000?style=flat-square&logo=next.js&logoColor=white)](https://nextjs.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Temporal](https://img.shields.io/badge/Temporal-Orchestration-6E44FF?style=flat-square)](https://temporal.io)
[![ERPNext](https://img.shields.io/badge/ERPNext-v15-0089FF?style=flat-square)](https://erpnext.com)

</div>

---

## The Problem

Factories — from small manufacturers to mid-size industrial businesses — run on WhatsApp threads and Excel files. Not because the owners are careless, but because every ERP on the market requires months of setup, expensive consultants, and a dedicated IT team to survive the implementation.

**The failure rate is around 70%.** Companies spend the money. The project drags. The consultant leaves. The ERP sits unused.

The core problem is not the software. It is that the business has no clean data layer to connect to. Someone has to manually clean, classify, map, and validate years of operational data before a single record can enter the system. That someone has always been a human consultant.

**F3Next automates that human.**

---

## What F3Next Does

A business uploads their raw files — vendor lists, inventory sheets, purchase records, BOMs, anything. F3Next does the rest:

1. **Understands the business** — classifies the data, infers the industry, builds a business profile automatically.
2. **Maps the chaos** — classifies every file to the correct ERP document type, resolves foreign keys, injects schema-level defaults, and validates the entire payload before a single write.
3. **Deploys safely** — runs a dry-run preflight that predicts risk tiers and requires explicit approval before any live database write.
4. **Stays live** — once deployed, users run the ERP via natural language. Ask the system to create a sales order, check stock levels, or pull a purchase report in plain English.

---

## System Architecture

F3Next is built as a fleet of autonomous AI agents orchestrated by Temporal, all connected to a shared infrastructure layer.

```
┌─────────────────────────────────────────────────────────────┐
│                        USER BROWSER                         │
│                    (Next.js 15 Frontend)                     │
└──────────────────────────┬──────────────────────────────────┘
                           │ HTTPS
                    ┌──────▼──────┐
                    │    Caddy    │  Reverse Proxy / SSL
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   FastAPI   │  API Gateway
                    └──────┬──────┘
                           │ Workflow signals
                    ┌──────▼──────┐
                    │   Temporal  │  Durable Orchestration Engine
                    └──────┬──────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
┌───────▼───────┐  ┌───────▼───────┐  ┌──────▼────────┐
│  Discovery    │  │Implementation │  │   OS Agent    │
│  Agent (Adi)  │  │    System     │  │  (Ops Brain)  │
│  Business     │  │  14-Step ERP  │  │  NL → ERP     │
│  Profiling    │  │  Pipeline     │  │  Commands     │
└───────────────┘  └───────────────┘  └───────────────┘
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐       ┌─────▼─────┐     ┌─────▼─────┐
   │Supabase │       │    R2     │     │   Redis   │
   │ pgvector│       │  Storage  │     │   Cache   │
   └─────────┘       └───────────┘     └───────────┘
```

### Core Moat: Company Brain Runtime

Most “AI ERP” products treat the LLM as the source of truth. We treat **live ERP state** as the final authority, enforcing an absolute golden rule: *Context may suggest; Live Registries decide.*

*   **Live Entity Registry (LER):** Every single entity link (Item, Supplier, Customer) is checked in real-time against the live Frappe site, completely bypassing stale caches.
*   **Temporal-First Orchestration:** Every data operation runs inside an isolated Temporal workflow. Activities retry automatically, surviving server crashes. A partial failure mid-import never corrupts the customer’s production database.
*   **Honesty Contracts:** Imports and commands return absolute pass/partial/fail logs. The system halts and requests human-in-the-loop signals via Temporal input channels before committing high-risk records.
*   **Provider-Agnostic LLM Layer:** Abstracted routing across Gemini Flash and GPT-4o-mini with schema locking via Pydantic to ensure zero JSON parse errors.


### Agent Fleet

| Agent | Role |
|---|---|
| **Discovery Agent** | AI consultant "Adi" — profiles the business through a structured conversation, outputs an industry + ERP fit recommendation |
| **Implementation System** | 14-step pipeline: ingest → classify → map → validate → dry-run preflight → phased import (Config → Master Data → Transactions) |
| **OS Agent** | Translates natural language commands into ERPNext API calls with a 9-step execution pipeline and confirmation gates for high-risk operations |
| **Context Versioning Service** | Listens to ERPNext webhooks and rebuilds live company context snapshots so the OS Agent always operates on fresh data |

---

## Key Engineering Decisions

**Temporal-first orchestration.** Every multi-step business process runs inside a Temporal workflow. Activities retry automatically. Workflows survive server crashes. A failed import mid-way does not corrupt the customer's database.

**Safety preflight before every live write.** The Implementation System runs a full dry-run before touching the ERP. It classifies every record by risk tier (Low / Medium / High) and requires explicit user approval before any medium or high-risk transaction is imported.

**Human-in-the-loop signals.** Users inject decisions — confirmations, clarifications, field corrections — into running workflows via Temporal signals. The system waits. The user approves. The workflow continues.

**3-tier context engine.** The OS Agent assembles context from three layers: static ERP schema (Tier 1), industry-specific templates (Tier 2), and live data snapshots from the customer's ERPNext instance (Tier 3). No hallucinated row counts. No stale context.

**Provider-agnostic LLM layer.** A single router abstracts Gemini Flash and GPT-4o-mini with automatic key rotation, quota tracking, and cross-provider fallback. Structured outputs are enforced via Pydantic schema locking — no JSON parse failures.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 15, React, TailwindCSS |
| API Gateway | FastAPI (Python 3.11) |
| Orchestration | Temporal.io |
| AI Agents | LangChain + Gemini Flash + GPT-4o-mini |
| Knowledge Base | pgvector (PostgreSQL) — APQC + ERPNext domain knowledge |
| Database | Supabase (PostgreSQL 15) |
| File Storage | Cloudflare R2 |
| Cache | Redis 7 |
| Reverse Proxy | Caddy v2 |
| Observability | Prometheus + Grafana |
| ERP Target | ERPNext v15 (Frappe Framework) |

---

## Status

F3Next is in **active beta** with manufacturing and industrial MSME customers. The full onboarding loop — upload → validate → deploy → operate — is functional end-to-end.

*   **Development Phase:** Core end-to-end loops (upload $\rightarrow$ map $\rightarrow$ validate $\rightarrow$ deploy $\rightarrow$ operate) are fully complete and functional.
*    **Enterprise Flight:** We are actively training and stress-testing our **v0.8 platform in production within a live corporate pilot deployment**.

*Note: The main application engine is kept in a private repository to safeguard active client configurations and enterprise infrastructure logic.*

---

## 🏗️ Get in Touch

We are actively building the future of industrial automation. If you are working in manufacturing, supply chain, enterprise infrastructure, or just want to talk system architecture, let’s connect.

* **Founder Email:** [parth.kulkarni2412@gmail.com](mailto:parth.kulkarni2412@gmail.com)
* **Platform Gateway:** [f3next.in](https://f3next.in)
  
---

<div align="center">
<sub>Built with unreasonable conviction.</sub>
</div>
