# Mapping Decisions

## Purpose

Record durable decisions that govern the CX Service Experience Mapping method and current measurement-opportunity phase.

Do not use this file as a running chat log.

## Decision 01 — Source systems remain authoritative

**Status:** Confirmed

Service Catalogue, Salesforce CRM and CX Segments and Modes source material remain authoritative for their original organisational content.

The mapping may connect or interpret these sources but must not overwrite their definitions.

## Decision 02 — The Excel workbook remains the active working artefact

**Status:** Confirmed

The current workbook is used for manual mapping and controlled reference lists.

The repository governs method, definitions, decisions and reusable outputs.

The workbook should not be committed until the method and handling rules are stable enough to justify versioning it.

## Decision 03 — Manual mapping before automation

**Status:** Confirmed

Modes and Segments should be populated manually using evidence and judgement before any classification automation is considered.

Automation is justified only if repeated manual use exposes stable patterns that can be encoded safely.

## Decision 04 — Blank is a valid result

**Status:** Confirmed

Do not classify simply to fill a cell.

An uncertain, absent or unmapped relationship should remain visible rather than be replaced with invented certainty.

## Decision 05 — Mode is independent of channel and organisational structure

**Status:** Confirmed

Mode represents the type of customer interaction and associated expectations.

It must not be derived directly from:

- channel;
- organisational team;
- technology;
- Salesforce classification.

## Decision 06 — Initial and Targeted Mode remain the working transition model

**Status:** Working method

Use:

`Initial Mode → Targeted Mode`

where a meaningful customer interaction transition exists.

Targeted Mode may remain blank where there is no meaningful transition.

Do not add additional Mode-sequence fields until real service mapping demonstrates a need.

## Decision 07 — Segments are design lenses

**Status:** Confirmed

Resident and Non-Resident Segment fields identify the strongest current design lens where evidence supports one.

They are not exhaustive audiences and must not be used for individual customer profiling.

## Decision 08 — Cross-segment characteristics remain separate

**Status:** Confirmed

Cross-segment characteristics describe circumstances that may materially change an experience.

They remain design stress tests rather than additional customer Segments.

## Decision 09 — The project is broader than Intelligent Front Door

**Status:** Confirmed

The mapping originated partly through Intelligent Front Door work but should remain reusable across service analysis, journey mapping, Channel Strategy, design prioritisation and measurement.

IFD does not govern the whole model.

## Decision 10 — Current project focus is measurement opportunity

**Status:** Confirmed

The current phase is **Measurement opportunity validation**.

The mapping should be used to identify decision-relevant questions and evidence gaps that could support better customer-experience measurement across services.

The phase should not optimise for catalogue completion.

## Decision 11 — The required phase output is handoff-ready

**Status:** Confirmed

The primary current-phase output is a **Measurement Opportunity Register** containing a small set of credible, caveated opportunities derived from real service mapping.

Each retained opportunity must include:

- the customer-experience question;
- supporting evidence or explicit evidence gap;
- candidate measurement signal;
- next validation action;
- likely next organisational capability / owner type;
- status.

The output should be usable by another person or team without requiring the original mapper to continue owning the work.

## Decision 12 — Modes provide benchmarks, not finished metrics

**Status:** Confirmed

Mode expectations can help determine what should be examined, but they do not define the metric, target, source or causal interpretation.

Every actual measure requires separate measurement design and governance.

## Decision 13 — Stop mapping when the decision value is reached

**Status:** Confirmed

Do not map all 198 Service Offerings unless later work genuinely requires it.

The current mapping sample is sufficient when it reveals enough recurring patterns and measurement gaps to create a credible handoff-ready Measurement Opportunity Register.

## Decision 14 — No Strategic OS expansion is required

**Status:** Confirmed

This project remains separate from Strategic OS.

Only promote a reusable pattern into Strategic OS if repeated real use exposes a genuine Strategic OS capability gap.

## Decision 15 — Bitbucket is the Atlassian integration surface

**Status:** Confirmed

The Bitbucket repository is used to run the project pipeline that creates and updates the linked Jira and Confluence project surfaces.

The pipeline must remain manually triggered during the current phase.

Do not introduce automatic publishing or two-way synchronisation until repeated use demonstrates a need.

## Decision 16 — Jira uses one Deliverable for the current phase

**Status:** Confirmed

The current Jira delivery record is one **Deliverable**:

`CX Service Experience Mapping — Measurement Opportunity Validation`

The pipeline should not automatically create Epics, Stories, Tasks or Sub-tasks beneath it.

Lower-level Jira delivery structure can be created later by the appropriate owner if the work moves into implementation.

## Decision 17 — Confluence structure remains minimal

**Status:** Confirmed

Use one parent page:

`CX Service Experience Mapping`

with two child pages only:

- `Project Management`
- `Measurement Opportunity Work`

`Project Management` provides the readable phase, scope, Jira Deliverable link and current next action.

`Measurement Opportunity Work` contains the evolving findings and Measurement Opportunity Register.

Do not add additional Confluence structure unless real use demonstrates a need.

## Decision 18 — Atlassian creation connectivity is proven

**Status:** Validated

The Bitbucket pipeline has successfully created Jira and Confluence content using the configured Atlassian connection.

The proof demonstrated:

- Jira item creation;
- Confluence page creation;
- repository, build and commit traceability;
- usable linked project content.

Future pipeline work can therefore focus on producing or refreshing real project deliverables rather than repeating connectivity tests.
