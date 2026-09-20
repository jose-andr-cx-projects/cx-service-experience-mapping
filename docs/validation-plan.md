# Validation Plan

## Purpose

Define the next contained test for CX Service Experience Mapping before the method is expanded, automated or treated as a reusable organisational pattern.

The test should determine whether the current mapping model can reliably connect:

`Service Offering → customer interaction → Interaction Mode → customer expectation → current evidence → measurement opportunity`

without forcing false one-to-one relationships or inventing certainty.

This is a project validation activity.

Strategic OS may consume validated reusable learning later, but this repository remains the working source for the test evidence, mapping decisions and project outputs.

---

## Validation question

> Is Service Offering a useful organisational anchor while customer interaction remains the correct grain for assigning Interaction Mode and identifying a meaningful measurement opportunity?

The test should also determine whether the existing:

`Initial Mode → Targeted Mode`

model is sufficient for the current phase.

---

## Why this test is needed

The first working mapping exposed three materially different cases:

1. a Service Offering that appears to map cleanly to one dominant Mode;
2. a Service Offering that contains several customer interactions and therefore several possible Modes;
3. a Service Offering that describes organisational capability or delivery work more clearly than a discrete customer interaction.

These cases need to be tested before scaling the mapping across more of the Service Catalogue.

---

## Test sample

Use a deliberately small and varied sample.

Candidate offerings include:

- Parking Permitting;
- Parking Enforcement;
- Infringement Review;
- Event Approvals;
- Animal Registration;
- Recreation Facilities;
- Community Grants Management;
- Graffiti Removal.

Confirm the exact Service Catalogue values against the current authoritative source before mapping.

Do not add more services merely to increase sample size.

The sample is sufficient if it tests:

- single-Mode interactions;
- multi-Mode interactions;
- regulatory and non-regulatory services;
- simple and complex interactions;
- services with stronger and weaker evidence;
- offerings that are clearly customer-facing and offerings where the customer interaction grain is ambiguous.

---

## Test method

For each sampled Service Offering:

1. preserve the authoritative Service Catalogue value;
2. record the current Salesforce representation where known;
3. identify the actual customer intent or interaction being examined;
4. assign an Initial Mode only where the evidence supports it;
5. assign a Targeted Mode only where a meaningful transition exists;
6. state the relevant Mode expectation in plain language;
7. record any supported current-experience evidence;
8. identify the customer-experience question that remains unanswered;
9. identify the smallest useful candidate measurement signal;
10. record the evidence or measurement gap;
11. define the next validation action;
12. identify the likely next organisational capability or owner type.

Leave fields blank where evidence is insufficient.

---

## Specific validation tests

### Test 1 — Clean single-Mode case

Use at least two offerings where the interaction appears straightforward.

Example hypotheses:

- Parking Permitting → Apply;
- Animal Registration → Transact.

Validate whether the Mode assignment remains stable when the actual customer intent and service process are examined.

Pass signal:

> The Service Offering provides a useful anchor and the customer interaction can be described with one clear Mode without losing important experience detail.

---

### Test 2 — Multi-Mode case

Use at least two offerings where different customer intents or actors may create different Modes.

Candidate cases:

- Parking Enforcement;
- Recreation Facilities;
- Infringement Review.

Test whether the interaction needs to be separated before Mode is assigned.

Pass signal:

> Multiple Modes can be represented without forcing the Service Offering itself to have one permanent Mode.

If this test passes, retain Service Offering as the organisational anchor and treat Mode as an interaction-level interpretation.

---

### Test 3 — Organisational-offering versus customer-interaction case

Use at least one offering whose catalogue label may describe organisational capability rather than a discrete customer interaction.

Test whether the mapping can identify the actual customer-facing interaction without changing the authoritative Service Catalogue label.

Pass signal:

> The mapping preserves source-system truth while making the customer interaction explicit as a separate interpretation layer.

---

### Test 4 — Mode transition sufficiency

Test whether:

`Initial Mode → Targeted Mode`

is sufficient to describe the sampled interactions.

Do not introduce a longer Mode sequence unless the sample demonstrates that two fields materially distort the customer journey.

Pass signal:

> The current transition model captures enough interaction logic to support the measurement question without adding new fields.

Fail signal:

> Important interaction changes are repeatedly lost or misrepresented because more than two Modes are required.

---

### Test 5 — Measurement value

For each mapped interaction, ask:

> Does this mapping expose a customer-experience question that could change a decision, prioritisation or design choice?

Retain a measurement opportunity only when it improves at least one of:

- decision clarity;
- cross-service comparison;
- evidence quality;
- journey understanding;
- prioritisation;
- handoff to the correct next owner.

Pass signal:

> The sample produces a small number of credible, caveated measurement opportunities rather than merely a richer classification table.

---

## Evidence to capture

For each test row, distinguish:

### Confirmed

- authoritative Service Catalogue value;
- authoritative Salesforce values where available;
- controlled Mode definitions;
- validated service or customer evidence.

### Working hypothesis

- customer intent;
- Mode assignment;
- Mode transition;
- Segment relevance;
- interpretation of the current experience;
- candidate measurement signal.

### Open

- missing source-system mapping;
- missing customer evidence;
- uncertain interaction grain;
- unresolved ownership;
- unclear measurement feasibility.

Do not convert a plausible mapping into a confirmed relationship without evidence.

---

## Strategic OS connection

This project is a live test of whether Strategic OS can connect governed reusable context to real project work without becoming the project system of record.

The operating boundary is:

`Strategic OS governed context → project reasoning and test design → project evidence and validation → approved reusable learning → Strategic OS`

During this test:

- Strategic OS may provide approved Segments and Modes interpretation;
- this repository records the actual mapping test, evidence and decisions;
- organisational systems remain authoritative for original organisational data;
- project hypotheses remain in this repository until validated;
- only genuinely reusable validated learning should later be promoted into Strategic OS.

Do not create a new Strategic OS framework or rule solely because this test exists.

---

## Outputs

The next validation cycle should produce:

1. a small reviewed mapping sample;
2. a short record of which mapping assumptions held or failed;
3. the first candidate rows for the Measurement Opportunity Register;
4. any evidence that the mapping grain needs to change;
5. any reusable learning worth considering for Strategic OS promotion.

Do not attempt the full Service Catalogue.

---

## Validation decision

At the end of the sample, make one of four decisions:

### Continue

The current model is useful enough to test on a slightly broader sample.

### Refine

A specific mapping rule needs adjustment before continuing.

### Narrow

The model is useful only for a smaller class of services or interactions.

### Stop

The mapping does not create enough decision or measurement value to justify further work.

The decision should be based on observed test evidence, not architectural preference.

---

## Definition of done

This validation step is complete when:

- at least one clean single-Mode case has been tested;
- at least one multi-Mode case has been tested;
- at least one ambiguous organisational-offering case has been tested;
- the sufficiency of Initial Mode → Targeted Mode has been assessed;
- unsupported Segment assignments have been avoided;
- source-system gaps remain visible rather than being forced into matches;
- at least one credible measurement opportunity has been produced or the test has shown why none should be retained;
- the next decision is explicitly Continue, Refine, Narrow or Stop;
- any Strategic OS learning is separated from project-specific evidence and requires human review before promotion.
