# Data Dictionary

## Purpose

Define the minimum fields used by the working mapping and the Measurement Opportunity Register.

Source-system fields retain their authoritative organisational meaning. Experience and measurement fields are working interpretation layers unless explicitly validated.

## Service Catalogue fields

### `service_area`

**Source:** Service Catalogue  
**Status:** Authoritative source value  
**Definition:** Highest current Service Catalogue grouping used in this mapping.

### `service`

**Source:** Service Catalogue  
**Status:** Authoritative source value  
**Definition:** Service within the relevant Service Area.

### `service_offering`

**Source:** Service Catalogue  
**Status:** Authoritative source value  
**Definition:** Service Offering used as the current practical mapping grain.

Do not assume Service Offering equals customer journey.

## Salesforce fields

### `sf_service_group`

**Source:** Salesforce CRM  
**Status:** Authoritative source value  
**Definition:** Current Salesforce Service Group where represented.

Blank / `NULL` is valid.

### `sf_service_name`

**Source:** Salesforce CRM  
**Status:** Authoritative source value  
**Definition:** Current Salesforce Service Name where represented.

Do not force a one-to-one relationship with Service Catalogue values.

## Experience fields

### `initial_mode`

**Controlled values:**

- Access
- Apply
- Comply
- Have a Say
- Report an Issue
- Take Part
- Transact

**Definition:** Interaction Mode that best describes the interaction when the customer's need, situation or obligation first triggers engagement.

**Status:** Working interpretation unless validated.

### `targeted_mode`

**Controlled values:** same as `initial_mode`.

**Definition:** Mode the customer should move into or complete once the initial interaction is understood.

Blank is valid where there is no meaningful transition.

### `mode_expectation`

**Definition:** Plain-English expression of what good should look like for the customer in the relevant Mode.

**Status:** Framework-derived benchmark to be tested against service evidence.

This is not a metric definition.

## Customer fields

### `resident_segment`

**Controlled values:**

- Young Adults Living Independently
- Young Families
- Midlife Adults
- Older Residents
- Young People Living at Home

**Definition:** Strongest current Resident Segment design lens where evidence supports one.

Blank is valid.

### `non_resident_segment`

**Controlled values:**

- Business Operators
- Landlords
- Commuters
- Occasional Visitors
- Out-of-state Visitors

**Definition:** Strongest current Non-Resident Segment design lens where evidence supports one.

Blank is valid.

### Cross-segment characteristics

Current reference characteristics include:

- Owner or Renter
- Culturally and Linguistically Diverse Background
- Service Complexity
- Access Complexity
- Near-resident
- Parenting Status
- Settling or Leaving

These are design stress tests, not additional Segments.

## Current-state evidence fields

### `known_current_experience`

**Definition:** Concise supported observation about the current customer experience.

Do not record assumptions as fact.

### `evidence_source`

**Definition:** Source supporting the current-experience observation or mapping.

Prefer a source reference over copied raw organisational material.

## Measurement opportunity fields

### `measurement_question`

**Definition:** The decision-relevant customer-experience question that current evidence does not adequately answer.

Good questions are specific enough to validate and useful enough to change a decision.

### `candidate_signal`

**Definition:** Possible observable measure that could help answer the measurement question.

Examples include effort, clarity, confidence, progress visibility, fairness, resolution, repeat contact or channel switching.

A candidate signal is not yet an approved KPI.

### `measurement_gap`

**Definition:** The missing evidence, definition, source, collection mechanism or analytical link preventing the question from being answered reliably today.

### `next_validation_action`

**Definition:** Smallest practical action required to validate or advance the measurement opportunity.

It should be assignable to another person or team.

### `likely_next_owner`

**Definition:** Role, team or capability best placed to take the next validation or implementation step.

Do not invent named owners without evidence.

Examples of capability types:

- Voice of Customer / Qualtrics;
- Data / Analytics;
- Service Owner;
- CRM / Salesforce;
- Service Design;
- Channel Strategy;
- Customer Contact.

### `status`

**Controlled working values:**

- Confirmed
- Working hypothesis
- Open
- Not represented
- Future signal

### `confidence`

**Definition:** Optional plain-language indication of how strongly the opportunity is supported once repeated use demonstrates that the field adds value.

Do not add a numerical scoring model during the current phase.

## Blank-value rule

Blank is valid.

A blank should not automatically be interpreted as:

- error;
- missing work;
- zero;
- no customer relevance;
- no Salesforce representation;
- no possible Mode.

Where the reason matters, use the status or notes associated with the mapping rather than inventing a value.
