# ADR-0002: Selection of pgvector for Unified Relational and Vector Storage

* **Status:** Under Review
* **Date:** 2026-10-23
* **Deciders:** Aya Aboulwafa 
* **Governing Principle:** [AP-01: Strict User Context Isolation](file:///home/astrid/Projects/ai-architecture-portfolio/docs/architecture-principles.md#ap-01-strict-user-context-isolation), [AP-05: Zero-Trust API Security & Decoupled Architecture](file:///home/astrid/Projects/ai-architecture-portfolio/docs/architecture-principles.md#ap-05-zero-trust-api-security--decoupled-architecture)

---

## 1. Context & Problem Statement

The **Marketing Automation with AI & Agents** system needs dense vector similarity search to power:
1. Product catalog embeddings matching customer interest vectors.
2. Contextual retrieval of past marketing campaign templates.
3. Cold-start user recommendations based on initial session intent.

We must decide between leveraging our primary transactional database (PostgreSQL with `pgvector`) versus introducing a dedicated vector database (Qdrant, Pinecone) or a graph/multi-model database (SurrealDB).

---

## 2. Decision Drivers

* **Operational Simplicity:** Minimize the count of distinct database systems requiring replication, monitoring, and backups.
* **ACID Consistency:** Ensure strict transactional consistency between relational customer profiles, purchase history, and vector embeddings without complex dual-write synchronizations.
* **Cost Efficiency:** Leverage existing managed PostgreSQL infrastructure without incurring additional managed SaaS vector DB licensing fees.
* **Pragmatic Scaling:** Satisfy current retrieval demands while clearly defining migration triggers for future scale.

---

## 3. Considered Options

1. **Option A: PostgreSQL with `pgvector` extension (Unified Datastore).**
2. **Option B: Dedicated Vector Engine (Qdrant / Milvus / Pinecone).**
3. **Option C: Multi-model database (SurrealDB).** *(Eliminated early due to operational immaturity and unnecessary complexity for current scope).*

---

## 4. Decision Outcome & Architecture Specification

**Chosen Option:** **Option A — PostgreSQL with the `pgvector` extension.**

### Architecture Specification:
* **HNSW Indexing:** Use Hierarchical Navigable Small World (`HNSW`) indexes inside PostgreSQL for sub-millisecond approximate nearest neighbor (ANN) retrieval.
* **Relational-Vector Hybrid Queries:** Perform single-query metadata filtering (e.g., `WHERE tenant_id = ? AND category = ? ORDER BY embedding <=> query_vector LIMIT 10`) eliminating the need for complex distributed joins.
* **Migration Trigger (Scale-out Policy):** We establish an explicit threshold: If the embedding volume exceeds **1,000,000 active vectors** OR the vector search p95 latency degrades beyond **50ms**, the team will initiate an architectural migration to a dedicated **Qdrant** cluster.

---

## 5. Pros and Cons / Trade-off Analysis

### Pros
* **Single Source of Truth:** Zero data drift between customer master data and vector embeddings.
* **Familiar Tooling:** Existing backup, replication, connection pooling (PgBouncer), and security auditing apply seamlessly.
* **Zero Additional Infrastructure Cost:** Operates within our allocated relational database capacity.

### Cons & Mitigations
* **Memory Contention at Scale:** Vector index caching competes with relational buffer pools.  
  * *Mitigation:* Allocate dedicated shared memory buffers for HNSW indices and enforce the 1M vector migration trigger.
