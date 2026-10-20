# Test proposal — Customer lenses in Databricks Genie

**Status:** Proposed; not implemented or validated  
**Date:** 20 October 2026  
**Project:** CX Service Experience Mapping  
**Purpose:** Test whether customer Segment and Interaction Mode context helps Genie identify defensible journey-based measurement and improvement opportunities that conventional operational reporting misses.

## Decision question

Can a governed Genie Agent combine existing service, operational and customer context to identify at least one useful, evidence-backed journey opportunity without inventing customer identities, modes, causal explanations or unsupported joins?

This is a bounded test of the existing CX Service Experience Mapping architecture, not a new architecture or production agent rollout.

## Architectural alignment

Use the existing relationship model:

Customer / Segment ↔ Interaction Mode ↔ Service / Service Offering ↔ Operational evidence ↔ Experience measurement

- Service Catalogue is the canonical service spine (198 distinct offerings in the current working baseline).
- Salesforce Product2 is operational configuration; Customer Intelligence ServiceName/ServiceNameGrouped provides forward classification. Mapping to catalogue offerings remains unconfirmed unless evidenced/reviewed.
- Website context is a separate evidence layer, not an automatic service match.
- Modes: Access; Apply; Comply; Have a Say; Report an Issue; Take Part; Transact.
- Segments are design lenses, not deterministic customer classifications. Resident: Young Adults Living Independently, Young Families, Midlife Adults, Older Residents, Young People Living at Home. Non-resident: Business Operators, Landlords, Commuters, Occasional Visitors, Out-of-state Visitors.
- Do not confuse a mode with a channel, CRM category, service offering, or journey stage. A journey may span multiple offerings and modes.
- The existing Measurement Opportunity Register remains the output and human-review surface.

## Sample and scope

Start with parking permits, including residential parking where suitable, using existing Databricks permit/application history and Salesforce support evidence. Genesys and GA4 may be used only where access, coverage, comparability and linkage permit. Do not assume person-level joins across these systems.

Investigate: whether support following an application indicates an observable gap in progress information, outcome communication or another part of the Apply experience. This is a hypothesis, not a confirmed customer need.

## Minimal customer-context contract

For each analysis expose, where available:

| Field / concept | Requirement |
| --- | --- |
| Canonical service / offering | Source ID, mapping method and validation status; allow unmapped |
| Operational service | Actual source-system identifier and source |
| Segment lens | Approved segment definition; observed evidence vs hypothesis vs unknown; never infer lifestyle segment from permit type alone |
| Mode | Controlled label; mapped service mode versus inferred interaction mode; classification rule and confidence |
| Journey evidence | Application/case events, timestamps, link method, stage or transition where supported |
| Metric | Business question, numerator, denominator, grain, period, filters and source |
| Provenance | Dataset/view, mapping version, classification version, missing coverage and limitations |
| Interpretation | Observed fact, inference, competing hypotheses, validation required |

Use read-only governed Unity Catalog views and approved access. Keep raw personal information and customer extracts out of GitHub.

## Genie configuration

Begin with **one** domain-specific Genie Agent (formerly Genie Space), not separate segment/mode/journey agents. Use existing Unity Catalog data, curated views, explicit join guidance, metric definitions, example SQL/trusted assets, business terminology and benchmark questions. Use agent mode for multi-step analysis if available in the organisational environment. Availability and permissions must be confirmed in Databricks.

Classify free-text evidence upstream using reviewable SQL rules or approved AI functions if necessary; do not rely on ad hoc, untraceable mode classification in every answer.

General interpretation contract:
1. Separate observed facts, calculated metrics, inferred customer expectations, hypotheses and recommendations.
2. State when a segment is unknown; do not invent segment membership.
3. Preserve mode definitions and distinguish mode from stage or activity.
4. Do not assert that a post-submission contact is caused by a particular application without validated linkage.
5. Surface counter-explanations, coverage limits, numerator/denominator and confidence.
6. Prefer cross-service recurring patterns over a single anecdote, but never claim cross-service evidence if the sample is only parking.
7. Produce candidate Measurement Opportunity Register entries, not autonomous service decisions.

## Three-week execution

### Week 1 — Evidence readiness
- Inventory existing permit/application history, Salesforce cases, available keys, service mappings and relevant dates.
- Verify joins, duplicate handling, coverage and safe analytical grain.
- Establish baseline metrics, including applications, processing time and support per 100 applications only when numerator and denominator are compatible.
- Record unknown segment coverage and unsupported linkages.

**Output:** one validated sample dataset/view and evidence coverage log.

### Week 2 — Genie customer lens
- Configure one Genie Agent with approved views, seven-mode vocabulary, segment definitions and source limitations.
- Add reviewed SQL examples for baseline metrics and relationships.
- Run benchmark questions against expected SQL/results.
- Compare results with conventional operational reporting.

**Output:** reproducible answers and an initial set of explicitly caveated hypotheses.

### Week 3 — Human validation
- Review findings with CX/service SMEs; inspect alternative explanations and missing evidence.
- Assess whether the lens changed the interpretation or identified a better next question.
- Prepare 3–5 candidate Measurement Opportunity Register entries where evidence supports them; fewer is acceptable.
- Decide whether to iterate, expand to another service, or stop.

**Output:** review-ready opportunity entries, benchmark results and a go/no-go recommendation.

## Benchmark questions

1. Which parking permit services have the highest support contacts per 100 applications, using a valid linked or clearly caveated activity base?
2. What is observable about support contacts after application submission, and what cannot be inferred about cause?
3. Which customer expectations relevant to the Apply mode may be unmet, and what evidence would validate them?
4. Can resident and non-resident segments be compared reliably? If not, what coverage or attribute is missing?
5. What journey-stage or mode-transition signals are supported by event timestamps and linked records?
6. Does the customer lens produce a decision-relevant opportunity that operational counts alone would not reveal?

## QA and success criteria

- **SQL QA:** key uniqueness, join cardinality, dates, filters, denominator compatibility and baseline reconciliation against independently reviewed SQL.
- **Semantic QA:** exact seven modes; no fabricated segment membership or service mappings; clear distinction between service mode and inferred interaction.
- **Interpretation QA:** facts vs hypotheses; competing explanations; no unsupported causal claims; evidence gaps visible.
- **Human QA:** CX/service SME reviews opportunity wording, plausibility and next validation action.
- **Decision value:** at least one candidate opportunity is judged materially more actionable or better framed than the operational-only view, with traceable evidence and a practical validation owner.
- **Stop conditions:** unreliable joins, insufficient coverage, privacy/access barriers or no demonstrated additional decision value.

## Measurement Opportunity Register handoff

For each accepted candidate record: service/offering, source mapping status, segment lens and evidence status, mode or transition, customer expectation, observed signal, metric definition, source/grain/period, coverage, interpretation and alternatives, confidence/status, next validation action, likely owner and potential channel/service-design implication.

Use existing statuses: Confirmed; Working hypothesis; Open; Not represented; Future signal.

## Ownership and authority

Organisational service, customer and operational sources remain authoritative. Confluence/Jira/Bitbucket govern organisational decisions, delivery and implementation as applicable. This GitHub repository is a controlled project mirror for the mapping method and safe test design, not a repository for customer data or a replacement for organisational sign-off.

**Current decision requested:** endorsement to conduct the bounded parking-permits feasibility test, not approval for production deployment or a new agent architecture.

## Platform reference

Databricks Genie Agents documentation (October 2026): https://docs.databricks.com/aws/en/genie-agents/
