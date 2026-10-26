# CX Service Experience Mapping

> **Bitbucket transition (26 October 2026):** This GitHub repository is an existing project reference, not the intended ongoing CoM source of truth. The Bitbucket-first operating architecture and controlled Strategic OS intake are staged in [CX portfolio transition](https://github.com/jose-andr-cx-projects/cx-portfolio-context/tree/main/bitbucket-transition). Before replacing any organisational records, compare against the latest approved Bitbucket/Confluence/Jira sources. No ongoing cross-platform mirroring is intended. This note does not change the project's substantive decisions or status.


## Purpose

Create a working service-experience relationship model that connects the City of Melbourne service model with customer, interaction and operational context.

The working mapping connects:

`Customer / Segment ↔ Interaction Mode ↔ Service / Service Offering ↔ Operational evidence ↔ Experience measurement`

The mapping is not an end in itself. Its strategic purpose is to test and make visible the relationships needed for a **business-led Customer Data Architecture**, while still producing usable cross-service measurement opportunities.

The Service Catalogue remains the service spine. Salesforce is an operational evidence layer. Segments explain **who**; Modes explain the **interaction type and customer expectation**. None of these layers on its own is the customer architecture.

## Strategic lens

This project is the service-experience modelling contribution to the current Customer Data Architecture priority.

It should help answer:

- which customer Segments interact with which services;
- which Interaction Modes describe those interactions;
- what expectations repeat across different services;
- where operational systems such as Salesforce provide only partial coverage;
- where customer or service relationships are not currently represented;
- what can be measured consistently across services; and
- which recurring patterns suggest a reusable CX capability rather than an isolated service fix.

The project should use practical hypotheses and visible uncertainty rather than waiting for perfect data.

It must not:

- invent relationships to make the model look complete;
- treat Service Offering as equivalent to a customer journey;
- treat Salesforce coverage as equivalent to organisational service coverage;
- define a final enterprise customer-data model;
- turn Segments into deterministic customer classifications; or
- promote proposed measures or relationships without human review.

A journey may cross several Service Offerings and Modes.

## Source authority

Organisational systems of record remain authoritative for original service data, including:

- Service Catalogue;
- Salesforce CRM;
- CX Segments and Modes source material;
- governed Voice of Customer and measurement sources.

The current Excel workbook is the working review artefact. Its protected spine is the authoritative Service Catalogue hierarchy: **198 distinct service offerings** in the current baseline. Supporting Salesforce and website context are kept as separate evidence layers rather than being automatically treated as confirmed mappings.

This repository governs the mapping method, definitions, decisions and reusable outputs. It does not replace organisational source systems.

## Current phase

**Customer architecture and measurement opportunity validation**

The current phase uses a representative sample of real services to determine whether the mapping can produce a useful measurement view across services.

The phase should answer:

> Can Service Catalogue, operational service evidence, customer Segments and Interaction Modes be connected well enough to reveal reusable customer-service relationships, measurable experience patterns and gaps that matter to a business-led customer architecture?

The work should produce something another person or team can take forward without requiring the original mapper to remain the owner.

## Phase output

The primary working output remains a **Measurement Opportunity Register**, supported by a service-experience relationship model that makes architecture gaps and reusable cross-service patterns visible.

Each candidate opportunity should make visible:

- service / service offering context;
- relevant Interaction Mode or Mode transition;
- relevant Segment lens where evidence supports it;
- customer expectation being tested;
- known or suspected experience gap;
- evidence currently available;
- possible measurement signal;
- evidence / measurement gap;
- next validation action;
- likely organisational owner or capability needed for the next step;
- status and confidence.

The register should support handoff into work such as:

- Voice of Customer / Qualtrics design;
- journey measurement;
- service analysis;
- CRM or operational data analysis;
- service design;
- Channel Strategy / Intelligent Front Door;
- prioritisation of reusable CX capabilities.

## Working principle

> **Map enough to reveal useful customer-service relationships and measurement opportunities. Do not complete the catalogue for completeness.**

A useful output is more important than exhaustive classification.

## Current working model

### Organisational structure

`Service Area → Service → Service Offering`

### Operational structure

`Salesforce Product2 current state → Customer Intelligence ServiceName / ServiceNameGrouped forward classification`

Product2 is current operational configuration, including legacy. Customer Intelligence provides the rationalised forward classification; unmatched Product2 records remain visible rather than being forced into that classification.

### Experience structure

`Initial Mode → Targeted Mode`

### Customer lens

`Resident Segment / Non-Resident Segment`

### Customer architecture extension

`Customer / Segment ↔ relationship/context ↔ Mode expectation ↔ Service / Offering ↔ observed experience ↔ evidence signal`

### Measurement extension

`Mode expectation → observed experience → evidence signal → measurement opportunity`

## Current controlled Interaction Modes

- Access
- Apply
- Comply
- Have a Say
- Report an Issue
- Take Part
- Transact

Mode describes the type of interaction, not the channel, organisational team, Salesforce classification or technology.

## Current customer Segments

### Resident

- Young Adults Living Independently
- Young Families
- Midlife Adults
- Older Residents
- Young People Living at Home

### Non-resident

- Business Operators
- Landlords
- Commuters
- Occasional Visitors
- Out-of-state Visitors

Segments are design lenses, not deterministic customer classifications.

## Measurement opportunity logic

The project should look for patterns such as:

- the same Mode expectation failing across several services;
- the same Segment experiencing a Mode differently across services;
- repeated lack of clarity, progress visibility, fairness, resolution or confidence;
- repeated channel switching, escalation or avoidable support demand;
- gaps where an important customer expectation is not currently measurable;
- service outcomes that can be compared using a common customer-experience lens.

Modes provide the benchmark or lens. They do not define the metric by themselves.

Every proposed measure still requires a clear definition, source, grain, period, denominator where relevant, collection method, interpretation and governance.

## Evidence discipline

Use these states:

- **Confirmed** — supported by an authoritative source or validated evidence.
- **Working hypothesis** — plausible mapping or interpretation requiring validation.
- **Open** — insufficient evidence.
- **Not represented** — no current source-system mapping exists.
- **Future signal** — potentially useful measurement or capability opportunity not yet approved.

Blank is valid and preferable to invented certainty.

## Scope boundary

This phase does not need to:

- classify all Service Offerings;
- design the final enterprise data model;
- create the final CX KPI framework;
- redesign Salesforce;
- automate classification;
- define permanent governance;
- create a new Strategic OS framework.

## Definition of done for the current phase

The current phase is complete when a representative set of services has been mapped far enough to produce a **credible Measurement Opportunity Register** containing a small set of clearly evidenced or explicitly caveated opportunities, each with a practical handoff path for further validation or implementation.

The output should be usable by a future owner without needing to reconstruct the logic from chat history.

## Current workbook architecture

The rebuilt workbook is source-first and refreshable:

`CSV/source files → Power Query source/context queries → Summary review surface`

- `Service_catalogue` is the clean 198-offering organisational spine.
- `Summary` must preserve that 198-row grain unless the authoritative catalogue changes.
- `Salesforce_context` retains full Product2/current-state and CI classification evidence.
- `Salesforce_summary` provides a review-oriented CI-rationalised aggregation without expanding Summary.
- `Website_context` contains the corporate sitemap hierarchy plus descriptions/action labels actually present in the saved sitemap.
- Website and Salesforce relationships to catalogue offerings remain blank until supported by evidence or human review; fuzzy/name similarity is not source truth.
- Transcript-inferred mappings are not preserved in the clean source layers.
- Helper/staging queries should be Connection Only where a visible worksheet is not required.
- Human review input must be stored in a refresh-safe input layer before operational review begins; typing into Power Query output is not durable.

The visible review experience is intended to centre on Summary plus Salesforce context, Website context, Modes, Segments, Check log and README.

## Repository structure

```text
cx-service-experience-mapping/
├── README.md
├── docs/
│   ├── mapping-method.md
│   ├── data-dictionary.md
│   └── mapping-decisions.md
└── outputs/
    └── measurement-opportunity-register.csv   # create when the first sample is ready
```

Do not commit the working Excel workbook until the method and handling rules are stable enough to justify versioning it.
