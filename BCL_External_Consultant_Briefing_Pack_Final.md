EXTERNAL CONSULTANT BRIEFING PACK

**Business Context Library**

Request for an independent approach, assessment and executive presentation



| **PURPOSE**Enable an external consulting team to understand the current Business Context Library concept, challenge the assumptions, and present a practical approach for proving and scaling it. This is a request for independent thinking, not a request to validate a predetermined solution. |
| --- |


| **Prepared by** | Martin Krawietz, Data Analytics |
| --- | --- |
| **Audience** | Selected external AI / knowledge-management consultants |
| **Date** | 31 August 2026 |
| **Handling** | Company Confidential. Share only through approved channels. Pre-contract version: named client detail is withheld until contracting and confidentiality are in place. |
| **Version** | 3.0 — revised after internal review |
*Expected consultant output: a concise executive presentation plus supporting recommendations, assumptions, risks and an actionable roadmap.*
# At a glance
Ten facts that should shape your response. Each one is expanded later in this pack. If your proposal contradicts any of them, say so explicitly — we would rather argue about it now than in December.

| **Topic** | **What you need to know** |
| --- | --- |
| Decision point | A funded programme runs to a decision gate in the week of 14 December 2026: scale, narrow, redesign or stop. Your recommendation has to be usable at that gate, not after it. |
| What we are buying | Independent thinking, a target architecture and an operating design, plus hands-on specialist support that leaves capability behind. Not a platform, not a licence, not a managed service. |
| What already exists | A working prototype: an ingestion-and-approval engine, seven context categories, a source register, entry-level change history, a guarded people lookup and a packaged internal portal. It is a prototype operated by a specialist, not a platform. |
| Four use cases, all at once | All four use cases start in parallel. There is no first mover whose context the others inherit for free, and no sequential learning curve to lean on. Your design has to cope with that. |
| The hard part | Proving reuse across use cases — not proving that AI answers better when given more context. The second is obvious and does not justify the investment. |
| The measurement gap | Nothing currently records what context is read or reused. In our view, building that instrumentation is the first thing that has to happen, before any curation is scored. |
| Non-negotiables | Content of record stays in company-controlled storage in an open, portable format. Human approval before context is trusted. No HR, compensation, performance or personal data. |
| Where it runs | Inside our Microsoft 365 tenant. No supplier-hosted components in the critical path and no company content sent outside approved channels. |
| Exit | We must be able to operate and extend the result without you. Knowledge transfer is a deliverable with an acceptance test, not a courtesy at the end. |
| Budget | We will not publish a budget. Give us a rate model, an effort profile by role and phase, and a smaller and a larger option so we can choose the level of ambition. |
# 1. Executive request
We are exploring a Business Context Library (BCL): a governed, reusable layer of business knowledge that helps AI understand the business meaning, goals, expectations and background information. We want an external consultant to assess the concept independently and explain how they would prove, design and scale it.

| **THE QUESTION TO ANSWER**How would you approach the Business Context Library so that it becomes a trusted, maintainable and reusable enterprise capability, rather than another static repository or isolated AI pilot? |
| --- |
## What we need from you
Assess the current concept, including strengths, gaps, dependencies and risks.

Propose a use-case-led approach that demonstrates measurable value and reuse across use cases.

Recommend a target architecture and operating model without assuming a preferred vendor or platform.

Define governance for intake, approval, ownership, access, provenance, freshness, conflicts and deprecation.

Explain how business context should be represented, connected, retrieved and consumed by people, copilots and agents.

Provide a phased roadmap, deliverables, roles, effort profile, decision gates and realistic cost drivers.

Prepare and present an executive-level recommendation that makes trade-offs explicit.
## Important positioning


| **This engagement is** | **This engagement is not** |
| --- | --- |
| Independent orientation, challenge and design support | A request to simply implement the current prototype |
| Evidence-led and use-case-led | A generic AI strategy exercise |
| Collaborative, with knowledge transfer | Long-term dependency on consultant staff |
| Focused on a reusable context capability | A data-lake, warehouse or BI replacement |
# 2. Current BCL concept
The current concept defines the BCL as a thin, governed meaning layer. It captures the “why,” “for whom” and “what it means” behind data and operational decisions. It complements technical data models, lineage and BI assets rather than duplicating them.

| **Context domain** | **Examples** |
| --- | --- |
| Systems & Data | Source systems, datasets, semantic models, data quality context, locations and lineage references |
| Metrics & KPIs | Definitions, calculation logic, targets, assumptions, changes and known limitations |
| Deliverables | Reports, dashboards, studies, readouts, obligations and ownership |
| Clients | Programs, terminology, products, pricing, SLAs, business model and key context |
| Process & Decisions | Workflows, decision rights, historical decisions, rationale and exceptions |
| People & Know-how | SMEs, ownership and knowledge currently held in people’s heads |
| External Data | Client websites, market research and approved external intelligence |
## How the library is meant to be used
This matters more than the content model, because a library that is not used has no value regardless of how well it is built. Your recommendation should be shaped by it.

The primary way the library is consumed is inside AI-assisted work, not by browsing. It should be available as context to the tools our analysts already use, so an answer can draw on it without anyone visiting a site.

It should work in two directions: as a source of business meaning, and as a check. Work products should be testable against the library so that contradictions and assumptions that no longer hold are surfaced before the work is published.

Using it should maintain it. Someone who finds something wrong, missing or stale should be able to correct or flag it as part of normal work, not through a separate governance process. Contribution is part of consumption, not an additional duty.

A portal or browsing view is a supporting surface for review and approval. It is not the point, and we do not expect people to read the library like an encyclopaedia.

Retrieval must be economical. A design in which every question scans the whole library is not acceptable. Explain how relevance is narrowed so that cost stays proportionate to the question as the library grows.

Existence is not value. Use is value. We would rather have a smaller library that is genuinely embedded in how work happens than a large one that is consulted occasionally.

| **THE ADOPTION TEST**At the decision gate we will not ask how many entries exist. We will ask which decisions drew on the library, how often, by whom, and what it cost to keep it current. Your plan has to make that answerable, and it has to say how use becomes part of normal workflow rather than an optional extra step. We have deliberately not decided whether use should be encouraged or required — tell us what you would do and why. |
| --- |
## Scope boundary — what the library does and does not do
We have taken a position on this and we want it challenged if you think it is wrong.

In scope is business meaning. When a number moves, the library should help answer whether that is good or bad, by how much, whether it supports or contradicts a stated goal, and what plausibly caused it.

In scope as knowledge, not as automation: where the data for a line of business resides and how it is connected, how a deliverable was implemented, who built it, which design decisions were taken and where the documentation sits.

Out of scope is producing data. The library is not a query engine, not a reporting tool and not an agent that retrieves or loads data. It explains numbers; it does not generate them. We are aware this is technically achievable and we consider it a distraction from the business knowledge.

For metrics, the business definition comes first and the calculation logic second. Where the two disagree, agreeing the business definition is the priority; the implementation discussion follows from it.

Deliberately deferred in this phase: a deep technical inventory of tables, transformation code and reporting workspaces. A separate major data-platform programme is changing that layer, and we do not want effort spent describing what is about to be replaced. We expect to revisit this at the decision gate — say so if you think that is the wrong call, and what we lose by waiting.

| **CORE DESIGN PRINCIPLE**Explain the business once, govern it, connect it to its sources, and reuse it across questions, clients and AI use cases. |
| --- |
# 3. Current-state snapshot
The current work is an early product concept and prototype, not an enterprise platform. The consultant should validate the facts below during discovery and distinguish what exists, what is partial and what is only proposed.

| **Capability** | **Current** **indication** | **What the consultant should test** |
| --- | --- | --- |
| Ingestion and approval | Working, but specialist-operated. A differential source-to-draft engine with per-source state fingerprints, a triage rubric for bulk crawls, a clarification loop that asks the contributor rather than guessing, a hard human approval gate, an enforced confidentiality filter and an automatic change log. It runs today as a developer-operated tool, not as a service, and there is no self-service intake. | Control effectiveness, usability, extensibility, auditability and platform fit. |
| Source coverage and formats | The source estate spans structured and unstructured content: documents, presentations, spreadsheets, semantic models, sites, mail and meeting records. The prototype reads some of it directly and converts the rest. We treat format handling as a solved problem in principle rather than a boundary of ambition. | We require consumption of structured and unstructured content of any type. Propose the acquisition and conversion strategy, the coverage it achieves, and what that coverage costs to run. |
| Differential scanning | Change detection works per source. There is no scheduler: every scan is triggered by hand, and at least one source registered as weekly is currently several weeks past its cadence. Drift reporting exists in the tooling but has not been operated in earnest. | Freshness model, cadence, error handling, retries, coverage and cost. |
| Conflict and gap resolution | Open questions are captured per entry and block promotion to approved. There is no automated duplicate or conflict detection across entries. A live example: one core commercial metric is currently defined three different ways in three places, with no agreed parent definition. | Resolution workflow, ownership, semantic consistency and escalation. |
| Consumption | A retrieval helper exists, and an internal portal is built and packaged but not yet deployed. Critically, nothing records what is read or reused. Enterprise channels (Teams, Copilot, agents) are undecided. | User journeys, cited answers, permission trimming, agent patterns and adoption. |
| Governance rails | Human approval, provenance, confidentiality filtering, change history and lifecycle states are defined and partly enforced in code. Enforcement covers tool-driven edits only; edits made by hand are not scanned. Ownership is informal and concentrated in a small number of people. | Whether controls are sufficient for enterprise use and how they map to existing governance. |
| Technology platform | Undecided. Current components are prototypes and should not constrain your recommendation. We are not committed to any product, vendor or storage pattern. | Architecture options, buy/build choices, integration and exit strategy. |
## Known source landscape
Documents, presentations, PDFs and spreadsheets

SharePoint sites, folders and repositories

Power BI workspaces, semantic models, reports and inventories

Emails, Teams messages and meeting transcripts, subject to access and privacy controls

Approved websites and market research

Structured SME interviews and business validation
## Known shortcomings of the prototype
Stated in general terms, so that no part of your proposal assumes a capability we do not have.

Operation depends on specialists. Scanning, intake and clarification all need someone who knows the tooling. There is no scheduled, monitored or self-service operation.

Reuse is not observable. Nothing records which context is retrieved, by whom, or for which question, so the central claim of the programme cannot currently be evidenced at all.

Semantic consistency is not enforced. Duplicates, overlaps and competing definitions are found by people rather than by the system.

Consumption channels are not established. A portal exists but is not in general use, and the library is not embedded in the workflows where analysis actually happens.

Coverage of the source estate is partial. Some context categories are modelled but not yet populated, and acquisition of external content is not automated.

Ownership is informal. Stewardship rests on a small number of people rather than on a defined role with capacity.

| **THIS IS NOT A DEFECT LIST**We are not asking you to repair these points. They are characteristics of a prototype, and we have described them so that you can scope realistically — not so that you can propose fixes. If your target design makes some of them irrelevant, that is a better answer than fixing them one by one. We are looking for a coherent whole, not a remediation plan, and how you handle this list is itself part of what we are assessing. |
| --- |
## Current operating assumptions to challenge
Human approval remains required before context is treated as trusted.

Every assertion should retain provenance, ownership, confidence and freshness metadata.

Context should be reusable across use cases, not rebuilt independently per solution.

The BCL should separate business meaning from numeric data storage and reporting.

The enterprise experience should work for both human users and AI agents.

# 4. Business outcomes and proof strategy
The objective is not to maximize the number of documents or context entries. The objective is to prove that governed, reusable context improves the quality, speed, consistency and trustworthiness of business outcomes.
## Candidate use cases


| **Use case** | **Illustrative business question** | **Why it is useful for the proof** |
| --- | --- | --- |
| Manufactured Housing growth drivers | What drives net adds, and where should leaders intervene? | Tests multi-source reasoning and faster insight generation. |
| Large broadband client — end-to-end context | How can insights support revenue and EBITDA improvement for the client? | Tests a complex, high-value client context, knowledge scattered across teams, and ongoing maintenance. |
| Connected Living attach rate | Where is attach performance changing, and why? | Tests metric consistency, data-science interaction and role-tailored outputs. |
| Global Auto / automotive insights | How can insight and recommendations scale across dealerships? | Tests reuse at larger operational scale and onboarding of additional sources. |
*Client and account names are* *generalised* *in this pre-contract version. Specific names,* *materials* *and the sealed questions are shared after contracting and confidentiality are in place.*

## Required proof design
Before work starts, business stakeholders define a fixed set of sealed questions and the decisions those questions support.

Create a baseline without BCL assistance, including time, sources used, manual context reconstruction and quality issues.

Run the same questions with the BCL and document what context was reused, which sources supported the answer and where humans intervened.

Use business reviewers to score decision usefulness, completeness, consistency, traceability and material errors.

Measure both use-case improvement and cross-use-case reuse. A successful isolated answer is not sufficient proof of a shared foundation.

At the decision gate, recommend scale, narrow, redesign or stop, based on evidence. “Stop” is a legitimate outcome, and it may mean that our current approach is wrong rather than that the concept is. In that case we expect a recommendation on what to do instead, including buying rather than building.

| **CONSULTANT CHALLENGE**Propose a measurement design that does not reward content volume, vanity metrics or unverified AI output. State which benefits can be measured during the pilot and which require longer observation. |
| --- |
## The measurement problem you must solve
This is the part we expect to get wrong without outside help, so we are being specific about it.

Reuse is currently unmeasurable. No system records which context is read. Any measurement design that assumes read-side telemetry already exists is not implementable in week one; building it is probably the first deliverable.

Two of the four use cases sit in the same line of business. Reuse between close neighbours is comparatively easy; reuse between distant use cases is the real test. Report the two separately instead of blending them into one average.

All four use cases start at the same time, so nobody inherits context for free. Tell us how you would still separate context that was genuinely reused from context a team would have created anyway.

Volume is not evidence. Entries created, documents ingested, tags applied and tokens consumed must not appear as success measures. Marking an entry as reusable is not reuse.

Include the cost side. What did it cost to capture and maintain the context, and did the second, third and fourth use case cost measurably less than the first?

Tell us what an honest negative looks like. If the evidence shows that reuse stays confined to closely related use cases, we want that finding stated plainly in December, not softened.

| **A WORKED EXAMPLE OF THE PROBLEM**One core commercial metric is currently defined three different ways, in three different places, with no agreed parent definition and no reliable way to tell which definition a given report used. Closing that fork once, so that all four use cases inherit the same definition and the next use case inherits it for nothing, is the kind of result we would count as evidence. Reproducing a clean definition four times in four places is not. |
| --- |
# 5. Questions the consultant must answer


| **Area** | **Required response** |
| --- | --- |
| Strategy and scope | What problem is the BCL solving? What should be explicitly out of scope? Where should the capability sit relative to data governance, enterprise search, metadata management, knowledge management and AI platforms? |
| Information model | What is the minimum viable context model? Which objects, relationships and metadata are essential? When are Markdown, graph, vector, relational and search-index representations appropriate? |
| Ingestion | How should structured and unstructured sources be discovered, prioritized, transformed and linked without uncontrolled copying? What belongs in the BCL versus remaining at source? |
| Trust and governance | How should provenance, approvals, data classification, permissions, freshness, conflicts, versioning, retention and deprecation work? |
| Retrieval and consumption | How should users and agents retrieve context with citations and permission trimming? How should context packs, RAG and knowledge-graph traversal complement each other? |
| Architecture | Which architecture options should be considered, and what are the trade-offs for security, scalability, interoperability, total cost and vendor lock-in? |
| Operating model | Who owns the product, domains, entries, review queues and platform? What work remains central and what is federated? |
| Value and scale | How do we prove reuse and business value? What conditions must be met before enterprise scale? |
| Delivery model | What should consultants deliver versus internal teams? How will knowledge transfer and capability handover be enforced? |
| Model Strategy | Single provider or multi-provider, selection criteria, switching cost, and what breaks if a provider changes terms. |
| Adoption and enablement | How does the library become part of normal workflow rather than an optional lookup? What makes use durable after your engagement ends, who owns adoption, and how is it measured? |
| Retrieval economics | How is context narrowed so that model cost stays proportionate to the question? What is the cost per answer at pilot scale and at enterprise scale, and what drives it? |
| Phasing | Would you go wider (more domains and lines of business) or deeper (more capability on a narrow scope), or both at once? What does each option require in people and elapsed time, and what does it cost us if we choose wrong? |
# 6. Required workstreams and deliverables


| **Workstream** | **Minimum** **deliverables** | **Acceptance test** |
| --- | --- | --- |
| 1. Discovery and challenge | Stakeholder map, current-state assessment, assumptions log, scope boundaries, risk register. | Findings distinguish verified facts, assumptions and recommendations. |
| 2. Use-case and value design | Sealed questions, baselines, benefit hypotheses, measurement plan, decision criteria. | Each metric has a source, owner, method and limitation. |
| 3. Context and knowledge model | Canonical domain model, metadata, taxonomy/ontology approach, relationship model, example entries. | Model supports the selected use cases without becoming use-case-specific. |
| 4. Governance and controls | Lifecycle, RACI, approval workflow, access model, audit/provenance, freshness, conflict and deprecation processes. | Controls are mapped to roles, systems and evidence. |
| 5. Architecture and integration | Option assessment, target architecture, data flows, security boundaries, integration map, non-functional requirements. | Trade-offs and open decisions are explicit; no hidden platform assumptions. |
| 6. Pilot execution design | Backlog, increments, test plan, environments, quality gates, adoption activities. | Plan can be executed by a mixed consultant/internal team. |
| 7. Operating model and handover | Product model, service catalogue, run processes, skills, documentation and transition plan. Enablement plan for user adoption and training. | Internal owners can operate the capability after handover, and the adoption plan names the workflows the library becomes part of, with an owner and a measure for each. |
| 8. Scale recommendation | Evidence summary, enterprise roadmap, cost drivers, dependencies and go/narrow/stop recommendation. | Executive decision can be made from the evidence presented. |
## Expected artifacts
Executive presentation

Detailed findings and recommendation document

Architecture diagrams and option matrix

Context model and sample records

Governance process maps and RACI

Pilot measurement framework and results template

Phased roadmap, backlog and effort/cost model

Risk, assumption, issue and dependency log

Knowledge-transfer and handover package
# 7. Architecture principles and assessment criteria
The consultant is free to recommend a different architecture. The following principles should be treated as evaluation criteria, not as predetermined implementation instructions.

| **Principle** | **What good looks like** |
| --- | --- |
| Source-aware | Context retains links to authoritative sources and does not silently become an uncontrolled copy. |
| Human-governed | AI can propose extraction, classification and resolutions; trusted publication remains governed. |
| Permission-respecting | Retrieval and consumption honor existing access rights and data classification. |
| Evidence-first | Answers expose citations, provenance, freshness and confidence where relevant. |
| Composable | The capability supports multiple models, agents, search experiences and business solutions. |
| Reusable | Context objects are shared across use cases while preserving domain-specific ownership. |
| Observable | Coverage, freshness, failures, usage, cost and quality can be monitored. |
| Portable | Content, metadata and relationships can be exported; vendor lock-in is explicit and manageable. |
| Right-sized | The design avoids building a broad enterprise ontology before the use cases prove the need. |
| Secure by design | Sensitive information is minimized, classified and controlled throughout the lifecycle. |
## Hard constraints
The principles above are open to challenge. These are not: If you believe one of them is wrong, you may propose an alternative — but label it clearly as a deviation and state what it costs, including intellectual-property ownership and exit terms.

The content of record stays in an open, text-based format in company-controlled storage and must be exportable in full at any time. No content store we cannot walk away from.

The operating runtime sits inside our Microsoft 365 tenant alongside the content: entries, approval workflow, permissions and usage logging. No component operated by the consultant or its partners may sit in the critical path — if the contract ends, the library keeps working.

Model choice is open. Inference may use external model services, including non-Microsoft providers, where the service is approved for the relevant data classification and the provider's data-use, retention and regional-processing terms are acceptable. We use an approved external model service today. Recommend the model strategy you consider right, including a multi-provider pattern, and state the dependency it creates.

Human approval before context is treated as trusted. Fully automated publication without a named approver is out of scope.

No HR, compensation, performance or personal data enters the library. This is enforced today by a filter that blocks the write, and any target design must keep an equivalent control.

Person data is limited to name and reporting line, resolved through a guarded lookup. Compensation and HR fields are never surfaced into content.

Usage logging must work at role level. Do not propose designs that depend on per-person usage surveillance.

A parallel ontology and data-quality effort in Data Services covers systems, data and metric definitions. Treat its output as an input you can reuse, not as a dependency that has to complete first.

The December decision gate is fixed. Anything that cannot produce evidence by then must be sequenced after it, and said to be so openly.
## Architecture options to compare
Microsoft-native enterprise knowledge architecture

Independent knowledge graph plus enterprise search/RAG services

Multi-model “Corporate LLM” workspace pattern

Metadata/catalog-led approach extended with business context

Hybrid pattern combining existing enterprise services and purpose-built components

Vendor-hosted platform model, where a provider operates the capability and the interconnection of the content

For each option, provide fit, limitations, required integrations, security model, operational complexity, cost drivers, time-to-evidence, portability and a reasoned recommendation. Do not present product marketing claims as verified capability.

On the last option, be direct with us. Our stated preference is independence and portability, and the hard constraints above reflect it. We are nonetheless willing to be argued out of that position: if you believe buying into an existing platform beats building, present it as a clearly labelled alternative alongside your primary recommendation, and state who owns the intellectual property, what it costs to leave, and what we lose if the relationship ends. We will not accept it as the only answer, and a recommendation that quietly assumes it will be treated as a non-response.

# 8. Governance and operating model requirements
## Minimum lifecycle


| **Stage** | **Required control** |
| --- | --- |
| Proposed / captured | Source, contributor, classification and intended domain are recorded. |
| Draft | AI-produced content is visibly untrusted and unavailable to default production consumption. |
| In review | Named owner/reviewer resolves questions, conflicts and sensitivity concerns. |
| Approved | Entry is published with provenance, owner, version, confidence/freshness metadata and review date. |
| Changed | Material source changes trigger reassessment and retain history. |
| Stale | Overdue or contradicted content is flagged and its consumption behavior is defined. |
| Deprecated | The entry remains traceable but is removed from normal retrieval or superseded explicitly. |
## Roles to define


| **Role** | **Accountability to design** |
| --- | --- |
| Executive sponsor / steering committee | Strategic scope, funding, conflicts and scale decisions. |
| BCL product owner | Value, roadmap, adoption, prioritization and service accountability. |
| Platform / solution architect | Architecture integrity, integration and non-functional requirements. |
| Domain context owner | Authority for business meaning within a domain or client. |
| Context steward / reviewer | Quality, freshness, conflict resolution and approvals. |
| Use-case lead | Business question, workflow integration, benefit evidence and adoption. |
| Security, privacy, legal and governance | Control requirements, approvals and monitoring. |
| Engineering / operations | Pipelines, reliability, observability, incident and change management. |


| **KNOWLEDGE-TRANSFER REQUIREMENT**External specialists must work alongside named internal counterparts, document decisions and patterns in company-controlled repositories, and leave reusable playbooks rather than consultant-only knowledge. |
| --- |
# 9. Security, privacy and responsible AI
The proposal must address security and privacy as architecture concerns, not as a final compliance review. The consultant should identify where existing enterprise controls can be reused and where new controls are required.

Data classification and explicit source eligibility rules.

Least-privilege access, permission trimming and separation between contribution, review, approval and consumption.

Handling of personal, HR, compensation, legal, contractual and client-confidential information.

Prompt-injection, malicious or misleading source content, data exfiltration and unsafe tool invocation risks.

Provenance, citation integrity, reproducibility and handling of uncertain or conflicting context.

Retention, deletion, legal hold, export and deprecation requirements.

Model/provider data-use terms, regional processing, logging and monitoring.

Red-team and quality testing for hallucination, unsupported conclusions and access leakage.

The consultant must clearly label any assumption where the applicable company policy, legal interpretation or technical control has not yet been confirmed.

# 10. Consultant response format
Please structure the written response so that proposals can be compared consistently.

| **Section** | **Requested content** |
| --- | --- |
| 1. Understanding | Your interpretation of the business problem, desired outcomes and key uncertainties. |
| 2. Point of view | Your recommended approach and why it fits this situation. |
| 3. Discovery plan | Stakeholders, evidence, workshops and outputs. |
| 4. Proof strategy | Use cases, sealed questions, baselines, experiments and success measures. |
| 5. Architecture | Options, recommendation, integrations, security and non-functional design. |
| 6. Governance and operating model | Lifecycle, roles, controls, ownership and sustainability. |
| 7. Delivery plan | Phases, workstreams, milestones, dependencies and decision gates. |
| 8. Team | Named roles, relevant experience, onshore/offshore model and internal participation required. |
| 9. Commercials | Assumptions, effort, rate model, expenses, licenses, cloud/model usage and contingency. |
| 10. Risks and exclusions | Top risks, mitigations, dependencies and what is not included. |
| 11. Handover | Documentation, training, paired delivery, acceptance and transition approach. |
| 12. References | Comparable engagements and results that can be substantiated. |
## Response quality requirements
Separate facts, assumptions and recommendations.

Make trade-offs explicit.

Use simple business language for the executive summary.

Show estimated ranges and drivers rather than false precision.

Identify information still needed from us.

Avoid generic AI maturity slides unless directly tied to the BCL decision.
## Timetable


| **Step** | **Date** |
| --- | --- |
| Briefing pack issued | Monday 31 August 2026 |
| Written questions due | Friday 4 September 2026 |
| Our written answers to your questions | Tuesday 8 September 2026 |
| Written response and pricing due | Friday 11 September 2026 |
| Executive presentation | Week of 14 September 2026 |
| Information-security questionnaire returned | Within 5 business days of request |
| Decision and contracting | Week of 21 September 2026 |
| Engagement start and onboarding | From Monday 28 September 2026 |
| Programme decision gate (fixed) | Week of 14 December 2026 |
*All dates are indicative* *except* *the decision gate, which is fixed. Note what this means: from onboarding there are* *roughly eleven* *working weeks to the gate. If you believe that* *window* *is too short for the evidence we are asking for, say so in your response and tell us what you would cut.*
## Submission and commercial terms
Send the written response as PDF to the contact named on the cover, with pricing as a separate file.

Price three things separately: the assessment and recommendation; optional hands-on support through to the December gate; and a rate card by role and location, stating any annual escalation.

Show effort in days by role and by phase. Where you assume our people do the work, say which roles and how many hours per week.

Name the individuals you actually propose and the share of their time. Substitution after award requires our written agreement.

Declare any subcontracting or offshore delivery, including location, and any conflict of interest.

Bids remain valid for 90 days from submission.

An information-security questionnaire will be issued to shortlisted suppliers and must be returned within 5 business days. Open findings are resolved before award.

Costs of responding are not reimbursed, and this request creates no obligation on either side.

We will not publish a budget. Propose what you believe the work requires, with a smaller and a larger option and the difference in evidence each one buys.
# 11. Required executive presentation
Prepare a decision-oriented presentation for senior leadership. Target 12 to 15 main slides, with technical detail moved to an appendix.

| **Slide** | **Required message** |
| --- | --- |
| 1. Executive recommendation | Your recommended approach and the decision requested. |
| 2. Business problem | Why reusable business context matters and what happens if nothing changes. |
| 3. Current-state assessment | Strengths, gaps, risks and assumptions. |
| 4. Scope and boundaries | What the BCL is and is not, including where you would draw the line on technical and implementation knowledge. |
| 5. Value hypothesis | Outcomes, users, decisions and how value will be measured. |
| 6. Use-case proof design | Selected use cases, sealed questions, baseline and evidence model. |
| 7. Target experience | How business users, analysts, copilots and agents contribute and consume context, and how use becomes part of normal workflow — including the adoption plan and how the library is used to check work products. |
| 8. Context / knowledge model | Core objects, relationships, metadata and source linkage. |
| 9. Target architecture | Components, integrations, security boundaries and information flows. |
| 10. Governance and operating model | Ownership, lifecycle, controls and federated responsibilities. |
| 11. Roadmap | Phases, milestones, decision gates and dependencies. |
| 12. Team and handover | Consultant/internal roles and capability transfer. |
| 13. Cost and commercial drivers | Delivery, platform, licensing, model usage and run-cost drivers, including cost per answer and how retrieval is kept economical. |
| 14. Risks and mitigations | Highest-consequence risks and actions. |
| 15. Decision and next steps | Choices, open questions and immediate actions. |
## Presentation standard
Lead with the recommendation, not the methodology.

Use one clear message per slide.

Include architecture and operating-model visuals, not only text.

Show alternatives considered and why they were not recommended.

Demonstrate how the recommendation changes the current four use cases.

End with a clear decision and evidence required at each stage gate.

# 12. Evaluation scorecard
The following scorecard may be used to compare consultant responses. The company may adjust weightings before formal procurement. Use it as a self-check before you submit: if a criterion is not visibly addressed in your response, assume it scores zero.

| **Criterion** | **Weight** | **What will be evaluated** |
| --- | --- | --- |
| Understanding and challenge | 15% | Depth of understanding, quality of questions and willingness to challenge assumptions. |
| Use-case and value proof | 20% | Credibility of baselines, measures, business validation and reuse evidence. |
| Architecture and engineering | 15% | Practicality, security, integration, scalability, portability and option trade-offs. |
| Governance and operating model | 10% | Ownership, lifecycle, controls, sustainability and fit with enterprise processes. |
| Delivery approach | 10% | Phasing, milestones, decision gates, dependencies and risk management. |
| Team and relevant experience | 10% | Named expertise and substantiated comparable work. |
| Knowledge transfer | 5% | Paired delivery, documentation, training and handover. |
| Commercial clarity | 5% | Transparent assumptions, cost drivers and value for money. |
| Adoption and enablement | 10% | Credibility of the plan that makes the library part of daily work, and how adoption is owned and measured. |
## Red flags
A product-led answer: arriving with a named product before discovery, evidence and the context model are established. Recommending a product is legitimate; starting from one is not.

Claims that a single model, vector database or knowledge graph alone solves the problem.

No clear operating owner or freshness process.

No permission model or source-level provenance.

Success measured primarily by documents ingested, tokens consumed or chatbot usage.

Undisclosed lock-in: consultant-owned IP or environments, or a hosted dependency whose exit cost and intellectual-property ownership are not stated up front.

A roadmap that jumps to enterprise scale before use-case and reuse evidence.
# 13. Discovery information available after onboarding
Subject to confidentiality, need-to-know access and approved sharing channels, the following materials can be made available to the selected consultant.

Current BCL strategy and investment decks.

Current product and feature specification.

Current-state prototype walkthrough, repositories and architecture notes.

Use-case materials for all four use cases, including the named client and account context, released after contracting.

Sample context entries, source register, lineage references and change history.

Relevant enterprise architecture, AI governance, privacy and security guidance.

Stakeholder interviews with Data Analytics, Data Services, business partners, architecture and governance teams.

| **EXTERNAL SHARING CONTROL**Do not send source extracts, client data, meeting transcripts, personal data or internal architecture outside approved company channels. The consultant should initially work from this briefing pack and receive detailed materials only after contracting, confidentiality and access controls are in place. |
| --- |
# 14. Suggested kickoff agenda


| **Timebox** | **Topic** | **Desired outcome** |
| --- | --- | --- |
| 10 min | Business objective and executive decision | Shared understanding of the decision this engagement must enable. |
| 15 min | Current concept and prototype | Distinguish current capabilities from ideas and gaps. |
| 20 min | Use cases and proof expectations | Agree candidate use cases, business reviewers and sealed questions. |
| 15 min | Architecture and governance landscape | Identify constraints, dependencies and required stakeholders. |
| 10 min | Consultant initial point of view | Hear hypotheses, challenges and option framing. |
| 10 min | Deliverables, working model and handover | Align on outputs, paired team and company-controlled documentation. |
| 10 min | Open questions and next actions | Confirm evidence requests, owners and immediate actions. |
# Appendix A. Internal source basis
This briefing pack was synthesised from internal material in the following categories. Individual documents are released selectively after contracting, through approved channels only.

Strategy and investment material for the Business Context Library.

The current product and feature specification.

The Phase 1 programme plan, role model, milestone plan, RACI and risk log.

Prototype documentation, the source register and sample context entries.

Working notes from use-case scoping sessions with the business.

Review material from the Data Analytics leadership forum.

Note: this document deliberately converts internal material into a consultant challenge rather than a solution brief. Where it states a current-state fact, that fact has been verified against the working prototype and the programme plan as at the date on the cover. Where it states an assumption, it is labelled as one and we expect you to test it.
# Appendix B. Consultant assumptions log


| **ID** | **Assumption** | **Impact if false** | **How you will validate** | **Owner / evidence** |
| --- | --- | --- | --- | --- |
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |
| 6 |  |  |  |  |
| 7 |  |  |  |  |
| 8 |  |  |  |  |
# Appendix C. Open decisions


| **Decision** | **Options considered** | **Decision criteria** | **Recommendation** | **Decision owner** |
| --- | --- | --- | --- | --- |
| Scope of pilot |  |  |  |  |
| Target platform pattern |  |  |  |  |
| Context representation |  |  |  |  |
| Governance ownership |  |  |  |  |
| Enterprise consumption channel |  |  |  |  |
| Scale / narrow / stop gate |  |  |  |  |

