# Catalogue-wide Interaction Mode mapping for human verification

**Recorded:** 13 November 2026  
**Status:** Working method, proposed for project review. Not an approved classification or a change to source-system truth.

## Decision and purpose

Use a configurable, reproducible first-pass classification to **facilitate human mapping**, not replace it. Evaluate the complete Service Catalogue baseline, expose candidate Modes and evidence, and direct reviewer attention to ambiguous offerings and customer interactions. This is a deliberate extension of the earlier representative-sample approach for review preparation, **not** a decision to classify all offerings as final or to replace the Measurement Opportunity Register.

## Source and run baseline

- Working source: `Service-Catalogue-Context-and-Checks.xlsx`, sheet `Catalogue to SF with modes`, older snapshot; current authoritative catalogue freshness not established.
- 645 source rows, 198 distinct exact `(service_area, service, service_offering)` keys.
- First rule-based pass: 73 candidate initial Modes, 125 unresolved; **zero automatically confirmed**.
- Seven allowed Modes: Access, Apply, Comply, Have a Say, Report an Issue, Take Part, Transact.
- Salesforce service names and pre-existing Mode values are context for review, not automatically validated relationships.
- No approved Segments, website associations, or customer journeys are inferred.

## Reproducible reasoning

The local working package contains `map_modes.py`, `criteria.json`, `offering_mode_candidates.csv`, `source_row_trace.csv` and a README. Do **not** commit workbook extracts or row-level organisational data to this public GitHub staging repository. Transfer the reviewed code/config to the approved Bitbucket project location after source and publication checks.

1. Preserve exact catalogue hierarchy and source-row trace. Group rows by offering without deduplicating away the underlying Salesforce context.
2. Apply editable regex rules to offering, parent service and Salesforce name fields, using explicit field weights.
3. Score each of the seven Modes, retain all matching rules and weighted contributions, and show the runner-up.
4. Propose a Mode only if both minimum score and margin thresholds pass. Otherwise leave it Open. Never default to Access.
5. Label every algorithmic result **Working hypothesis**. The confidence heuristic is not a calibrated probability.
6. Leave Targeted Mode blank unless a meaningful customer transition is evidenced. The existing Apply-to-Comply default in the prototype is a **review-required hypothesis**, not a general rule.
7. Do not infer Segments or turn organisational capability labels into customer tasks without evidence.

## Human verification workflow

**Preparation:** confirm the current catalogue version and stable offering keys; supply authorised offering descriptions and customer-facing task statements where available. Preserve source fields separately from inferred fields.

**Review unit:** `Service Offering → customer intent / interaction → Initial Mode → Targeted Mode (if meaningful)`. One offering may produce multiple interaction rows. The offering-level candidate is a triage suggestion, not the final classification grain.

**Review order:** (1) conflicting or close scores; (2) 125 Open offerings; (3) organisational-capability labels; (4) candidates that appear straightforward; (5) any existing human mapping that disagrees with the model.

**For each interaction:** record customer task, proposed Mode(s), source reference, evidence type, matched rules, alternative Mode, reviewer decision (Accept / Amend / Split interaction / Leave Open), rationale, reviewer and date. Preserve the original model output. A human approval must not overwrite the evidence trace.

**Calibration:** adjust `criteria.json` only after reviewing a varied set of accepted and rejected examples. Re-run all offerings and produce a before/after diff of changed Mode assignments and new conflicts. Do not optimise for a higher completion percentage. Validate false positives and missed customer intents before accepting revised thresholds.

**Handoff:** promote reviewed interaction mappings to the refresh-safe human input layer, not directly into Power Query outputs. Create measurement opportunities only where a decision-relevant question and evidence gap exist.

## Acceptance criteria

- Every distinct offering is accounted for, including reviewed-but-open records.
- No unreviewed candidate is labelled Confirmed.
- Multi-interaction offerings can be split without changing the source catalogue.
- A reviewer can explain and override any proposed classification.
- Changes to rules are versioned and reproducible, with regression comparisons.
- Authoritative catalogue and Salesforce identities remain untouched.
- No raw organisational extracts are published to GitHub.

## Meeting update for Dennis, 13 November 2026

**Project:** Service and Interaction Modes mapping  
**Decision:** use rules-as-code to generate an explainable first-pass mapping for human verification rather than manually assigning all offerings from scratch.  
**Progress:** complete baseline evaluated (198 offerings; 645 working source rows). 73 rule-supported candidates; 125 explicitly Open. No classifications approved by automation.  
**Why it matters:** focuses review on ambiguous customer intents, supports consistent decisions and allows criteria to be refined without losing the reasoning trail.  
**Strategic direction:** validate interaction-level mapping with a small varied sample, refine rules from human decisions, and preserve the Service Catalogue as the organisational anchor. Do not prioritise artificial 100% Mode coverage.  
**Dependency:** verify source freshness and move implementation to the approved Bitbucket workflow before operational use.
