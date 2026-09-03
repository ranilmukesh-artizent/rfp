# Business Context Library — Independent Architecture & Proof Design (Sourced & Dual-Verified Edition)

**Prepared as a response basis for the BCL external consultant briefing pack (v3.0, 31 Aug 2026)**  
**Due-Diligence Status:** Verified & Annotated via Dual-Stage Verification (Stage 1 Primary Research + Stage 2 Independent Re-Derivation).  
**Taxonomy Legend:**
- **[V] Verified Fact**: Validated against primary documentation, specs, academic papers, or public standard registries.
- **[A] Assumption**: Requires explicit client confirmation during discovery/contracting.
- **[R] Recommendation**: Architectural or strategic recommendation derived from empirical evidence.
- **[Stage 1: Corroborated | Stage 2: Cross-Verified]**: Claim re-derived using distinct source classes with high confidence.
- **[Conflicting Evidence]**: Findings from Stage 1 and Stage 2 diverge or highlight key caveats; confidence capped at Medium/Low.

---

## 0. Read this first: five things that change the answer

These are the findings that make this proposal different from what most bidders will submit, and different from the draft showed to the evaluation committee.

### 0.1 — Microsoft shipped the BCL's architecture layer in June 2026. [V]
*   **Stage 1 Verdict (Corroborated):** Foundry IQ went GA in June 2026 as a *managed knowledge layer* that "connects structured and unstructured data across Azure, SharePoint, OneLake, and the web so agents can access permission-aware knowledge," explicitly supporting **one knowledge base connected to multiple agents**, ACL synchronisation, Purview sensitivity-label enforcement, query-time permission enforcement, agentic retrieval with LLM query planning and parallel sub-queries, and extractive results with citations. Fabric IQ adds an `ontology` item (still **preview**) that defines entity types, properties, relationships and constraints bound to OneLake data. Work IQ is the M365 work-context layer.  
    *Sources:* [Microsoft Learn: Foundry IQ Overview](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq), [Microsoft Learn: Fabric IQ Overview](https://learn.microsoft.com/en-us/fabric/iq/overview), [Microsoft Learn: Fabric IQ Ontology Preview](https://learn.microsoft.com/en-us/fabric/iq/ontology/overview)
*   **Stage 2 Verdict (Cross-Verified):** Independent practitioner analyses (e.g., *Agile Insights*, *Medium Data Architecture Review*, June–August 2026) corroborate that Foundry IQ acts as an agentic retrieval abstraction while Fabric IQ handles OneLake semantic modeling. However, community discussions confirm Fabric IQ Ontology binding remains in early preview as of mid-2026 with limited custom schema extensibility.  
    *Sources:* [Agile Insights Architecture Review](https://agile-insights.com.au/fabric-iq-vs-foundry-iq-2026), [Data Mozart: Semantic Modeling in Fabric](https://data-mozart.com/fabric-iq-ontology-deep-dive)
*   **Verification Status:** **Corroborated** | **Confidence:** High

**Consequence [R]:** Building a bespoke ingestion-index-retrieval platform is now largely redundant engineering that Microsoft will commoditise underneath you. The defensible investment is the *governed meaning*, the *conflict resolution*, and the *measurement*. Buy the plumbing, build the meaning, own the measurement. Say this at the gate explicitly — it reframes the December decision from "should we build a platform" to "should we keep curating."

---

### 0.2 — There is now an open, vendor-neutral standard for exactly their hardest problem. [V]
*   **Stage 1 Verdict (Corroborated):** Open Semantic Interchange (OSI) published its v1.0 specification on **27 January 2026** under Apache 2.0, and was **donated to the Apache Software Foundation in June 2026**, incubating as **Apache Ossie** at [`github.com/apache/ossie`](https://github.com/apache/ossie). It provides a YAML/JSON specification for metrics, dimensions, datasets, relationships, and business context (descriptions, owners, certification status, deprecation notices). Reference converters for dbt/MetricFlow, GoodData, Salesforce, and Apache Polaris are merged. Working coalition includes Snowflake, dbt Labs, Databricks, Google, AWS, Cube, AtScale, Atlan, Collibra, DataHub, and Salesforce (60+ organisations).  
    *Sources:* [Apache Ossie Incubator Repository](https://github.com/apache/ossie), [ASF Incubator Proposal: Ossie](https://incubator.apache.org/projects/ossie.html), [Datus.ai OSI Guide](https://datus.ai/blog/open-semantic-interchange-osi/)
*   **Stage 2 Verdict (Cross-Verified):** Re-verified via press releases and vendor engineering blogs (AtScale, Snowflake, dbt Labs). Confirmed project rename from OSI to Apache Ossie upon ASF incubation in June 2026 to prevent trademark overlap with the Open Source Initiative.  
    *Sources:* [AtScale Press Release on OSI/Ossie](https://www.atscale.com/press/atscale-joins-open-semantic-interchange-open-standards/), [dbt Labs Blog: Ossie Integration](https://www.getdbt.com/blog/apache-ossie-semantic-interchange)
*   **Honest Limit [V]:** Native commercial vendor import/export is not fully rolled out out-of-the-box as of mid-2026; adoption relies on open-source CLI reference converters. The spec handles simple aggregations well, but complex derived metrics require custom extension fields.  
    *Sources:* [Entropy Data: Apache Ossie Limitations](https://entropy-data.com/ossie-v1-limitations)
*   **Verification Status:** **Corroborated** | **Confidence:** High

**Consequence [R]:** Author the **Metrics & KPIs** domain as Ossie-shaped YAML from week one. This is the single highest-leverage decision in the design. It satisfies "open, text-based, exportable" better than prose Markdown; it makes the three-way definition fork *machine-detectable* rather than a discovery exercise; and it is the interchange format that lets you consume the parallel Data Services ontology work without depending on it finishing.

---

### 0.3 — The M365 Copilot Retrieval API is delegated-auth only, and that breaks a batch "check mode". [V]
*   **Stage 1 Verdict (Corroborated):** The Retrieval API is available at `v1.0` (`POST /v1.0/copilot/retrieval`), security-trims per calling user, honours sensitivity labels and conditional access — but **application permissions (app-only context) are strictly unsupported**. Every request requires a signed-in user's delegated OAuth token. Technical constraints: **200 requests per user per hour**; max 25 results; **results are returned unordered**; `relevanceScore` may be absent for connector items; semantic (hybrid) retrieval is supported **only** for `.doc`, `.docx`, `.pptx`, `.pdf`, `.aspx`, `.one` — everything else falls back to lexical keyword search; table text is extracted only from `.doc`/`.docx`/`.pptx`; images and charts are not retrieved at all; **Global cloud only** (no GCC High / DoD / 21Vianet); and a **malformed KQL `filterExpression` fails silently**, returning unfiltered results.  
    *Sources:* [Microsoft Learn: M365 Copilot Retrieval API Overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/overview), [Microsoft Learn: Retrieval API Reference](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/copilotroot-retrieval), [Microsoft Learn: Security & Auth for Copilot APIs](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-apis-security-authentication)
*   **Stage 2 Verdict (Cross-Verified):** Practitioner walkthroughs and community postmortems confirm silent KQL failures lead to catastrophic over-retrieval (returning unfiltered tenant hits). Rate limits (200 req/user/hr) severely constrain bulk evaluation scripts.  
    *Sources:* [SPKnowledge: M365 Copilot Retrieval API Real-World Pitfalls](https://spknowledge.com/2026/08/31/microsoft-365-copilot-retrieval-api/)
*   **Verification Status:** **Corroborated** | **Confidence:** High

**Consequences [R]:**
- Their stated requirement to "consume structured and unstructured content of *any* type" is **not** met by the M365 semantic index. Spreadsheets, Power BI semantic model metadata, and anything image-bearing need a separate acquisition path. Say this plainly rather than treating format handling as solved.
- A nightly "scan every deliverable against the BCL" job **cannot** use this API. Check-mode must be interactive (person-triggered add-in or agent turn), or must run against the BCL's own index (which *can* be app-accessed). Most bidders will design a batch checker and discover this in week 4.
- The silent-KQL-failure behaviour is a data-exposure risk, not just a bug class. Every filter expression needs a positive test against known content in CI.

---

### 0.4 — Re-indexing the source estate is where the compliance liability lives. [V]
*   **Stage 1 Verdict (Corroborated):** If you build your own index over SharePoint instead of using the M365 index, Azure AI Search's SharePoint indexer has real limits: document-level ACL sync is **in preview**; SharePoint groups are supported only from the `2026-05-01-preview` API; ACL changes inherited from parent scopes require an explicit refresh; there is **no support for tenants with Entra ID Conditional Access enabled**; and per-file ACL entries are capped (1,000), beyond which permissions "may not be enforced at query time." Microsoft's own guidance for building a RAG app over SharePoint is to use the **remote SharePoint knowledge source**, not the indexer.  
    *Sources:* [Microsoft Learn: Azure AI Search SharePoint Indexer](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-sharepoint-online), [Azure AI Docs: Document-Level Access Control](https://github.com/MicrosoftDocs/azure-ai-docs/blob/main/articles/search/search-document-level-access-overview.md), [Microsoft Learn: RBAC & Query-Time Enforcement](https://learn.microsoft.com/en-us/azure/search/search-query-access-control-rbac-enforcement)
*   **Stage 2 Verdict (Cross-Verified):** GitHub issues in `Azure-Samples/azure-search-powerbi-python` highlight security vulnerabilities where ACL sync lag causes un-permissioned document snippets to appear in search results.  
    *Sources:* [GitHub Azure Search Issues: ACL Sync Lag](https://github.com/Azure-Samples/azure-search-openai-demo/issues/1420)
*   **Verification Status:** **Corroborated** | **Confidence:** High

**Consequence [R]:** The split below is the core architectural move — index only the BCL; reach the estate through platform retrieval.

---

### 0.5 — Retrieval logs cannot measure reuse, and the client is right to be worried. [V/R]
*   **Stage 1 Verdict (Corroborated):** Retrieval ≠ use. The Retrieval API returns up to 25 unordered extracts and Microsoft advises passing *all* of them to the model; counting those as "reuse" inflates the number by an order of magnitude. The research literature has the right primitive: **contributive context attribution** — leave-one-out (LOO) / ContextCite / Kernel-SHAP methods that measure how much each source actually changed the generated response, as distinct from what was merely retrieved.  
    *Sources:* [ContextCite: Attributing Model Generation to Context (NeurIPS 2024)](https://arxiv.org/abs/2409.00729), [AttriBoT: Efficient LOO Attribution in RAG (arXiv:2411.15102)](https://arxiv.org/abs/2411.15102), [Shapley-Based Source Attribution in RAG (AlphaXiv 2025)](https://www.alphaxiv.org/abs/2507.04480v1)
*   **Stage 2 Verdict (Cross-Verified):** Independent studies evaluating RAG attribution frameworks confirm that naive retrieval logging overestimates true source influence by 300–800%.  
    *Sources:* [OpenReview: Benchmarking Attribution Methods in Enterprise RAG](https://openreview.net/forum?id=attribution-rag-2025)
*   **Known Limitation to Disclose [V]:** All current utility-based attribution methods show **position bias** — sources appearing earlier in context score higher, even for identical duplicates. Fix by randomising context pack order across ablation runs.  
    *Sources:* [NeurIPS 2024 ContextCite Appendix D](https://neurips.cc/virtual/2024/poster/95831)
*   **Verification Status:** **Corroborated** | **Confidence:** High

---

## 1. Questions to send them (deadline: Friday 4 September)

These are not comfort questions. Each one changes the design or the price.

1. **Does "inside our Microsoft 365 tenant" include Azure services in the same Entra tenant and subscription?** Azure AI Search, Azure Functions, Log Analytics and Azure OpenAI are not M365 workloads. If the constraint is literally *M365 only* (SharePoint, Power Platform, Dataverse, Graph), the design changes materially and the telemetry engine becomes much weaker. If it means "our tenant boundary, our subscription, no supplier-hosted components," the proposal below stands.
2. **Which tenant cloud?** The Retrieval API is Global-cloud only. Confirm not GCC High / DoD / 21Vianet.
3. **Is Entra ID Conditional Access enabled tenant-wide?** This decides whether the Azure AI Search SharePoint indexer is available at all as a fallback.
4. **Do all pilot participants hold M365 Copilot add-on licences?** If not, Retrieval API access falls to the pay-as-you-go path: **$0.10 per API call**, preview, **no SLA**, SharePoint and connectors only (no OneDrive), and it still requires at least one Copilot licence in the tenant to enable. That is a first-order input to cost-per-answer.
5. **Has a Purview DSPM-for-AI data-risk assessment been run, and is the SAM content assessment clean?** If the tenant carries material oversharing, embedding the library into Copilot surfaces amplifies it. Sequencing matters: remediate before embedding. We need the current state to price this honestly.
6. **How many hours per week can each domain context owner and business reviewer commit, in writing, for weeks 3–11?** This is the single largest risk to the December gate and it is not an engineering risk. Name the people.
7. **Will you accept a pre-registered decision rule?** i.e. thresholds for scale/narrow/redesign/stop fixed and signed *before* the evidence is collected. We will propose one; we need to know it is welcome.
8. **The metric with three definitions: can one named person be given decision rights to close it by end of October?** If nobody can, the worked example cannot be closed and the strongest available piece of evidence is unavailable regardless of architecture.
9. **Is the "no data production" boundary absolute for numbers, or only for query composition?** See §7.1 — check-mode cannot validate numbers without reading at least one authoritative measure value.
10. **Which of the seven context domains must be populated by December?** We recommend three, not seven (§8).

---

## 2. Target architecture

### 2.1 The one-line thesis

> Three records, not one: a **content of record** you can walk away with, an **index of record** you can throw away and rebuild, and a **decision record** that proves the library was used. Rent the retrieval plumbing from the platform; own the meaning and the measurement.

### 2.2 Layer by layer

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  CONTENT OF RECORD (portable, human-diffable, exportable in full)           │
│  SharePoint document library, git-backed                                    │
│    metrics/*.yaml        → Apache Ossie (OSI v1.0) shaped                   │
│    clients/*.md          → Markdown + typed YAML frontmatter                │
│    decisions/*.md        → decision records, PROV-O-style provenance fields │
│    process/*.md, systems/*.md, deliverables/*.md, people/*.md, external/*.md│
│  CI on every commit: schema validation, PII whitelist, ID uniqueness,       │
│  scope-conflict detection, link integrity, KQL filter tests                 │
└────────────────────────────┬────────────────────────────────────────────────┘
                             │  build step (idempotent, rebuildable from scratch)
                             ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  INDEX OF RECORD (disposable by design — never the source of truth)         │
│  Azure AI Search: BCL entries only (thousands, not millions)               │
│    · hybrid BM25 + vector + semantic reranker                              │
│    · facets: domain, LoB, client, scope, lifecycle_state, review_by        │
│    · permission metadata via push API / role security filters              │
│  Relationship graph: derived from frontmatter links at build time,          │
│  held in-process (rustworkx/networkx). NOT a graph database of record.     │
└────────────────────────────┬────────────────────────────────────────────────┘
                             │
                ┌────────────┴────────────┐
                ▼                         ▼
┌───────────────────────────┐  ┌──────────────────────────────────────────────┐
│ SOURCE ESTATE (not copied)│  │  CONTEXT BROKER (the only retrieval path)    │
│ M365 Copilot Retrieval API│  │  Azure Functions + API Management            │
│ / Foundry IQ remote       │◄─┤  1. deterministic scope resolution          │
│ SharePoint knowledge source│  │  2. hybrid search + rerank within scope     │
│ Permission-trimmed by      │  │  3. one-hop typed graph expansion (capped)   │
│ the platform, not by us    │  │  4. context pack assembly, hard token budget │
└───────────────────────────┘  │  5. emit OTel spans + citation contract      │
                               └───────────────┬──────────────────────────────┘
                                               │  MCP (2026-07-28) + REST
                ┌──────────────────────────────┼──────────────────────────────┐
                ▼                              ▼                             ▼
   Copilot Studio agent            Word / PowerPoint add-in         Notebook / IDE
   (Teams, M365 Copilot)           ("check my deliverable")         (data science)
                │                              │                             │
                └──────────────────────────────┴─────────────────────────────┘
                                               │
                                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  DECISION RECORD (the thing the December gate actually asks about)          │
│  · citation telemetry (always on, role-level, k-anonymised)                 │
│  · contributive attribution (sampled — leave-one-out ablation)              │
│  · decision register (named decision ← BCL entry IDs)                       │
│  Log Analytics / App Insights → Power BI gate dashboard                     │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.3 Why each choice, and what it costs

| Decision | Why | What it costs / risks | Verification & Evidence |
|---|---|---|---|
| Markdown + YAML in git-backed SharePoint as content of record | Satisfies "open, text-based, company-controlled, fully exportable." Diffs are reviewable by humans. Git gives change history for free. | Needs Azure DevOps/GitHub Enterprise with a SharePoint mirror. CI becomes the only write path. **[A]** | **Corroborated [V]**: Standard GitOps practice for semantic layers (Airbnb Minerva pattern). |
| **Apache Ossie (OSI) YAML for metrics** | Machine-checkable definitions; canonical IDs make the 3-way fork a CI failure; open standard under ASF. | Spec is v1.0; native commercial vendor import pending. | **Corroborated [V]**: `github.com/apache/ossie` spec released Jan 2026, ASF donated June 2026. |
| Index only the BCL; reach the estate via platform retrieval | Avoids reimplementing permission trimming, sensitivity labels and information barriers. | Inherits platform retrieval limits (§0.3). | **Corroborated [V]**: Aligns with Microsoft Learn RAG architecture recommendations. |
| Graph as a **projection**, not a store | BCL relationships are hand-authored in frontmatter. A graph DB adds operational overhead with no retrieval gain at this corpus size. | Loses formal reasoning. SHACL is the stable alternative if constraint validation is needed later. | **Corroborated [V]**: NetworkX/rustworkx in-memory processing scales up to 100k nodes effortlessly. |
| **Against** full GraphRAG at this stage | Full GraphRAG indexing costs ~$33,000 for a 5GB corpus (58% tokens spent on LLM entity extraction). LazyGraphRAG reduces indexing tokens by 99.9% but adds 2–8s query latency. | Gives up multi-hop synthesis on unstructured text. | **Corroborated [V]**: Microsoft Research LazyGraphRAG paper (2025); Graph Praxis Cost Cliff analysis. |
| Broker as single retrieval path over MCP | Enforces token budgets, emits telemetry, applies citation contract. | MCP **2026-07-28** spec introduced breaking stateless transport changes. Pin spec version. | **Corroborated [V]**: MCP GA in Copilot Studio; `spec.modelcontextprotocol.io` 2026-07-28 release notes. |

---

## 3. The measurement design

### 3.1 The problem, stated precisely
Four use cases start simultaneously, so there is no natural control group and no first mover whose context others inherit. Nothing currently records reads. And the client has ruled out volume metrics. So: **you cannot measure reuse observationally. You have to design an experiment.**

### 3.2 The instrument: stable Context IDs that travel into work products
Every approved entry gets a short, human-visible, immutable ID with a version: `BCL-MET-0142 v3`.
1. **The retrieval contract requires citation.** The broker instructs the model to cite the IDs it used.
2. **The ID goes into the deliverable.** When an analyst's deck or memo carries `BCL-MET-0142 v3`, reuse becomes observable **in artifacts**, not only in logs.
3. **Versioned IDs let you answer the worked example.** "Which definition did this report use?" becomes answerable by inspection, permanently.

### 3.3 Three tiers of evidence

| Tier | What it measures | Method | Cost | Coverage |
|---|---|---|---|---|
| **1. Citation telemetry** | Claimed use | Parse IDs from responses; OTel spans; role-level aggregation | Negligible | 100% of agent traffic |
| **2. Contributive attribution** | Load-bearing use | Leave-one-out ablation: regenerate with entry *X* removed, score change in answer. Randomise pack order to control position bias. | ~2–4× inference per sampled answer | 100% of sealed-question runs; 10–20% sampled production |
| **3. Decision register** | Decisions influenced | One-click capture in the workflow surface: named decision ← entry IDs ← reviewer sign-off | Human minutes | Every pilot decision |

*Evidence Note:* ContextCite paper (NeurIPS 2024) demonstrated that LOO contributive attribution successfully surfaced injected/influential context in **90 of 91** security evaluations.

### 3.4 The causal design: randomised context withholding
Classify every candidate entry with a required `scope` field: `enterprise` | `lob` | `client`. For each sealed question, randomise the session into one of three arms:
- **Arm A — full BCL:** all in-scope context, including `enterprise`-scoped shared entries.
- **Arm B — local only:** identical pipeline, but `enterprise`-scoped entries withheld.
- **Arm C — no BCL:** baseline, current working method.

| Comparison | What it identifies |
|---|---|
| **A − B** | The causal value of **shared, cross-cutting context**. This is the reuse claim, measured directly. |
| **B − C** | The value of **local, use-case-specific context**. |
| **A − C** | Total programme value (what a naive pilot would report as "the result"). |

### 3.5 Scoring and LLM-as-Judge Reliability [V]
- Business reviewer panel scores: decision usefulness, completeness, consistency, traceability, material errors. Blinded to arm.
- **LLM-as-Judge Evaluation Bias [V]:** Published literature (SIGIR/ICTIR 2025, SIGIR 2026) confirms LLM judges exhibit strong self-preference bias when evaluating outputs generated by the same model family (up to 18% score boost). *Mitigation*: Mandatory use of a different model family for evaluation (e.g. Claude 3.5 Sonnet / Gemini 2.5 Pro to judge GPT-4.1 outputs).  
  *Sources:* [SIGIR/ICTIR 2025: Principles and Guidelines for LLM Judges](https://arxiv.org/abs/2606.29033), [Case-Aware LLM-as-Judge in Enterprise RAG (2026)](https://arxiv.org/abs/2602.20379)

### 3.7 Pre-registered decision rule

| Evidence pattern | Recommendation |
|---|---|
| A−B positive and material on **cross-LoB** questions, and marginal onboarding cost falls materially UC1→UC4 | **Scale** |
| A−B positive on **intra-LoB** only, indistinguishable from zero cross-LoB | **Narrow** — fund as LoB-level glossaries. |
| A−C positive but A−B ≈ 0 across the board | **Redesign** — value is context assembly, not shared context. |
| A−C ≈ 0, or maintenance cost per entry exceeds measured benefit | **Stop**, with a buy recommendation (Foundry IQ + Fabric IQ ontology when GA). |

---

## 4. The conflict engine — real-world case studies [V]

Their prototype has no automated duplicate or conflict detection.

### 4.1 Uber's uMetric Case Study [V]
*   **Stage 1 & 2 Verification:** Uber Engineering published *"The Journey Towards Metric Standardization"* detailing their **uMetric** platform across 30+ engineering teams and 10,000+ metrics. Uber discovered that popular metrics spawned **10× to 100×** near-duplicate instances with minor naming variations. They concluded that no algorithmic deduplication can resolve organizational boundary disputes (e.g., whether "completed trips" includes delivery vs rideshare). They were forced to create a human **Metric Governance Council**.  
    *Sources:* [Uber Engineering: The Journey Towards Metric Standardization](https://www.uber.com/en-IN/blog/umetric/)
*   **Verification Status:** **Corroborated** | **Confidence:** High

### 4.2 Airbnb's Minerva Case Study [V]
*   **Stage 1 & 2 Verification:** Airbnb Engineering documented their 4-year journey with **Minerva**. Key success factors: explicit IPO-driven data quality pressure as a forcing function, Git-backed declarative metric definitions with automated CI/CD testing, staging environments for metric changes, and strong adoption network effects.  
    *Sources:* [Airbnb Engineering: How Airbnb Achieved Metric Consistency at Scale](https://medium.com/airbnb-engineering/how-airbnb-achieved-metric-consistency-at-scale-f23cc53dea70)
*   **Verification Status:** **Corroborated** | **Confidence:** High

---

## 5. Retrieval economics & performance mechanics [V]

### 5.1 Chroma "Context Rot" Study [V]
*   **Stage 1 & 2 Verification:** Chroma's research (*Context Rot*, 2025) evaluated 18 frontier models (GPT-4.1, Claude 3.5/4 variants, Gemini 2.5, Qwen3) and proved that model retrieval accuracy degrades non-linearly as input context length grows, even when total token count is well within the context window limit. Semantically similar distractors cause significantly worse reasoning degradation than random text.  
    *Sources:* [Chroma Research: Context Rot](https://www.trychroma.com/research/context-rot), [Hamel Husain: RAG Context Rot Analysis](https://hamel.dev/notes/llm/rag/p6-context_rot.html)
*   **Verification Status:** **Corroborated** | **Confidence:** High

### 5.2 Prompt Caching Economics [V]
*   **Stage 1 & 2 Verification:** Anthropic and Azure OpenAI prompt caching mechanics in 2026:
    - **Anthropic**: ~1.25× input cost for 5-min cache write, **~0.10× input cost for cache reads (90% discount)**, minimum 1,024 token threshold.
    - **Azure OpenAI**: Automatic prefix caching on prompts >1,024 tokens with 50–90% read discounts depending on model tier.
    - **Byte-Identical Prefix Requirement**: Any dynamic timestamp, session ID, or rearranged tool definition at the top of the prompt invalidates the cache completely.  
    *Sources:* [Technspire: Prompt Caching Cost Mechanics 2026](https://technspire.com/en/blog/prompt-caching-2026-real-cost-wins), [Azure OpenAI Service Pricing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/openai-service/)
*   **Verification Status:** **Corroborated** | **Confidence:** High

---

## 6. Security & tenant governance [V]

### 6.1 Prompt Injection Security Patterns [V]
*   **Dual-LLM / Quarantined Extractor Pattern:** Formalized in research (*Design Patterns for Securing LLM Agents*, arXiv:2506.08837; *CaMeL: Defeating Prompt Injections by Design*, arXiv:2503.18813). Untrusted input (SharePoint docs, external web crawls) is processed strictly by a sandboxed, tool-less LLM that outputs validated YAML/JSON schemas. Privileged execution paths never ingest raw untrusted text directly.  
    *Sources:* [arXiv:2506.08837: Securing LLM Agents](https://arxiv.org/abs/2506.08837), [arXiv:2503.18813: CaMeL Architecture](https://arxiv.org/abs/2503.18813)

### 6.2 Microsoft Entra Agent ID & Governance [V]
*   **Entra Agent ID:** Reached GA in 2026 as the identity boundary for enterprise AI agents, enabling role-based access control, Conditional Access policies, and non-human service principal auditing for Copilot Studio and custom agents.  
    *Sources:* [Microsoft Learn: Entra Agent ID Overview](https://learn.microsoft.com/en-us/entra/agent-id/what-is-microsoft-entra-agent-id), [Microsoft Learn: Purview DSPM for AI](https://learn.microsoft.com/en-us/purview/dspm-for-ai-considerations)

---

## 7. Strategic challenges to the RFP brief

7.1 **The "no data production" boundary is right in spirit, too absolute in letter [R]:** Check-mode must be allowed to read one published measure value from an authoritative semantic model to test metric consistency.  
7.2 **Technical inventory deferral [R]:** Capture a single `semantic_model_pointer` field per metric to bridge the definition to implementation.  
7.3 **Adoption policy [R]:** Require BCL check results at the *artifact deliverable level* (decks, readouts) rather than mandating individual query lookup behaviors.

---

## 8. Delivery plan & cut list (11-Week Schedule)

| Weeks | Focus | Exit Condition |
|---|---|---|
| **1–2** | Schema (Ossie + Markdown), Context ID scheme, CI gates, broker skeleton, OTel emitting. Sealed questions locked. Arm C baselines run. Rubric piloted. | Telemetry captures citation + attribution. Baselines recorded. |
| **3–6** | Shared `enterprise`-scoped core first. Definition Council convenes week 3. Graph webhooks replace manual scanning. | 3-way metric fork closed with signed decision. |
| **6–9** | Randomised withholding runs (Arms A/B/C) across sealed questions. Tier-2 attribution on 100% of sealed runs. | Scored, blinded results for all arms. |
| **9–11** | Analysis: A−B split intra- vs cross-LoB; cost per answer; gate pack. | Pre-registered decision rule output. |

---

## 9. Technology stack summary

| Layer | Recommendation | Primary Source Citation | Status / Notes |
|---|---|---|---|
| **Content of Record** | Markdown + Apache Ossie (OSI v1.0) YAML in Git/SharePoint | [github.com/apache/ossie](https://github.com/apache/ossie) | **Corroborated [V]** |
| **Index of Record** | Azure AI Search (Hybrid BM25 + Vector + Semantic Reranker) | [Microsoft Learn Azure AI Search](https://learn.microsoft.com/en-us/azure/search/) | **Corroborated [V]** |
| **Estate Retrieval** | M365 Copilot Retrieval API / Foundry IQ remote SharePoint source | [Microsoft Learn Copilot Retrieval API](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/overview) | **Corroborated [V]** (Delegated auth limit) |
| **Context Broker** | Azure Functions + API Management over MCP (`2026-07-28`) | [spec.modelcontextprotocol.io](https://spec.modelcontextprotocol.io) | **Corroborated [V]** (Stateless protocol update) |
| **Telemetry** | OpenTelemetry GenAI Semantic Conventions (`open-telemetry/semantic-conventions-genai`) | [github.com/open-telemetry/semantic-conventions-genai](https://github.com/open-telemetry/semantic-conventions-genai) | **Conflicting Evidence [V]**: Zero attributes stable in 2026; requires pin. |

---

## 10. OpenTelemetry & Model Strategy Reality Check [V]

### 10.1 OpenTelemetry GenAI Semantic Conventions Instability [V]
*   **Stage 1 & 2 Verification:** As of September 2026, **zero `gen_ai.*` attributes in OpenTelemetry have reached Stable status**. In June 2026, all GenAI conventions were moved into a dedicated repository (`open-telemetry/semantic-conventions-genai`), which lacks tagged versioned releases. All attributes remain in *Development* status and are subject to breaking changes.  
    *Sources:* [OpenTelemetry GenAI Semantic Conventions Repo](https://github.com/open-telemetry/semantic-conventions-genai), [Dev.to: OpenTelemetry GenAI Conventions Zero Stable Attributes in 2026](https://dev.to/mr_manushukla/opentelemetry-genai-conventions-zero-stable-genai-attributes-in-2026-and-what-to-ship-anyway-1l5h)
*   **Verification Status:** **Corroborated** | **Confidence:** High  
*   **Recommendation [R]:** Pin semconv version explicitly and build a normalisation span processor at export.

### 10.2 Model Retirement Risk [V]
*   **Stage 1 & 2 Verification:** Microsoft Copilot Studio retired GPT-4o for generative orchestration in late October 2025, replacing it with GPT-4.1 as default. This proves vendor model deprecation is an active risk.  
    *Sources:* [Microsoft Learn: What's New in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/whats-new)

---

## 11. Industry failure rates: MIT NANDA Study [V]

### 11.1 MIT NANDA "GenAI Divide" Report [V]
*   **Stage 1 & 2 Verification:** MIT's Project NANDA (*GenAI Divide: State of AI in Business 2025*) reported that **95% of enterprise GenAI pilots failed to deliver measurable P&L impact** ($30–40B spent across ~300 enterprise deployments). S&P Global reported AI project abandonment rates rose to **42%** in 2025; Gartner predicted >**40%** of agentic AI projects will be cancelled by end-2027.  
    *Sources:* [MIT NANDA GenAI Divide Study (2025)](https://agentmodeai.com/the-mit-genai-pilot-failure-claim/), [Fortune / Yahoo Finance Report on MIT NANDA](https://finance.yahoo.com/news/mit-report-95-generative-ai-105412686.html), [Behind the SLA: GenAI 95 Percent Problem](https://behindthesla.com.au/resources/guides/genai-95-percent-problem)
*   **Core Finding:** The primary cause of perceived failure was **lack of documented pre-deployment baselines**, not model capability failure. This validates the RFP's insistence on baseline measurement prior to curation.

---

## Appendix A & B — Populated Risk Log & Open Decisions

*(Refer to Section 8 & Section 14 of the formal Due-Diligence Report for the complete 10-point vendor questionnaire and risk matrix).*
