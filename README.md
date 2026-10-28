# AI Architecture Portfolio

A hands-on engineering and architecture portfolio documenting systems design, architectural governance, trade-off evaluations, and decision frameworks for production-grade AI and agentic systems.

Maintained by **Aya Aboulwafa**.

---

## Repository Structure & Navigation

This repository is organized into dedicated architectural domains:

```text
ai-architecture-portfolio/
├── docs/
│   ├── governance-pack-wiki.md      # Consolidated Governance Wiki Page (Session 1 Deliverable)
│   ├── architecture-principles.md   # TOGAF-aligned Architecture Principles mapped to Business Goals
│   └── arb-checklist.md             # Architecture Review Board (ARB) Production Gate Checklist
├── adr/
│   ├── 0001-cloud-llm-api-selection.md           # LLM Serving Strategy: Cloud API with PII Masking
│   ├── 0002-vector-database-pgvector.md          # Vector DB: Unified PostgreSQL with 1M Scale Trigger
│   └── 0003-hybrid-realtime-batch-processing.md  # Serving: Real-Time NBA + Batch Feature Store
├── diagrams/
│   ├── marketing-automation-architecture.drawio  # Editable Draw.io System Model
│   └── marketing-automation-architecture.png     # Rendered High-Resolution System Diagram
├── study/
│   ├── session-1-notes.md           # Session 1: Theoretical Foundations, Frameworks (TOGAF, C4, Zachman)
│   └── session-2-notes.md           # Session 2: Viewpoints, Event-Driven Data Platform (CDC, Kafka, CDP) & Production AI Stack
├── assets/
│   ├── session-1/                   # Session 1 diagrams and architectural reference visuals
│   └── session-2/                   # Session 2 diagrams (Connections Table, Building Blocks, To-Be Architecture)
├── index.html                       # Interactive RTL Web Presentation — All Sessions (GitHub Pages)
└── README.md                        # Portfolio documentation and repository roadmap
```

---

## Featured Case Study: Marketing Automation with AI & Agents

The foundational project in this portfolio models an **AI-Powered Marketing Automation Platform** for high-volume e-commerce.

### Business Goals & Target Metrics
* **Sales Take-Rate:** Increase conversion and sales take-rate by **+80%** during promotional peak seasons.
* **Customer Satisfaction (CSAT):** Elevate CSAT to **≥ 70%** via non-intrusive, privacy-compliant personalization.
* **Operational Velocity:** Automate multi-channel campaign drafting and evaluation through collaborative AI Agents.
* **Regulatory Compliance:** Strict adherence to the Saudi Personal Data Protection Law (**PDPL**).

### Core Architectural Deliverables

1. **Governance Pack Wiki Page:**  
   A two-page executive summary synthesizing principles, decisions, review gates, and component flows:  
   [docs/governance-pack-wiki.md](docs/governance-pack-wiki.md)

2. **System Architecture Diagram:**  
   Visual model covering edge security, real-time recommendation engines, agentic campaign orchestration, and human oversight:  
   [diagrams/marketing-automation-architecture.drawio](diagrams/marketing-automation-architecture.drawio) (rendered in [diagrams/marketing-automation-architecture.png](diagrams/marketing-automation-architecture.png))

3. **Architecture Principles (TOGAF):**  
   Five binding principles (AP-01 to AP-05) governing tenant isolation, defense-in-depth, data privacy, elastic scaling, and decoupled zero-trust APIs:  
   [docs/architecture-principles.md](docs/architecture-principles.md)

4. **Architecture Decision Records (ADRs):**  
   Rigorous decision records documenting evaluated options, decisions, and trade-off mitigations:
   * [ADR-0001: Commercial Cloud LLM APIs with In-Memory PII Masking](adr/0001-cloud-llm-api-selection.md)
   * [ADR-0002: PostgreSQL pgvector Unified Datastore with 1M Scale Trigger](adr/0002-vector-database-pgvector.md)
   * [ADR-0003: Hybrid Real-Time & Batch Architecture for Personalization](adr/0003-hybrid-realtime-batch-processing.md)

5. **Architecture Review Board (ARB) Checklist:**  
   Production readiness criteria covering Security, PDPL Compliance, AI Evals (Langfuse/Sentry), Cost Governance, and Dual Human Oversight (Marketer Approval + End-User Agency + Kill-Switch):  
   [docs/arb-checklist.md](docs/arb-checklist.md)

---

## Assumptions & Design Boundaries

* **Inbound Data Sanitization:** PII masking occurs in-memory prior to passing context to third-party model providers.
* **Pragmatic Scaling (No Premature Optimization):** PostgreSQL with `pgvector` handles relational data and embeddings under a single datastore until active vectors exceed 1,000,000 or p95 search latency exceeds 50ms, triggering migration to a dedicated engine (Qdrant).
* **Hybrid Serving Separation:** Intensive machine learning model passes (collaborative filtering and customer clustering) run in off-peak batch windows, while Next-Best Action evaluations run in under 50ms at request time against an in-memory Redis feature store.
* **Human-in-the-Loop Authority:** Autonomous agents are strictly restricted from directly broadcasting marketing campaigns without human verification (Approve, Edit, Reject, or Schedule).

---

## Interactive Study Materials

Interactive Arabic RTL reference guides covering theoretical foundations, architectural viewpoints, event-driven data pipelines, and production AI stacks are published via GitHub Pages:

* **Session 1 (Fundamentals, Frameworks & Case Study):**
  * Web Guide: [Aya-Aboulwafa.github.io/ai-architecture-portfolio](https://aya-aboulwafa.github.io/ai-architecture-portfolio/)
  * Local Markdown Notes: [study/session-1-notes.md](study/session-1-notes.md)
* **Session 2 (Viewpoints, Event-Driven Data Platform & Production AI Stack):**
  * Web Guide: [Aya-Aboulwafa.github.io/ai-architecture-portfolio/#session-2](https://aya-aboulwafa.github.io/ai-architecture-portfolio/#session-2)
  * Local Markdown Notes: [study/session-2-notes.md](study/session-2-notes.md)
