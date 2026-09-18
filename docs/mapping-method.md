# Mapping Method

## Purpose

Define the minimum method for using the working service mapping to identify customer-experience measurement opportunities.

The goal is not to classify every service. The goal is to reveal patterns that can support a useful measurement, design or prioritisation decision.

## Core mapping sequence

Use the following sequence:

`Service Offering → Salesforce representation → Initial Mode → Targeted Mode → Segment lens → Mode expectation → current evidence → measurement opportunity`

## Step 1 — Select a representative service sample

Choose a deliberately varied sample rather than working through the catalogue sequentially.

Prefer services that vary by:

- Service Area;
- Interaction Mode;
- customer group;
- regulatory versus non-regulatory context;
- digital versus assisted experience;
- simple versus complex journey;
- strong versus weak existing evidence.

The sample should be large enough to reveal recurring patterns but small enough to review manually.

## Step 2 — Preserve source-system truth

Copy Service Catalogue and Salesforce values exactly from their authoritative sources.

Do not normalise or rewrite source values merely to make the mapping cleaner.

A missing Salesforce mapping is valid evidence and may indicate a structural or coverage gap.

## Step 3 — Assign Interaction Modes

Use only the controlled Mode values:

- Access
- Apply
- Comply
- Have a Say
- Report an Issue
- Take Part
- Transact

### Initial Mode

The Interaction Mode that best describes the interaction when the customer's need, situation or obligation first triggers engagement.

### Targeted Mode

The Mode the customer should move into or complete once the initial interaction is understood.

Blank is valid where there is no meaningful transition.

Do not classify Mode by channel, technology or organisational team.

## Step 4 — Apply the most relevant Segment lens

Select the strongest current Resident and/or Non-Resident Segment lens only when evidence supports it.

Do not list every possible audience.

Leave blank when no Segment is clearly more useful than another.

Segments are used for service and design analysis, not individual profiling.

## Step 5 — State the Mode expectation

Describe the customer expectation associated with the relevant Mode in plain language.

Examples:

- **Apply:** clear requirements, clear process, visible progress, fair assessment, appropriate support.
- **Comply:** understandable obligations, fairness, transparency, path to resolution.
- **Report an Issue:** clear ownership, minimal hand-offs, progress visibility, visible resolution.
- **Transact:** easy, fast, predictable completion.
- **Have a Say:** evidence that input was considered and its effect is understandable.
- **Access:** competent, welcoming and context-appropriate access.
- **Take Part:** easy discovery, booking, access and participation.

Treat these as evaluative benchmarks, not metrics.

## Step 6 — Capture current evidence

Record only what is currently supported.

Possible evidence includes:

- Voice of Customer / Qualtrics;
- complaints;
- enquiry or support demand;
- CRM case data;
- journey research;
- service metrics;
- usability research;
- operational observations;
- service documentation;
- validated stakeholder evidence.

Separate evidence from interpretation.

## Step 7 — Identify the measurement opportunity

Create a measurement opportunity when the mapping exposes a decision-relevant question that is not adequately answered today.

Useful candidate questions include:

- Are customers able to understand what is required?
- Can customers see progress without contacting Council?
- Are customers repeatedly switching channels to complete the interaction?
- Does the same Mode produce similar friction across different services?
- Do particular Segments experience the same Mode differently?
- Does the interaction reach a customer-relevant outcome, not just an internal transaction completion?
- Is an important Mode expectation currently invisible in existing reporting?

Do not create a metric simply because data exists.

## Step 8 — Describe a possible signal, not a finished KPI

For each opportunity, identify the smallest useful measurement signal that could test the question.

Examples:

- perceived clarity;
- effort;
- confidence;
- progress visibility;
- perceived fairness;
- resolution;
- repeat contact;
- escalation;
- avoidable support demand;
- channel switching;
- completion outcome;
- Mode expectation attainment.

Label these as candidate signals until measurement design is validated.

## Step 9 — Define the handoff

Every retained measurement opportunity must include a practical next step that can be owned by someone else.

Examples:

- Qualtrics / VoC team validates survey feasibility;
- Data / analytics team checks whether a behavioural signal already exists;
- service owner validates the customer expectation and service outcome;
- CRM team confirms available fields and operational grain;
- service designer tests the issue in journey research;
- Channel Strategy tests whether the issue recurs across services.

The mapping phase should not absorb work that properly belongs to the next owner.

## Measurement Opportunity Register fields

Use the following fields when the first sample is ready:

| Field | Purpose |
| --- | --- |
| service_area | Source Service Catalogue context |
| service | Source Service Catalogue context |
| service_offering | Working mapping grain |
| sf_service_group | Source Salesforce context |
| sf_service_name | Source Salesforce context |
| initial_mode | Interaction when the need first triggers engagement |
| targeted_mode | Desired next / completion Mode where relevant |
| resident_segment | Strongest current Resident design lens |
| non_resident_segment | Strongest current Non-Resident design lens |
| mode_expectation | Customer-side benchmark being examined |
| known_current_experience | Supported current-state observation |
| evidence_source | Source supporting the observation |
| measurement_question | Decision-relevant question that remains unanswered |
| candidate_signal | Possible measure or observable signal |
| measurement_gap | What is currently missing or unreliable |
| next_validation_action | Smallest practical next step |
| likely_next_owner | Capability / role best placed to continue |
| status | Confirmed / Working hypothesis / Open / Future signal |
| confidence | Optional plain-language confidence once useful |

Do not add more fields until real use exposes a need.

## Review test

Keep a measurement opportunity only if it improves at least one of:

- decision clarity;
- cross-service comparison;
- evidence quality;
- journey understanding;
- prioritisation;
- ability to hand work to the correct next owner.

If it only adds documentation, remove it.
