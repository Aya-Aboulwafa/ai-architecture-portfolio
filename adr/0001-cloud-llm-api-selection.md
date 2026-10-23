# ADR-0001: Adoption of Commercial Cloud LLM APIs with In-Memory PII Masking

* **Status:** Under Review
* **Date:** 2026-10-23
* **Deciders:** Aya Aboulwafa 
* **Governing Principle:** [AP-02: Robust LLM Guardrails](../docs/architecture-principles.md#ap-02-robust-llm-guardrails--defense-in-depth), [AP-03: Privacy by Design & PDPL](../docs/architecture-principles.md#ap-03-privacy-by-design--pdpl-compliance)

---

## 1. Context & Problem Statement

The **Marketing Automation with AI & Agents** system requires natural language generation and agentic reasoning capabilities for:
1. Dynamic ad copy and email campaign generation.
2. Next-Best Action decision explanations.
3. Multi-agent campaign automation (planning, drafting, evaluating marketing workflows).

We need an LLM serving strategy that maximizes reasoning quality and time-to-market while strictly abiding by the Saudi Personal Data Protection Law (PDPL) and keeping infrastructure overhead manageable for the initial system release.

---

## 2. Decision Drivers

* **Time to Market:** Rapid deployment without upfront GPU procurement or dedicated MLOps infrastructure.
* **Reasoning Capability:** High-fidelity creative copywriting and reliable structured tool-calling in Arabic and English.
* **Cost & Capital Expenditure (CapEx):** Zero upfront hardware costs; pay-per-token operational expenditure (OpEx).
* **Data Privacy (PDPL):** Absolute prohibition against leaking customer PII or having proprietary business data used for model training.

---

## 3. Considered Options

1. **Option A: Commercial Cloud LLM APIs (OpenAI / Anthropic Claude / Google Vertex AI) with In-Memory PII Masking.**
2. **Option B: Self-Hosted Open-Source Models (Llama 3 / Mistral) on dedicated GPU clusters (vLLM / TGI).**
3. **Option C: Hybrid Deployment (Small local LLM for classification + Cloud LLM for copywriting).**

---

## 4. Decision Outcome & Architecture Specification

**Chosen Option:** **Option A — Commercial Cloud LLM APIs with In-Memory PII Masking.**

### Architectural Guardrails Applied:
* **In-Memory PII Masking / Tokenization:** Before any prompt is dispatched to the external LLM provider, an inbound gateway sanitizes all customer identifiers (names, emails, phone numbers, purchase amounts) replacing them with anonymous synthetic tokens (e.g., `<USER_REF_492>`, `<PRODUCT_CATEGORY_FASHION>`).
* **Zero Data Retention (ZDR):** Enterprise API agreements must be enforced with vendors specifying that prompts and completions are neither logged permanently nor used for model training.
* **Fallback Strategy:** Multi-provider API routing (e.g., OpenAI with Claude as automatic fallback) via an LLM Gateway to mitigate external API downtime.

---

## 5. Pros and Cons / Trade-off Analysis

### Pros
* **Immediate High Performance:** Frontier model intelligence for Arabic copywriting and agentic orchestration out of the box.
* **Zero GPU Maintenance:** No need for cluster provisioning, CUDA driver updates, or autoscaling GPU nodes.
* **Pay-as-you-go Economics:** Aligns with early campaign iteration without idle infrastructure waste.

### Cons & Mitigations
* **Per-Token Cost at Extreme Scale:** Addressed by aggressive prompt caching and local rule-based filtering before LLM invocation.
* **Vendor Dependency & Rate Limits:** Addressed by multi-provider gateway routing with exponential backoff and circuit breakers.
