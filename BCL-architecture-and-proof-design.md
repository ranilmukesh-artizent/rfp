# Business Context Library — Independent Architecture & Proof Design

**Prepared as a response basis for the BCL external consultant briefing pack (v3.0, 31 Aug 2026)**
Status of this document: working proposal. Facts verified against primary sources are marked **[V]**; assumptions requiring their confirmation are marked **[A]**; recommendations are marked **[R]**. This mirrors their stated response-quality requirement to separate facts, assumptions and recommendations.

---

## 0. Read this first: five things that change the answer

These are the findings that make this proposal different from what most bidders will submit, and different from the draft you showed me.

**0.1 — Microsoft shipped the BCL's architecture layer in June 2026. [V]**
Foundry IQ went GA as a *managed knowledge layer* that "connects structured and unstructured data across Azure, SharePoint, OneLake, and the web so agents can access permission-aware knowledge," explicitly supporting **one knowledge base connected to multiple agents**, ACL synchronisation, Purview sensitivity-label enforcement, query-time permission enforcement, agentic retrieval with LLM query planning and parallel sub-queries, and extractive results with citations. Fabric IQ adds an `ontology` item (still **preview**) that defines entity types, properties, relationships and constraints bound to OneLake data. Work IQ is the M365 work-context layer.
Source: [Foundry IQ (Microsoft Learn)](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq), [Fabric IQ (Microsoft Learn)](https://learn.microsoft.com/en-us/fabric/iq/overview), [Ontology preview (Microsoft Learn)](https://learn.microsoft.com/en-us/fabric/iq/ontology/overview)

**Consequence [R]:** building a bespoke ingestion-index-retrieval platform is now largely redundant engineering that Microsoft will commoditise underneath you. The defensible investment is the *governed meaning*, the *conflict resolution*, and the *measurement*. Buy the plumbing, build the meaning, own the measurement. Say this at the gate explicitly — it reframes the December decision from "should we build a platform" to "should we keep curating."

**0.2 — There is now an open, vendor-neutral standard for exactly their hardest problem. [V]**
Open Semantic Interchange (OSI) published its v1.0 specification on **27 January 2026** under Apache 2.0, and was **donated to the Apache Software Foundation in June 2026**, incubating as **Apache Ossie** at `github.com/apache/ossie`. It is a YAML format for metrics, dimensions, datasets, relationships and business context — including human-readable descriptions, owners, certification status and deprecation notices. Reference converters for dbt/MetricFlow, GoodData, Salesforce and Apache Polaris are merged. Working group includes Snowflake, dbt Labs, Databricks, Google, AWS, Cube, AtScale, Atlan, Collibra, DataHub, Salesforce, 60+ organisations.
Sources: [Apache Ossie / OSI guide](https://datus.ai/blog/open-semantic-interchange-osi/) (vendor blog — corroborated by [SBI](https://www.sbi-group.com/blog/open-semantic-interchange-osi), [Unwind Data](https://unwinddata.com/osi-open-semantic-interchange-guide), [AtScale press release](https://www.atscale.com/press/atscale-joins-open-semantic-interchange-open-standards/))

**Honest limit [V]:** no vendor ships native OSI import/export yet as of mid-2026; adoption is Phase 2 through 2026. And the spec covers simple aggregations well — complex derived metrics remain hard to express.

**Consequence [R]:** author the **Metrics & KPIs** domain as Ossie-shaped YAML from week one. This is the single highest-leverage decision in the design. It satisfies "open, text-based, exportable" better than prose Markdown; it makes the three-way definition fork *machine-detectable* rather than a discovery exercise; and it is the interchange format that lets you consume the parallel Data Services ontology work without depending on it finishing.

**0.3 — The M365 Copilot Retrieval API is delegated-auth only, and that breaks a batch "check mode". [V]**
The Retrieval API is available at `v1.0` (`POST /v1.0/copilot/retrieval`), security-trims per calling user, honours sensitivity labels and conditional access — but **application permissions are not supported**. Every call must carry a signed-in user's token. Also: **200 requests per user per hour**; max 25 results; **results are returned unordered**; `relevanceScore` may be absent for connector items; semantic (hybrid) retrieval is supported **only** for `.doc`, `.docx`, `.pptx`, `.pdf`, `.aspx`, `.one` — everything else falls back to lexical only; table text is extracted only from `.doc`/`.docx`/`.pptx`; images and charts are not retrieved at all; **Global cloud only** (no GCC High / DoD / 21Vianet); and a **malformed KQL `filterExpression` fails silently**, returning unfiltered results.
Sources: [Retrieval API overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/overview), [API reference](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/copilotroot-retrieval), [security & auth](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-apis-security-authentication), consolidated in [this practitioner walkthrough](https://spknowledge.com/2026/08/31/microsoft-365-copilot-retrieval-api/)

**Consequences [R]:**
- Their stated requirement to "consume structured and unstructured content of *any* type" is **not** met by the M365 semantic index. Spreadsheets, Power BI semantic model metadata, and anything image-bearing need a separate acquisition path. Say this plainly rather than treating format handling as solved.
- A nightly "scan every deliverable against the BCL" job **cannot** use this API. Check-mode must be interactive (person-triggered add-in or agent turn), or must run against the BCL's own index (which *can* be app-accessed). Most bidders will design a batch checker and discover this in week 4.
- The silent-KQL-failure behaviour is a data-exposure risk, not just a bug class. Every filter expression needs a positive test against known content in CI.

**0.4 — Re-indexing the source estate is where the compliance liability lives. [V]**
If you build your own index over SharePoint instead of using the M365 index, Azure AI Search's SharePoint indexer has real limits: document-level ACL sync is **in preview**; SharePoint groups are supported only from the `2026-05-01-preview` API; ACL changes inherited from parent scopes require an explicit refresh; there is **no support for tenants with Entra ID Conditional Access enabled**; and per-file ACL entries are capped (1,000), beyond which permissions "may not be enforced at query time." Microsoft's own guidance for building a RAG app over SharePoint is to use the **remote SharePoint knowledge source**, not the indexer.
Sources: [SharePoint indexer](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-sharepoint-online), [document-level access control](https://github.com/MicrosoftDocs/azure-ai-docs/blob/main/articles/search/search-document-level-access-overview.md), [query-time ACL enforcement](https://learn.microsoft.com/en-us/azure/search/search-query-access-control-rbac-enforcement)

**Consequence [R]:** the split below is the core architectural move — index only the BCL; reach the estate through platform retrieval.

**0.5 — Retrieval logs cannot measure reuse, and the client is right to be worried. [V/R]**
Retrieval ≠ use. The Retrieval API returns up to 25 unordered extracts and Microsoft advises passing *all* of them to the model; counting those as "reuse" inflates the number by an order of magnitude. The research literature has the right primitive: **contributive context attribution** — leave-one-out / ContextCite / Kernel-SHAP methods that measure how much each source actually changed the generated response, as distinct from what was merely retrieved.
Sources: [ContextCite (NeurIPS 2024)](https://arxiv.org/pdf/2409.00729), [AttriBoT: efficient LOO attribution](https://arxiv.org/pdf/2411.15102), [Shapley-based source attribution in RAG](https://www.alphaxiv.org/abs/2507.04480v1)

**Known limitation to disclose [V]:** all current utility-based attribution methods show **position bias** — sources appearing earlier in context score higher, even for identical duplicates. Fix by randomising pack order across ablation runs.

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
│ / Foundry IQ remote        │◄─┤  1. deterministic scope resolution          │
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

| Decision | Why | What it costs / risks |
|---|---|---|
| Markdown + YAML in git-backed SharePoint as content of record | Satisfies "open, text-based, company-controlled, fully exportable." Diffs are reviewable by humans. Git gives change history for free — replaces their prototype's bespoke change log. | Git in SharePoint is awkward; needs either Azure DevOps/GitHub Enterprise with a SharePoint mirror, or a discipline about editing through the toolchain. Their current gap — "edits made by hand are not scanned" — is closed by making CI the only write path. **[A]** they have an internal git host. |
| **Apache Ossie (OSI) YAML for metrics** | Machine-checkable definitions; canonical IDs make the three-way fork a CI failure; interchange with dbt/Power BI/Fabric ecosystems; Apache 2.0, ASF-governed, so portability is structural not promised. | Spec is v1.0 and young; no native vendor import yet; complex derived metrics may need extension fields. Mitigate by treating Ossie as the *skeleton* and keeping narrative rationale in linked Markdown. |
| Index only the BCL; reach the estate via platform retrieval | Avoids reimplementing permission trimming, sensitivity labels and information barriers — which is both hard and a compliance liability. Keeps the index tiny, so retrieval is cheap. Index is disposable: portability is preserved because nothing of record lives there. | You inherit the platform's file-type and ordering limitations (§0.3). You depend on a Microsoft API surface. Both are stated as deviations. |
| Graph as a **projection**, not a store | The BCL's relationships are hand-authored in frontmatter, so there is nothing to extract. A graph DB would add an operational component with no retrieval gain at this corpus size — and their own red-flag list calls out "a knowledge graph alone solves it." | Loses formal reasoning. If they later need constraint validation, SHACL is the standardised, stable answer; OWL reasoning is not needed at this scale. |
| **Against** full GraphRAG at this stage | Full GraphRAG indexing was reported around **$33,000** for a single 5 GB corpus in early 2024, with LLM entity extraction consuming ~58% of indexing tokens. LazyGraphRAG brings indexing to **parity with vector RAG (0.1% of full GraphRAG)** and global queries up to **~700× cheaper**, but adds **2–8 s** query latency. The value shows up on global/synthesis queries over corpora with dense unauthored entity structure — not a curated library of a few thousand entries. | Gives up some multi-hop synthesis quality. Revisit at the gate if sealed-question failures are predominantly "no single entry holds the answer." <br>Sources: [LazyGraphRAG (Microsoft Research)](https://www.microsoft.com/en-us/research/blog/lazygraphrag-setting-a-new-standard-for-quality-and-cost/), [GraphRAG in production](https://tianpan.co/blog/2026-04-09-graphrag-production-when-vector-search-hits-ceiling), [cost analysis](https://medium.com/graph-praxis/the-graphrag-cost-cliff-how-33-000-became-33-in-eighteen-months-be1b0fbe37e4) |
| Broker as the single retrieval path, exposed over MCP | One place to enforce token budgets, one place to emit telemetry, one place to apply the citation contract. MCP is GA in Copilot Studio with runtime tracing that shows which server and tool were invoked. | MCP churn is real: the **2026-07-28** spec is the largest revision since launch — stateless transport, governed extensions, OAuth/OIDC alignment, CIMD client registration — and is **not fully backward compatible**. Pin the version; budget for one migration.<br>Sources: [MCP GA in Copilot Studio](https://www.microsoft.com/en-us/microsoft-copilot/blog/copilot-studio/model-context-protocol-mcp-is-now-generally-available-in-microsoft-copilot-studio/), [MCP roadmap, Aug 2026](https://blog.modelcontextprotocol.io/posts/mcp-roadmap/) |

---

## 3. The measurement design (the part they said they expect to get wrong)

This is where the engagement is won or lost. Their scorecard weights "Use-case and value proof" at 20% — the highest single weight.

### 3.1 The problem, stated precisely

Four use cases start simultaneously, so there is no natural control group and no first mover whose context others inherit. Nothing currently records reads. And the client has ruled out volume metrics. So: **you cannot measure reuse observationally. You have to design an experiment.**

### 3.2 The instrument: stable Context IDs that travel into work products

Every approved entry gets a short, human-visible, immutable ID with a version: `BCL-MET-0142 v3`.

Three properties make this the load-bearing design choice:

1. **The retrieval contract requires citation.** The broker instructs the model to cite the IDs it used; the response is parsed for them. This is cheap and always on.
2. **The ID goes into the deliverable.** When an analyst's deck or memo carries `BCL-MET-0142 v3`, reuse becomes observable **in artifacts**, not only in logs. This survives someone bypassing the agent entirely — which is the failure mode that makes a broker-only telemetry design measure nothing.
3. **Versioned IDs let you answer the worked example.** "Which definition did this report use?" becomes answerable by inspection, permanently. This alone closes the gap the brief describes as having "no reliable way to tell."

### 3.3 Three tiers of evidence, increasing rigour and cost

| Tier | What it measures | Method | Cost | Coverage |
|---|---|---|---|---|
| **1. Citation telemetry** | Claimed use | Parse IDs from responses; OTel spans; role-level aggregation | Negligible | 100% of agent traffic |
| **2. Contributive attribution** | Load-bearing use | Leave-one-out ablation: regenerate with entry *X* removed, score change in answer. Randomise pack order to control position bias. | ~2–4× inference per sampled answer | 100% of sealed-question runs; 10–20% sampled production |
| **3. Decision register** | Decisions influenced | One-click capture in the workflow surface: named decision ← entry IDs ← reviewer sign-off | Human minutes | Every pilot decision |

Tier 2 is what converts "it was retrieved" into "removing it would have changed the answer." That is the honest definition of reuse, and it directly answers *"marking an entry as reusable is not reuse."*

**Bonus [V]:** the same attribution machinery is a prompt-injection detector. ContextCite surfaced the injected source as most influential in **90 of 91** successful attacks. One instrument, two jobs — worth a line in the exec deck.

### 3.4 The causal design: randomised context withholding

Because all four use cases start together, create the control condition rather than looking for one.

Classify every candidate entry at authoring time with a required `scope` field: `enterprise` | `lob` | `client`. Then, for each sealed question, randomise the session into one of three arms:

- **Arm A — full BCL:** all in-scope context, including `enterprise`-scoped shared entries.
- **Arm B — local only:** identical pipeline, but `enterprise`-scoped entries withheld.
- **Arm C — no BCL:** baseline, current working method.

Then:

| Comparison | What it identifies |
|---|---|
| **A − B** | The causal value of **shared, cross-cutting context**. This is the reuse claim, measured directly. |
| **B − C** | The value of **local, use-case-specific context**. |
| **A − C** | Total programme value (what a naive pilot would report as "the result"). |

Report **A − B separately for intra-LoB pairs (Manufactured Housing ↔ Connected Living) and cross-LoB pairs (Housing/Living ↔ Broadband, ↔ Auto)**, exactly as they demanded, never blended.

This is a within-question ablation. Each sealed question is its own control, which removes between-team confounding — the thing that makes parallel starts look unmeasurable. It is standard practice in retrieval research applied to a business question, and it is implementable in week one because it needs no historical telemetry.

### 3.5 Scoring, and the reliability of the scorer

- Business reviewer panel scores: decision usefulness, completeness, consistency with stated goals, traceability, material errors. Blinded to arm.
- Rubric piloted in **week 2** on ~10 questions; measure inter-rater agreement (Cohen's/Krippendorff's) *before* committing the question count. If agreement is poor, the rubric is wrong, not the reviewers.
- LLM-as-judge only as a **pre-screen and a scale multiplier**, never as the gate evidence, and with a **different model family from the generator** to avoid self-preference. Published agreement between LLM judges and human panels on enterprise RAG dimensions is reasonable but dimension-dependent — strong on hallucination and identifier integrity, weaker on nuanced quality. Validate on a stratified sample every run.
  Sources: [Case-aware LLM-as-judge for enterprise RAG](https://arxiv.org/html/2602.20379v1), [Principles and guidelines for LLM judges (SIGIR/ICTIR 2025)](https://arxiv.org/pdf/2606.29033)
- **Fix the number of sealed questions after the week-2 rubric pilot.** Working assumption **[A]**: 8–12 per use case, 3 reviewers each, ~40 total across 3 arms ≈ 120 scored runs. Statistical power comes from the paired design, not from volume — but four questions per use case cannot detect anything, and that must be said out loud if the calendar forces it.

### 3.6 The cost side

| Metric | How captured |
|---|---|
| Capture cost per approved entry | Engineer hours + SME hours, logged per entry at approval |
| Maintenance cost per entry per month | Re-review time, drift-triggered rework |
| Marginal onboarding cost, UC1 → UC4 | Total hours to first credible answer, by use case, in start order |
| Cost per answer | Retrieval calls + query-planning tokens + synthesis tokens + cache hit rate, per answered question |

### 3.7 Pre-registered decision rule (proposed — fix before evidence collection)

Offer this in the proposal. Nobody else will, and it is the single strongest signal on "willingness to challenge" and "honest negative."

| Evidence pattern | Recommendation |
|---|---|
| A−B positive and material on **cross-LoB** questions, and marginal onboarding cost falls materially UC1→UC4 | **Scale** |
| A−B positive on **intra-LoB** only, indistinguishable from zero cross-LoB | **Narrow** — fund as LoB-level governed glossaries, not an enterprise layer. Stop calling it an enterprise capability. |
| A−C positive but A−B ≈ 0 across the board | **Redesign** — the value is context assembly, not shared context. Reallocate to workflow integration. |
| A−C ≈ 0, or maintenance cost per entry exceeds measured benefit | **Stop**, with a buy recommendation (Foundry IQ + Fabric IQ ontology when it leaves preview) |

**Say this in the response:** if the December evidence shows reuse confined to close neighbours, we will write "Narrow" on slide 1. That is the honest negative they asked for, committed to in advance.

---

## 4. The conflict engine — three layers, and honesty about the third

Their prototype has no automated duplicate or conflict detection. The temptation is to solve it with embeddings and an LLM. That fails, and there is a documented precedent.

**Uber's uMetric [V]:** working with 30+ teams and 10,000+ metrics, they found popular metrics spawn **10× to 100×** near-duplicate instances with misleading names. They built algorithmic deduplication — and reported that no amount of algorithmic refinement resolved the questions that actually block standardisation: *where is the scope of "one business logic"?* Should completed trips mean the same thing for rideshare and delivery? Which source is production? They had to introduce an organisational forum with decision rights.
Source: [The Journey Towards Metric Standardization (Uber Engineering)](https://www.uber.com/en-IN/blog/umetric/)

**Airbnb's Minerva [V]:** a four-year programme; "crossed the chasm" to company-wide recognition as single source of truth around 2020; succeeded partly because of an external forcing function (IPO-driven data-quality pressure); definitions live in git with peer review, automated testing and CI/CD; a staging environment exists specifically so definitions can change without breaking critical metrics; and adoption showed **network effects** — each added metric lowered the activation energy for the next team.
Sources: [How Airbnb achieved metric consistency at scale](https://medium.com/airbnb-engineering/how-airbnb-achieved-metric-consistency-at-scale-f23cc53dea70), [How Airbnb standardized metric computation at scale](https://medium.com/airbnb-engineering/how-airbnb-standardized-metric-computation-at-scale-9afe6695b486)

**The design that follows:**

**Layer 1 — deterministic, in CI (build fails).** This is the layer everyone skips and it does most of the work.
- Canonical metric ID uniqueness across `approved` entries.
- An entry may not reach `approved` if another `approved` entry claims the same canonical ID **without** a declared `supersedes` or `parent_of` relation.
- `scope` is a **required** field. This turns Uber's unanswerable question into a mandatory authoring decision: is this definition enterprise-wide, LoB-specific, or client-specific? You do not resolve the fork by merging; you resolve it by forcing the scope declaration.
- PII **whitelist**, not blocklist: frontmatter may contain only `person.name` and `person.reports_to`, resolved through the guarded lookup. Any other person-shaped field fails the build. Whitelists fail closed; regex blocklists fail open.
- Layered on top: Purview sensitive information types and trainable classifiers on the write path, so classification is not maintained in bespoke regex.

**Layer 2 — probabilistic candidate detection (produces a queue, never a merge).**
Blocking on names and aliases → embedding similarity → candidate pairs → entailment/contradiction classification by LLM → typed queue: `duplicate` / `contradiction` / `legitimate scoped variant`. Output is always a human queue item. No auto-merge, ever.

**Layer 3 — the definition council (organisational).**
A standing 45-minute weekly forum, named decision rights, published decisions as `decisions/*.md` entries in the library itself. Starts **week 3**, not week 9. This is the layer that closes the three-way metric fork, and it is not an engineering deliverable.

**State the limit:** closing one core metric fork by December is achievable. Reaching enterprise-wide semantic consistency is not, and any bidder implying otherwise should be disbelieved on the evidence of Airbnb's four years and Uber's 10,000 metrics.

---

## 5. Retrieval economics — reframed as a quality argument

They asked how relevance is narrowed so cost stays proportionate. The stronger framing: **small context packs are better for accuracy, not just cheaper.**

**Evidence [V]:** Chroma's *Context Rot* study evaluated 18 frontier models (GPT-4.1, Claude 4 variants, Gemini 2.5, Qwen3) and found performance degrades as input length grows **on all of them**, even on deliberately simple tasks with task complexity held constant. Degradation is non-uniform: semantically similar but irrelevant distractors hurt more than length alone, and — counterintuitively — models performed *better* on shuffled haystacks than on logically coherent documents across all 18 models. Independent follow-up work finds monitoring recall on long transcripts dropping materially at very large prefills.
Sources: [Context Rot (Chroma)](https://www.trychroma.com/research/context-rot), [Chroma discussion (Hamel Husain)](https://hamel.dev/notes/llm/rag/p6-context_rot.html), [long-context monitor degradation](https://arxiv.org/html/2605.12366v1)

**Consequence:** "retrieval must be economical" and "answers must be accurate" are the same requirement. That is a good slide.

### 5.1 The staged pipeline

1. **Deterministic scope resolution first — before any similarity search.** The agent endpoint carries `use_case`, `lob`, and where applicable `client`. Filter the index by facet. Most questions touch a small fraction of entries. This, not vector cleverness, is what keeps cost proportionate as the library grows.
2. **Hybrid BM25 + vector + semantic reranker within scope.** Practitioner reports through 2026 consistently show hybrid + reranker beating either method alone on recall by a wide margin; exhaust this before reaching for a graph.
3. **One-hop typed graph expansion, capped.** Follow only meaningful edges: metric → parent definition, metric → known limitation, metric → implementing semantic model, client → programme. Hard cap on expanded entries.
4. **Context pack assembly with a hard token budget**, stable ordering, static content first.
5. **Optional:** Azure AI Search *agentic retrieval* (GA in the `2026-04-01` REST API; knowledge bases, LLM query planning, parallel sub-queries, semantic reranking, citations, and an execution activity log; also exposed via an MCP endpoint). Query planning and answer synthesis bill Azure OpenAI tokens, so switch it on per query class, not globally.
   Source: [Agentic retrieval overview](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-overview)

### 5.2 Cost-per-answer model

Components to price, each separately:

| Component | Driver | Notes |
|---|---|---|
| Estate retrieval | Retrieval API calls | Free under Copilot add-on licence; **$0.10/call** on PAYG preview (no SLA) **[V]** |
| BCL index query | Azure AI Search tier + semantic ranker units | Small index; not the cost driver |
| Query planning | Azure OpenAI tokens (if agentic retrieval on) | Turn on selectively |
| Synthesis | Model input + output tokens | Dominant term |
| Attribution (Tier 2) | 2–4× synthesis on sampled answers | Sampling rate is a dial |

**The single biggest lever is prompt caching [V].** Public pricing mechanics as of mid-2026: Anthropic charges ~1.25× input for a 5-minute cache write (higher for 1-hour TTL) and **~0.10× input for reads — a ~90% discount**, minimum ~1,024 tokens; OpenAI/Azure OpenAI cache automatically on stable prefixes above ~1,024 tokens, with discounts varying by model generation (roughly 50% on older families, up to ~90% on current flagships). Caching requires a **byte-identical prefix** — a timestamp, session ID or reordered tool definition at the top of the prompt silently destroys the hit rate.
Sources: [prompt caching mechanics comparison](https://technspire.com/en/blog/prompt-caching-2026-real-cost-wins), [cost/discount breakdown](https://www.flexera.com/blog/ai/reduce-ai-api-costs) — *verify against live pricing pages at contract time; these move.*

**Design consequence [R]:** the context pack must be laid out static-first (schema, rubric, stable enterprise-scoped entries) and volatile-last (the question, session metadata). Cache hit rate becomes a monitored KPI, not an afterthought. Reported production experience puts achievable hit rates far above what most teams get — the gap between a 10% and an 80% hit rate is the difference between a defensible and an indefensible run cost.

Also: **batch APIs are ~50% cheaper** on both major providers for asynchronous work. Ingestion, extraction, drift re-assessment and evaluation runs are all batch candidates. Interactive answers are not.

---

## 6. Security, and why human approval is a security control

Their proposal treats HITL approval as governance. It is better argued as the **prompt-injection circuit breaker**, which makes it much harder to challenge.

### 6.1 The threat model

The BCL ingests SharePoint documents, PDFs, emails, meeting transcripts, and approved external websites. All of that is **untrusted input to an LLM**. An instruction embedded in a crawled client website or a forwarded email can attempt to write false business meaning into a governed library that then grounds every downstream answer. That is a supply-chain attack on the meaning layer.

### 6.2 The pattern to apply

Follow the published design-pattern taxonomy rather than inventing one:

- **Quarantined extractor.** The extraction model sees raw source text but has **no tools**, no network, and can emit **only** a strictly-typed frontmatter schema. It cannot invoke anything.
- **Privileged path never sees raw source.** The orchestration/approval path sees only validated, typed fields — a formal interface, not arbitrary text. This is the dual-LLM / action-selector pattern; the paper's own guidance is that the safest design has the agent interact with untrusted content only through a strictly formatted interface.
- **Approval as the enforcement point.** Nothing an injection produces can reach `approved` without a named human. This converts a probabilistic defence into a structural one — and it is exactly the control they already have.
- **Attribution as detection.** Tier-2 attribution flags anomalously influential sources (§3.3).

Sources: [Design Patterns for Securing LLM Agents against Prompt Injections (arXiv 2506.08837)](https://arxiv.org/abs/2506.08837), [CaMeL: Defeating Prompt Injections by Design](https://arxiv.org/abs/2503.18813), [practitioner summary](https://simonwillison.net/2025/Jun/13/prompt-injection-design-patterns/)

**Be honest [V]:** LLM-level defences (hardened prompts, adversarial training) provide *no guarantees*, and user-confirmation defences degrade as reviewers tire and rubber-stamp. Design so that the reviewer's decision is small, typed and checkable — not "read this paragraph and approve."

### 6.3 Tenant-side controls to reuse rather than rebuild

| Requirement | Control | Status |
|---|---|---|
| Permission trimming on estate retrieval | Retrieval API delegated auth — enforced by platform per request, incl. sensitivity labels, information barriers, conditional access **[V]** | GA |
| Keep high-risk sites out of grounding | SharePoint **Restricted Content Discovery** | Available |
| Keep labelled content out of Copilot grounding | Purview **DLP for Copilot** (label-based) | Available |
| Find the oversharing before you amplify it | Purview **DSPM for AI** data risk assessments; SAM Content Management Assessment | Available |
| No HR/comp/performance data | Purview sensitive info types + trainable classifiers + schema whitelist in CI | Build the whitelist |
| Agent identity, lifecycle, conditional access | **Entra Agent ID** (GA 2026), Agent 365 as agent registry, Conditional Access for agents | GA |

Sources: [Purview for Copilot](https://learn.microsoft.com/en-us/purview/ai-m365-copilot), [DSPM for AI considerations](https://learn.microsoft.com/en-us/purview/dspm-for-ai-considerations), [mitigating oversharing](https://techcommunity.microsoft.com/blog/microsoft365copilotblog/mitigate-oversharing-to-govern-microsoft-365-copilot-and-agents/4448744), [Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/what-is-microsoft-entra-agent-id)

**Uncomfortable finding to put in the deck [V]:** industry experience with Copilot deployments is consistent that oversharing is the dominant risk and is almost never malicious — it is legacy permissions, broken inheritance, "Everyone except external users" on ownerless sites, and company-wide sharing links. Copilot did not create it; it made it reachable. If the BCL is embedded into Copilot surfaces before remediation, the BCL will be blamed for an exposure it did not cause. **Sequence remediation before embedding, and say so at the gate.**

### 6.4 Role-level telemetry that is structurally incapable of surveillance

They said: no per-person usage surveillance. Do better than a policy promise — make it architectural.

- Aggregate on the Entra **job-role** claim at the broker. The user principal is used for authorisation and then **discarded before the telemetry write**.
- **k-anonymity suppression**: any reporting cell with n < 5 is suppressed, not stored.
- Query fingerprints are salted hashes, not text.
- Document this as a design property with a test in CI that fails if a user identifier reaches the telemetry store.

That is a defensible answer to a works-council or privacy review, not a paragraph of reassurance.

---

## 7. Where I would push back on their brief

Their scorecard gives 15% to "Understanding and **challenge**." These are the challenges worth making.

### 7.1 The "no data production" boundary is right in spirit, too absolute in letter

They say the library explains numbers and does not generate them, and that they consider data production a distraction. Mostly correct — and it protects the December date.

But **check mode cannot validate numbers without reading at least one number.** "Your report defines net adds differently from the canonical definition" is a prose check. "Your report's net-adds figure is inconsistent with the canonical definition applied to the same period" requires reading an authoritative measure value. Without that, check mode catches wording drift and misses the errors that actually reach executives.

**[R]** Draw the line differently: the BCL may **read a published measure value** from an authoritative semantic model to test consistency. It may **not compose new queries, join data, or produce novel figures.** Read-one-known-value is a small, auditable exception; query composition is the slope they are right to refuse.

### 7.2 Deferring the technical inventory is right, with exactly one exception

Deferring table/ETL/workspace inventory while the data-platform programme replaces that layer is sound. But their own worked example — "no reliable way to tell which definition a given report used" — cannot be closed without one thin artefact: a **pointer** from each metric definition to the semantic model and measure that implements it.

**[R]** Capture the pointer, not the inventory. One field per metric. It is cheap, it survives the platform migration (the pointer changes; the definition does not), and without it the strongest evidence available in December is unreachable.

### 7.3 "Encouraged or required" — they left this open and asked for an answer

**[R]** Required at the **artifact** level; encouraged at the **query** level.

Do not mandate that analysts consult an agent — that is unenforceable, resented, and drives shadow workarounds. Instead, make it a condition of a **defined class of deliverable** (executive readouts, client-facing insight decks, metric-bearing reports) that it carries a BCL check result and cites the entry IDs it relied on. Treat it like a lint gate on a document, enforced at the review step that already exists.

Why this works:
- It attaches to an existing control point, so it needs no new process.
- It generates the artifact-level evidence in §3.2, which is the only telemetry that cannot be bypassed.
- It creates Airbnb's **network effect** — each cited entry lowers the activation energy for the next deliverable — without a compliance culture war.
- Owner: use-case lead, with the deliverable's existing reviewer as the enforcement point. Measure: proportion of in-scope deliverables carrying a check result; proportion whose check surfaced at least one contradiction.

### 7.4 "Format handling is a solved problem in principle" — it is not, on their platform

Restating §0.3 because it belongs in the challenge section: the M365 semantic index does semantic retrieval on six file extensions, extracts table text from three, and does not retrieve images or charts at all. Spreadsheets and Power BI semantic model metadata — two named source categories in their own estate — need a separate acquisition path. This is a scope and cost item, not an assumption to wave through.

### 7.5 Eleven weeks is enough for evidence, not enough for coverage

Say what gets cut. See §8.

---

## 8. Eleven weeks: the plan, and the cut list

Onboarding from 28 September; gate week of 14 December. Roughly eleven working weeks.

| Weeks | Focus | Exit condition |
|---|---|---|
| **1–2** | Schema (Ossie + Markdown), Context ID scheme, CI gates, broker skeleton, telemetry emitting. Sealed questions locked with business stakeholders. **Arm C baselines run with humans.** Rubric piloted; inter-rater agreement measured; question count fixed. | Telemetry demonstrably captures citation + attribution on a toy corpus. Baselines recorded. |
| **3–6** | **Shared `enterprise`-scoped core first**, then per-use-case content. Conflict CI live. **Definition council convenes week 3.** Graph webhooks + Functions replace manual scanning. Purview controls verified. | The three-way metric fork has a named owner, a parent definition, and a dated decision. |
| **6–9** | Randomised withholding runs (Arms A/B/C) across all sealed questions. Tier-2 attribution on 100% of sealed runs. Check-mode add-in in the hands of real reviewers. | Scored, blinded results for every question in every arm. |
| **9–11** | Analysis: A−B split intra- vs cross-LoB; marginal onboarding cost; cost per answer; maintenance cost per entry. Gate pack. | A recommendation the pre-registered rule produces mechanically. |

### What gets cut, stated openly

- **Portal deployment.** It is built but not deployed, and it is explicitly not the point. Deploy it for reviewers only — approval queue and browsing for stewards. No general rollout.
- **Four of seven domains.** Go deep on **Metrics & KPIs**, **Clients**, **Process & Decisions**. These are what the sealed questions actually need. Systems & Data reduces to the lineage pointer (§7.2). Deliverables, People & Know-how, External Data get schemas and a handful of exemplar entries — enough to prove the model extends, not enough to claim coverage.
- **Automated external content acquisition.** Manual, curated, small.
- **Fabric IQ ontology binding.** The ontology item is still **preview** [V]; it cannot be a December-gate dependency. Design the interchange path (Ossie YAML) so it becomes a week-two task in the next phase.
- **Agent-to-agent patterns.** Out. One agent, one broker, one path.
- **Full source-estate coverage.** Scope the estate to what the sealed questions require, and report coverage honestly as a fraction.

### The real schedule risk is not engineering

SME and reviewer availability. Working estimate **[A]**: 4–6 hours per week per domain context owner, and 2–3 hours per week per business reviewer, sustained for weeks 3–11. If that cannot be committed in writing by named individuals at contracting, the evidence will not exist in December regardless of the architecture — and the correct response is to reduce the number of use cases in scope for *evidence* (while continuing content capture on all four), not to reduce the rigour of the measurement.

---

## 9. Stack summary

| Layer | Recommendation | Alternatives considered | Why not |
|---|---|---|---|
| Content of record | Markdown + YAML frontmatter; **metrics as Apache Ossie (OSI v1.0)**; git-backed, mirrored to SharePoint | Dataverse; SharePoint lists; a CMS | Not diffable, not portable in the sense they mean, not exportable as text |
| Index | Azure AI Search (hybrid + semantic reranker), BCL entries only | pgvector on Azure PostgreSQL; Qdrant/Weaviate/Milvus on ACA/AKS; LanceDB/DuckDB-VSS | A dedicated vector DB for a few thousand curated entries is over-engineering and hits their own red flag. **LanceDB/DuckDB is the interesting maximum-portability variant** — index as a file beside the Markdown — worth a spike if "must be exportable in full" is read strictly. pgvector is a reasonable fallback but you then own reranking and ACL filtering. |
| Estate retrieval | M365 Copilot Retrieval API / Foundry IQ remote SharePoint knowledge source | Azure AI Search SharePoint indexer | ACL sync in preview; unsupported with Conditional Access; Microsoft's own guidance points elsewhere (§0.4) |
| Graph | Derived projection from frontmatter, in-process | Neo4j; Ontotext GraphDB; Stardog; Fabric Graph | No extraction to do; adds an operational component with no retrieval gain at this size. Revisit with Fabric Graph at scale. If formal validation is ever needed, **SHACL** is the stable choice; OWL reasoning is not warranted. |
| Retrieval augmentation | Hybrid + rerank + one-hop typed expansion; agentic retrieval selectively | Full GraphRAG; LazyGraphRAG; LightRAG | §2.3. Revisit if sealed-question failures are dominated by multi-hop synthesis gaps |
| Consumption | Copilot Studio agent (MCP GA), Word/PPT add-in for check mode, notebook client | Custom portal as primary surface | Their own brief says the portal is not the point |
| Orchestration & broker | Azure Functions + API Management, MCP `2026-07-28` pinned | Direct agent-to-index | Loses the single telemetry and budget enforcement point |
| Freshness | Microsoft Graph change notifications → Functions → differential re-extract; delta-query reconciliation sweep; `review_by` auto-staling | Cron scanning | Their current state is manual triggering with at least one weekly source weeks overdue |
| Models | Multi-provider via gateway in their subscription; task-class routing (small for extraction/planning, frontier for synthesis/judging; judge ≠ generator) | Single provider | See §10 |
| Telemetry | OpenTelemetry GenAI semantic conventions, **version-pinned**, with a normalisation span processor; Log Analytics → Power BI | Bespoke schema | See §10 |
| Governance | Purview (DSPM for AI, DLP for Copilot, RCD, labels) + Entra Agent ID + CI gates | Bespoke controls | Reuse the tenant's controls; don't rebuild compliance |
| Evaluation | Blinded human panel as gate evidence; LLM-judge as pre-screen; Tier-2 attribution | RAGAS/DeepEval-style automated metrics alone | Automated metrics measure retrieval hygiene, not decision usefulness |

### On "Semantica" and similar open-source semantic frameworks [V]

You asked specifically. "Semantica" currently resolves to **at least three unrelated, very early-stage open-source projects** — `semantica-agi/semantica` (graph-native context/decision infrastructure, PROV-O provenance, SHACL constraints, positioned as an open-source Palantir Foundry alternative), `BitDanceLabels/semantica-layer` (v0.1.1, MIT, semantic layer + GraphRAG framework), and `Hawksight-AI/semantica` (KG intelligence framework with an MCP server). None of them is enterprise-proven at the maturity a funded programme with a fixed December gate can safely depend on for a content of record.

**[R]** Adopt the *ideas*, not the dependency. Three of their patterns are exactly right and should be implemented on stable primitives:
- **W3C PROV-O-style provenance fields** on every assertion (they already want provenance; PROV-O gives it a standard shape).
- **SHACL-style constraint validation** as CI gates (implement as schema validation now; upgrade to actual SHACL if they ever move to RDF).
- **Decisions as first-class objects** with causal links and precedent search — this is precisely their "Process & Decisions" domain, and framing decisions as retrievable objects rather than prose is a genuinely good idea.

Sources: [semantica-agi/semantica](https://github.com/semantica-agi/semantica/blob/main/README.md), [semantica-layer](https://github.com/BitDanceLabels/semantica-layer), [Hawksight-AI/semantica](https://github.com/Hawksight-AI/semantica)

---

## 10. Model strategy and telemetry standards — the honest version

### Model strategy

**[R]** Multi-provider through a gateway in their own Azure subscription, with routing by task class:

| Task class | Model tier | Rationale |
|---|---|---|
| Extraction / classification from untrusted sources | Small, cheap, **most constrained** (no tools, typed output only) | High volume; this is the injection surface |
| Query planning, reranking | Small | Latency- and cost-sensitive, low reasoning demand |
| Synthesis of answers | Frontier | Where quality is visible to executives |
| Judging / evaluation | Frontier, **different family from the generator** | Avoids self-preference bias |

**The dependency this creates, stated plainly [V]:** the switching cost is not the API shape — gateways abstract that. It is (a) **cache economics**, which are provider-specific (explicit `cache_control` breakpoints with ~90% read discounts and write premiums versus automatic prefix caching at generation-dependent discounts), so the cost model changes on switch; and (b) **the evaluation suite must be re-run**, because sealed-question results are not model-portable.

**A concrete example of "what breaks if a provider changes terms" [V]:** Copilot Studio retired GPT-4o for agents using generative orchestration in late October 2025, with GPT-4.1 becoming the default. Platform-driven model retirement is not hypothetical; it has already forced migrations in the exact product they would be building on.
Source: [What's new in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/whats-new)

**[R]** Contract for this: pin model versions per evaluation run; keep a frozen "gate model" for the December evidence so results are comparable; budget one re-baselining exercise per year.

### Telemetry standards — do not overclaim maturity

**[V]** As of August 2026, **no `gen_ai.*` attribute in the OpenTelemetry GenAI semantic conventions has reached Stable.** Every GenAI document carries status *Development*, which in OpenTelemetry's own maturity ladder sits below Alpha and states that a component "SHOULD NOT be used in production" and "MAY be removed without prior notice." The conventions were moved to a dedicated repository in June 2026 (GenAI + provider-specific + MCP), and as of late August 2026 that repository had no official release. The only Stable attributes on a GenAI span (`error.type`, `server.address`, `server.port`) are inherited from core conventions. `gen_ai.operation.name` does cover `retrieval`, and MCP tool calls now share the same trace vocabulary. SDKs disagree with each other on several widely-used attribute names.
Sources: [OpenTelemetry GenAI observability (official blog)](https://opentelemetry.io/blog/2026/genai-observability/), [what actually shipped in 2026](https://dev.to/azena-ai/opentelemetrys-genai-semantic-conventions-are-not-stable-yet-heres-what-actually-shipped-in-2026-3mke), [zero stable attributes analysis](https://dev.to/mr_manushukla/opentelemetry-genai-conventions-zero-stable-genai-attributes-in-2026-and-what-to-ship-anyway-1l5h)

**[R]** Emit OTel GenAI conventions anyway — it is the right bet and the alternative is a bespoke schema nobody can read. But: **pin the semconv version**, own a thin normalisation span processor at export so renames do not silently break dashboards, and treat the convention version as a versioned contract in the handover documentation. Do not tell the steering committee this is a settled standard.

---

## 11. What "if nothing changes" actually looks like — and the honest caveat

Their slide 2 needs this, and the temptation is to quote the famous number without the asterisk.

**[V]** MIT's Project NANDA *GenAI Divide: State of AI in Business 2025* reported that roughly **95%** of enterprise generative-AI pilots delivered **no measurable P&L impact**, against an estimated $30–40bn invested, based on ~150 executive interviews, ~350 employee survey responses and analysis of ~300 deployments. It also reported that pilots blending internal specialists with external expertise succeeded at a far higher rate than IT-only builds.

**The caveat that makes this usable rather than embarrassing [V]:** the study is preliminary, not peer-reviewed, has a small sample, and defines success narrowly — "no measurable impact" is substantially a function of pilots **not having documented pre-deployment baselines**, not of pilots failing technically. Corroborating data points: S&P Global found the share of organisations abandoning most AI initiatives rose to **42%** (from 17% a year earlier), with an average of 46% of proofs of concept scrapped before production; Gartner predicted over **40%** of agentic AI projects would be cancelled by end-2027 on cost, unclear value and inadequate risk controls.
Sources: [Fortune summary of MIT NANDA](https://finance.yahoo.com/news/mit-report-95-generative-ai-105412686.html), [methodology critique](https://agentmodeai.com/the-mit-genai-pilot-failure-claim/), [S&P Global and Gartner figures collated](https://behindthesla.com.au/resources/guides/genai-95-percent-problem)

**Turn the caveat into the argument [R]:** the dominant reason these programmes cannot demonstrate value is the absence of a documented baseline. This programme has already identified that gap and put the sealed-question baseline and read-side instrumentation first. That is not a slide about industry failure rates — it is a slide about why *this* programme is designed to be able to produce a negative answer, which is what makes a positive answer worth believing.

---

## 12. Mapping to their required response format

| Their section | Where it comes from here |
|---|---|
| 1. Understanding | §0 (the five findings), §1 (questions), §7 (challenges) |
| 2. Point of view | §2.1 thesis; §0.1 buy-the-plumbing recommendation |
| 3. Discovery plan | §1 questions; §8 weeks 1–2; Purview assessment as a discovery input |
| 4. Proof strategy | §3 in full — Context IDs, three tiers, randomised withholding, pre-registered rule |
| 5. Architecture | §2, §5, §9 |
| 6. Governance & operating model | §4 (three layers + definition council), §6 (security as architecture), §6.4 (role-level telemetry) |
| 7. Delivery plan | §8 including the cut list |
| 8. Team | Named roles; the SME commitment ask in §8 |
| 9. Commercials | §5.2 cost model; smaller/larger options driven by number of use cases carrying *evidence* vs *content* |
| 10. Risks & exclusions | §0.3 (delegated auth, file types), §0.4 (ACL preview status), §7.4, §8 cut list, §10 (standards immaturity) |
| 11. Handover | CI gates, schemas and playbooks are the handover; the definition council is an internal body from week 3, not a consultant artefact |
| 12. References | Airbnb Minerva, Uber uMetric and the published research in §3–§6 are the substantiated comparables to cite alongside your own |

### Self-check against their scorecard and red flags

| Their red flag | How this proposal avoids it |
|---|---|
| Product-led answer | No product named before §9; the content model and measurement design come first, and the primary recommendation is a *format standard* (Ossie), not a vendor |
| "A single model / vector DB / knowledge graph solves it" | Explicitly argues against a dedicated vector DB and against GraphRAG at this stage, with numbers |
| No operating owner or freshness process | Definition council with named decision rights from week 3; `review_by` auto-staling; Graph webhooks replacing manual scanning |
| No permission model or source-level provenance | Platform-enforced trimming on the estate; push-API permission metadata on the BCL index; PROV-O-shaped provenance fields; versioned Context IDs |
| Success measured by volume | Volume metrics appear nowhere; evidence is A−B quality deltas, contributive attribution, decisions influenced, and cost per entry |
| Undisclosed lock-in | Content of record is portable text under an ASF-governed spec; index is declared disposable; every platform dependency is labelled as a deviation with its cost |
| Roadmap jumping to scale | Pre-registered decision rule includes Narrow and Stop with explicit triggers |

---

## Appendix A — Assumptions log (their Appendix B, populated)

| ID | Assumption | Impact if false | How to validate | Owner / evidence |
|---|---|---|---|---|
| 1 | "Inside our M365 tenant" permits Azure services in the same tenant/subscription | Severe — telemetry, index and broker all move; likely descope of Tier-2 attribution | Question 1, §1 | Client architecture |
| 2 | Tenant is Global cloud | Retrieval API unavailable; must build own index and inherit ACL-preview limitations | Question 2 | Client IT |
| 3 | Pilot users hold Copilot add-on licences | Retrieval shifts to $0.10/call PAYG preview with no SLA; cost per answer rises materially | Question 4; admin centre | Client licensing |
| 4 | Conditional Access status permits the fallback indexer path | No fallback if Retrieval API is unsuitable | Question 3 | Client identity team |
| 5 | An internal git host exists and can mirror to SharePoint | Content-of-record design changes; CI-as-only-write-path weakens | Discovery week 1 | Client platform team |
| 6 | Domain owners can commit 4–6 h/week for weeks 3–11 | December evidence not obtainable; reduce use cases carrying evidence | Question 6, in writing at contracting | Named individuals |
| 7 | One named person can be given decision rights on the forked metric by end-October | Worked example cannot be closed; strongest single evidence item lost | Question 8 | Executive sponsor |
| 8 | Sealed questions can be locked by end of week 2 and not changed | Arms A/B/C not comparable; evidence invalidated | Week 2 sign-off | Use-case leads |
| 9 | Purview DSPM for AI assessment is available or can be run in week 1 | Oversharing risk unquantified before embedding into Copilot surfaces | Question 5 | Client security |
| 10 | Business reviewers can score blinded, and inter-rater agreement is adequate | Gate evidence rests on unreliable scores; rubric must be rebuilt | Week 2 rubric pilot | Reviewer panel |

## Appendix B — Open decisions (their Appendix C, populated)

| Decision | Options | Criteria | Recommendation | Owner |
|---|---|---|---|---|
| Scope of pilot | 4 use cases for content + 4 for evidence; or 4 for content + 2 for evidence | Statistical power vs breadth under SME constraint | 4 for content, all 4 for evidence **only if** assumption 6 holds; otherwise 4 content / 2 evidence with the cross-LoB pair preserved | Product owner + sponsor |
| Target platform pattern | Microsoft-native; independent KG + RAG; hybrid; vendor platform | Portability, time-to-evidence, exit cost | **Hybrid**: portable text of record, disposable platform index, platform-managed estate retrieval | Architect |
| Context representation | Prose Markdown; Ossie YAML + Markdown; RDF/OWL; property graph | Machine-checkability, portability, interchange | **Ossie YAML for metrics + typed Markdown for narrative; graph as projection** | Architect + domain owners |
| Governance ownership | Central stewardship; federated domain owners; hybrid | Capacity, authority to decide scope | Federated domain owners + central definition council with decision rights | Sponsor |
| Enterprise consumption channel | Portal; Copilot Studio agent in Teams/M365; add-ins; all | Where analysis actually happens | Copilot Studio agent + Word/PPT check-mode add-in; portal for reviewers only | Product owner |
| Scale / narrow / stop gate | Judgement at the gate; pre-registered rule | Credibility of the negative | **Pre-registered rule signed before evidence collection** (§3.7) | Sponsor + steering |

---

*Verification note: claims marked **[V]** were checked against the linked sources between 31 August and 3 September 2026. Microsoft product surfaces in this space are moving monthly and several capabilities cited are explicitly in preview — re-verify preview/GA status and all pricing immediately before submission, and again before contracting. Where a claim rests on a vendor or practitioner blog rather than primary documentation, the source is named so its incentive is visible.*
