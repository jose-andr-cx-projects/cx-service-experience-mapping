# CX Service Experience Mapping

## Purpose

Create a working connection between the City of Melbourne service model and customer experience model so service interactions can be compared, measured and improved across organisational boundaries.

The working mapping connects:

`Service Catalogue → Salesforce CRM → Interaction Modes → Customer Segments → Measurement opportunity`

The mapping is not an end in itself. Its purpose is to expose **usable customer-experience measurement opportunities** that can be handed to the appropriate next owner for validation, measurement design or implementation.

## Source authority

Organisational systems of record remain authoritative for original service data, including:

- Service Catalogue;
- Salesforce CRM;
- CX Segments and Modes source material;
- governed Voice of Customer and measurement sources.

The current Excel workbook is the working review artefact. Its protected spine is the authoritative Service Catalogue hierarchy: **198 distinct service offerings** in the current baseline. Supporting Salesforce and website context are kept as separate evidence layers rather than being automatically treated as confirmed mappings.

This repository governs the mapping method, definitions, decisions and reusable outputs. It does not replace organisational source systems.

## Current phase

**Measurement opportunity validation**

The current phase uses a representative sample of real services to determine whether the mapping can produce a useful measurement view across services.

The phase should answer:

> Can Service Catalogue, Salesforce, customer Segments and Interaction Modes be combined to identify measurable customer-experience patterns that are useful beyond a single service?

The work should produce something another person or team can take forward without requiring the original mapper to remain the owner.

## Phase output

The primary output is a **Measurement Opportunity Register** derived from the working service mapping.

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

> **Map enough to reveal measurement opportunities. Do not complete the catalogue for completeness.**

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
