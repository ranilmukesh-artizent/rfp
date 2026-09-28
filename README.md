# Enterprise Business Context Library (BCL) & Technical Due-Diligence Architecture

[![Architecture](https://img.shields.io/badge/Architecture-3--Record_Model-0078D4.svg)](#2-system-architecture)
[![Specification](https://img.shields.io/badge/Open_Spec-Apache_Ossie_v1.0-orange.svg)](https://github.com/apache/ossie)
[![Protocol](https://img.shields.io/badge/Protocol-MCP_2026--07--28-blueviolet.svg)](https://spec.modelcontextprotocol.io)
[![Verification](https://img.shields.io/badge/Methodology-DeepMind_SAFE_%2B_CoVe-success.svg)](#4-universal-due-diligence--adversarial-verification-framework)
[![Tenant Boundary](https://img.shields.io/badge/Boundary-100%25_Client_Tenant-brightgreen.svg)](#6-security-governance--compliance-boundaries)

An enterprise reference architecture, RFP response design, and adversarial due-diligence framework for the **Business Context Library (BCL)**. This repository establishes a governed, vendor-neutral semantic layer and context-brokering infrastructure operating entirely within an enterprise Microsoft 365 and Azure tenant boundary.

---

## 1. Executive Thesis: The 3-Record Model

Most enterprise context and RAG initiatives fail because they conflate data storage with business meaning, build redundant retrieval plumbing that hyperscalers commoditize, or rely on vanity retrieval metrics that fail to prove business value.

This architecture enforces **Three Records, Not One**:

```
 ┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
 │    CONTENT OF RECORD    │     │     INDEX OF RECORD     │     │     DECISION RECORD     │
 │ (Portable, Git-backed)  │ ──► │  (Disposable, Ephemeral)│ ──► │  (Measurable Adoption)  │
 │ Markdown + Ossie YAML   │     │  Azure AI Search BCL    │     │ Attribution + Telemetry │
 └─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘
```

1. **A Content of Record you can walk away with:** Git-backed SharePoint document library storing human-diffable Markdown and open-standard **Apache Ossie (OSI v1.0)** YAML schemas.
2. **An Index of Record you can throw away and rebuild:** A disposable, compact Azure AI Search index covering strictly curated BCL entries (thousands, not millions), reaching the broad estate via native platform retrieval (M365 Copilot Retrieval API / Foundry IQ).
3. **A Decision Record that proves the library was used:** Rigorous **contributive context attribution** (leave-one-out ablation / Kernel-SHAP) and an immutable decision register feeding executive gate dashboards in Power BI.

---

## 2. System Architecture

### Architectural Blueprint (16:9 Enterprise Widescreen)

![Enterprise Business Context Library (BCL) Reference Architecture](assets/bcl_architecture_diagram.jpg)

### The 4 Vertical Pillars & Foundation Plane

| Pillar | Core Technologies | Primary Function | Operational Guarantees |
|---|---|---|---|
| **Zone 1: Content of Record** | Git, Markdown, Apache Ossie (OSI v1.0) YAML, CI Pipelines | Single source of authoritative truth for metrics, client profiles, and decisions. | Strict GitOps write-path; automated CI validation (JSON/YAML schema checks, PII whitelisting, ID uniqueness, KQL syntax tests). |
| **Zone 2: Search & Estate Index** | Azure AI Search, M365 Copilot Retrieval API, Foundry IQ, In-Process Graph | Permission-trimmed hybrid retrieval across curated context and the broader enterprise estate. | Index is 100% disposable; zero duplicate document storage; document-level ACLs enforced natively by platform. |
| **Zone 3: Context Broker & MCP** | Azure Functions, Azure API Management, MCP (`2026-07-28`), REST | Governed context retrieval, prompt assembly, and token budget enforcement. | Deterministic scope resolution, hard token limits, Normalisation Span Processor for OTel GenAI telemetry. |
| **Zone 4: Decision Record & Consumption** | Copilot Studio, Office Add-ins, Log Analytics, Power BI | Multi-surface agent consumption coupled with empirical reuse measurement. | Proves ROI via contributive attribution (ContextCite / LOO ablation) and immutable decision registers. |
| **Horizontal Foundation** | Enterprise Tenant Boundary (Client M365 & Azure Subscription) | Complete data sovereignty and zero supplier-hosted critical path components. | Full exit portability; all keys, data, and compute remain in client tenant under Entra ID and Purview DSPM. |

---

### Interactive System Topology & Data Flow

```mermaid
flowchart TD
    subgraph COR["1. Content of Record (Authoritative GitOps)"]
        Y1["metrics/*.yaml (Apache Ossie OSI v1.0)"]
        M1["clients/*.md & decisions/*.md"]
        CI["CI Gate: Schema Check + PII Whitelist + KQL Test"]
        Y1 --> CI
        M1 --> CI
    end

    subgraph IDX["2. Search & Estate Index (Disposable by Design)"]
        AIS[("Azure AI Search: BCL Index Only<br>(BM25 + Vector + Semantic Reranker)")]
        M365["M365 Copilot Retrieval API / Foundry IQ<br>(Tenant Source Estate - Not Copied)"]
        GRAPH["In-Process Relationship Graph<br>(1-hop expansion via rustworkx)"]
        CI -->|Idempotent Build| AIS
        CI -->|Derive Links| GRAPH
    end

    subgraph BROKER["3. Context Broker (Single Retrieval Path)"]
        APIM["Azure API Management Gateway"]
        FUNC["Azure Functions Context Broker<br>• Deterministic Scope Resolution<br>• Token Budget Enforcement<br>• Citation Contract Engine"]
        OTEL["Normalisation Span Processor<br>(OTel GenAI Conventions)"]
        APIM --> FUNC
        AIS <-->|BCL Search| FUNC
        M365 <-->|Estate Retrieval| FUNC
        GRAPH <-->|Topology Expand| FUNC
        FUNC --> OTEL
    end

    subgraph CONSUMPTION["4. Decision Record & Consumption Surfaces"]
        CS["Copilot Studio Agent<br>(Teams & M365 Copilot)"]
        OFFICE["Office Add-ins<br>('Check My Deliverable')"]
        IDE["Notebook / IDE Agents<br>(Data Science & Analytics)"]
        
        ATTR["Contributive Attribution Engine<br>(Leave-One-Out Ablation / Kernel-SHAP)"]
        DREG[("Decision Register & Log Analytics")]
        PBI["Power BI Gate Dashboard<br>(Empirical Proof of Reuse)"]
        
        FUNC -->|MCP 2026-07-28 & REST| CS
        FUNC -->|MCP 2026-07-28 & REST| OFFICE
        FUNC -->|MCP 2026-07-28 & REST| IDE
        
        CS --> ATTR
        OFFICE --> ATTR
        ATTR --> DREG
        DREG --> PBI
    end

    classDef pillar fill:#f8fafc,stroke:#334155,stroke-width:1px;
    class COR,IDX,BROKER,CONSUMPTION pillar;
```

---

## 3. The Five Findings That Changed the Answer

The proposal separates unverified assumptions from verified 2026 platform realities:

1. **Microsoft Delivered the Architecture Layer in June 2026 (Foundry IQ & Fabric IQ):**
   * *Reality:* Microsoft shipped Foundry IQ as a managed knowledge layer connecting structured and unstructured data with query-time ACL enforcement.
   * *Strategic Move:* Do not waste capital building a bespoke ingestion/indexing pipeline. **Buy the plumbing, build the meaning, own the measurement.**
2. **Apache Ossie (OSI v1.0) Standardizes Semantic Metrics:**
   * *Reality:* The Open Semantic Interchange (OSI) v1.0 specification was donated to the Apache Software Foundation in June 2026 as **Apache Ossie** (`github.com/apache/ossie`).
   * *Strategic Move:* Author the **Metrics & KPIs** domain as Ossie-shaped YAML from day one. This makes semantic metric forks machine-detectable in CI rather than an elusive discovery exercise.
3. **M365 Copilot Retrieval API is Delegated-Auth Only:**
   * *Reality:* The v1.0 Retrieval API enforces a 200 req/user/hour quota and **does not support application permissions**.
   * *Strategic Move:* A batch "check every deliverable at midnight" job cannot use this API. Automated deliverable verification must query the BCL's disposable Azure AI Search index directly via app permissions, while interactive checking runs through the user-delegated path.
4. **Re-Indexing the Source Estate is a Compliance Liability:**
   * *Reality:* Azure AI Search SharePoint indexers lack support for tenants with Entra ID Conditional Access enabled and cap ACL entries per file.
   * *Strategic Move:* Index strictly the curated BCL entries; reach the broader estate solely through native remote platform retrieval.
5. **Retrieval Logs Cannot Measure Reuse:**
   * *Reality:* Counting retrieval API hits inflates adoption metrics by 10× because models often ignore injected context chunks.
   * *Strategic Move:* Implement **contributive context attribution** (leave-one-out ablation / ContextCite) to mathematically quantify which BCL entries directly impacted agent outputs.

---

## 4. Repository Document Catalog

This repository houses the complete RFP proposal, technical due-diligence audits, and specialized adversarial prompts:

| File | Classification | Summary & Key Content |
|---|---|---|
| [BCL-architecture-and-proof-design.md](file:///c:/repos/assurant/BCL-architecture-and-proof-design.md) | **Working RFP Proposal** | Core architectural proposal, 3-record model breakdown, the five pivotal findings, questions for the client, and measurement design. |
| [BCL_Architecture_and_Proof_Design_Sourced.md](file:///c:/repos/assurant/BCL_Architecture_and_Proof_Design_Sourced.md) | **Peer-Sourced Proposal** | Fully cited edition referencing 20+ primary source benchmarks, ASF specs, Microsoft Learn documentation, and NeurIPS research. |
| [BCL_External_Consultant_Briefing_Pack_Final.md](file:///c:/repos/assurant/BCL_External_Consultant_Briefing_Pack_Final.md) | **Client Briefing Pack** | The original client specification, problem statement, seven context domains, and criteria for the December gate. |
| [BCL_Technical_Due_Diligence_Report.md](file:///c:/repos/assurant/BCL_Technical_Due_Diligence_Report.md) | **Adversarial Audit Report** | Rigorous independent audit of the proposal, claim verification table, red-team attack vectors, and 10 procurement gate questions. |
| [verification-due-diligence.md](file:///c:/repos/assurant/verification-due-diligence.md) | **Specialized System Prompt** | SOTA 2026 Adversarial Verification Framework prompt based on DeepMind SAFE, Factored CoVe, and 9-stage verification lifecycle. |
| [universal-due-diligence-engine.md](file:///c:/repos/assurant/universal-due-diligence-engine.md) | **Specialized System Prompt** | Universal Claim Verification & Adversarial Due-Diligence Engine prompt with cross-domain adaptation matrices. |

---

## 5. Universal Due-Diligence & Adversarial Verification Framework

Included in this repository are the specialized agent prompts (`verification-due-diligence.md` and `universal-due-diligence-engine.md`) that execute independent reality checks against architectural proposals.

### The 5-Tier Evidence Hierarchy

```
 Tier 1 (Gold)    : Official API Specs, RFCs, Merged PRs, Peer-Reviewed Papers, Statutory Gazettes
 Tier 2 (Silver)  : Engineering Blogs (Netflix, Uber, AWS Architecture), Outage Postmortems
 Tier 3 (Bronze)  : Independent Benchmarks (Artificial Analysis, LMSYS), Security Advisories
 Tier 4 (Context) : Curated Practitioner Communities (Hacker News, r/LocalLLaMA) for failure modes
 Tier 5 (Untrusted): Vendor Marketing, Sales Decks, Sponsored PR (Disqualified as primary proof)
```

### The 9-Stage Verification Lifecycle

1. **Atomic Decomposition:** Deconstruct paragraphs into isolated, falsifiable technical and financial claims.
2. **Factored Query Planning:** Generate neutral corroboration and falsification queries (SAFE / CoVe).
3. **Stage 1 Primary Audit:** Verify against Tier 1 specifications, RFCs, and official documentation.
4. **Stage 2 Orthogonal Audit:** Re-derive all assertions from an independent technical angle.
5. **Precedent Analysis:** Evaluate real-world failure modes and architectural postmortems.
6. **SOTA Standards Check:** Compare against 2026 production baselines (e.g., Apache Ossie, MCP `2026-07-28`).
7. **Mathematical Audit:** Re-compute token counts, VRAM allocations, and FinOps cloud billing models.
8. **Adversarial Red-Team Pass:** Stress-test prompt injection, oversharing amplification, and rate-limit throttling.
9. **Dual Deliverables:** Generate both inline annotated claims and an executive Due-Diligence Audit Report.

---

## 6. Security, Governance & Compliance Boundaries

- **Zero Supplier-Hosted Dependencies:** All infrastructure (Azure Functions, Azure AI Search, Azure OpenAI, Log Analytics) deploys directly within the client's corporate Azure subscription and Microsoft 365 tenant.
- **PII Whitelist Enforcement:** CI pipelines fail closed on any YAML frontmatter or Markdown property containing personnel data outside approved fields (`person.name` and `person.reports_to`).
- **Purview DSPM-for-AI Alignment:** Mandatory pre-deployment data-risk assessment to remediate legacy SharePoint oversharing ("Everyone except external users") before exposing search indexes to agent retrieval.
- **Statutory Cloud Isolation:** Validated exclusively for Global Cloud deployments (explicitly excluding GCC High / DoD / 21Vianet where Copilot Retrieval APIs are unsupported).

---

## 7. Repository Maintenance & Git Guidelines

- **Clean Scope:** This repository is strictly dedicated to the Business Context Library (BCL) architecture, RFP responses, and specialized due-diligence prompts.
- **Excluded Assets:** Local experimental reports, recruitment evaluation runs, and scratch scripts are filtered via [`.gitignore`](file:///c:/repos/assurant/.gitignore).
- **Branching Policy:** All updates to architectural specifications follow standard GitOps review procedures: branch, pull request, CI schema validation, and merge to `main`.
