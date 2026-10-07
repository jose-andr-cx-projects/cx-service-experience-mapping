# Architecture Translator — Salesforce Service Configuration

## Purpose

Document the architecture translation needed to locate and interpret City of Melbourne Salesforce service configuration.

This resolves a long-standing business ambiguity: **where is a Salesforce "service" actually configured?**

The answer is not a single field or a custom `Service__c` object. The operational service model is distributed across several Salesforce structures, with **Product (`Product2`) acting as the core service configuration object**.

This document records the translation so future analysis does not have to rediscover the architecture from case data or UI labels.

## Key finding

### Core translation

```text
Business concept: Salesforce Service
        ↓
Salesforce standard object: Product
API object: Product2
        ↓
Product.Name
        ↓
Case.Product
        ↓
Case.Service_Name__c
Formula: Product.Name
```

The Case field labelled **Service Name** is not an independently configured service value.

`Case.Service_Name__c` is a **formula field** whose formula is:

```text
Product.Name
```

Therefore, when the business sees a Service Name on a Case, the underlying service record is a **Product2 record**.

## Why this matters

Previous investigation could reasonably suggest that service configuration had to be reconstructed from transactional Case fields because no obvious standalone `Service__c` configuration object was present.

Direct inspection of Salesforce Object Manager shows a different architecture:

> CoM uses the Salesforce standard Product object (`Product2`) as the core operational service configuration object.

This means current service configuration should be sourced from Product2 wherever possible, rather than inferred from historical cases.

Cases remain useful for understanding observed operational use and historical configuration, but they are not the preferred source for the current service master.

## Product2 configuration evidence

Direct inspection of **Object Manager → Product / Service (`Product2`) → Fields & Relationships** confirmed service-specific configuration fields including:

| Business concept | Salesforce field |
| --- | --- |
| Service Name | `Name` |
| Service Friendly Name | `Service_Friendly_Name__c` |
| Service Unique Name | `Service_Unique_Name__c` |
| Service Description | `Description` |
| Active | `IsActive` |
| Service Type | `Service_Type__c` |
| Reporting Service Type | `Reporting_Service_Type__c` |
| Service Calendar | `Service_Calendar__c` |
| SLA Customer Response | `SLA_Customer_Response__c` |
| SLA Warning Notification | `SLA_Warning_Notification__c` |
| Auto Close | `Auto_Close__c` |
| Auto Close Outcome | `Auto_Close_Outcome__c` |
| Disable Automatic Notifications | `Disable_Auto_Notifications__c` |
| Disable Auto Notifications — Creation | `Disable_Auto_Notifications_Creation__c` |
| Creation Description | `Creation_Description__c` |
| Creation Description 2 | `Creation_Description_2__c` |
| Creation Description SMS | `Creation_Description_SMS__c` |
| Service Work Order Auto Create | `Service_Work_Order_Auto_Create__c` |

Product2 contains many additional fields. The table above records only fields confirmed during this investigation that are directly relevant to service configuration and the CX service mapping.

## Supporting configuration layers

Product2 is the core service object, but it is not the only relevant configuration layer.

### 1. Product2 — core service configuration

Use for the current operational definition of a Salesforce service.

```text
Product2
→ service identity
→ friendly/customer-facing name
→ description
→ active state
→ service type
→ calendar
→ SLA-related configuration
→ automation
→ notification behaviour
```

### 2. Customer Intelligence service fields — analytical layer

For this validation, the Databricks boundary is the **Customer Intelligence** schema only.

Relevant visible source:

```text
datahub_refined.customer_intelligence.vwcase
```

The current Customer Intelligence `vwcase` exposes:

- `Service_Name`
- `Service_Group`

For the service-experience mapping, retain only these semantic outputs:

- `sf_service_name`
- `ci_service_name_grouped`

Do not include `ci_theme`.

Do not use or reference the `customer_account_management` schema in this lineage analysis.

### Lineage rule

The validation question is not which adjacent refined schema contains similar service fields. It is:

> What upstream source feeds the relevant service fields into `customer_intelligence`?

Current evidence supports:

- `Service_Name` is present in `customer_intelligence.vwcase`.
- Salesforce Case uses `Service_Name__c`, which resolves to Product `Name`.

Therefore `sf_service_name` has a credible Salesforce lineage.

The exact upstream derivation of `ci_service_name_grouped` within the Customer Intelligence layer is **still to be verified**. Do not infer it from a similarly named field in another schema.

Until that derivation is confirmed, describe `ci_service_name_grouped` as a **Customer Intelligence classification field with source lineage pending validation**.

### 3. Service Assignment Rule — specialist routing configuration

Salesforce custom object:

```text
Service_Assignment_Rule__c
```

Direct Salesforce inspection confirmed that this object contains operational routing/configuration fields including:

- Service Type
- Service Subtype
- Service SubType Code
- Service Type Friendly
- SLA
- SLA Days
- SLA Measured In
- Case Team
- Case Team Queue ID
- Responsible Officer
- escalation configuration
- notification controls
- survey settings
- work-order behaviour

The Service Type field is a picklist backed by a **Global Value Set: Service Type**. Service Subtype is dependent on Service Type.

A direct Salesforce report export returned **75 Service Assignment Rule records**, covering a subset of operational services. Therefore this object is an enrichment/configuration subsystem, not the complete Salesforce service catalogue.

### 4. Case — observed operational use

Relevant source:

```text
Case
datahub_refined_source.salesforce.vwcase
```

Case records contain service-related values and configuration stamped into transactional history.

Use Case data for:

- observed use;
- historical configuration;
- detecting configuration drift;
- validating whether configured services are actually used;
- operational measurement.

Do **not** reconstruct current service configuration from Cases when Product2 is available.

## Architecture translation

The current working translation is:

```text
SERVICE CATALOGUE
Business service / service offering
        ↓ mapping relationship

SALESFORCE SERVICE MASTER
Product2
Product.Name = operational Service Name
        ↓

CASE
Case.Product
Case.Service_Name__c = Product.Name
        ↓

CUSTOMER INTELLIGENCE
customer_intelligence.vwcase
Service_Name → sf_service_name
Service_Group / grouped logic → ci_service_name_grouped
        ↓

SPECIALIST CONFIGURATION
Service_Assignment_Rule__c
Service Type / Subtype → routing / SLA / responsible team
        ↓

TRANSACTIONAL EVIDENCE
Case history
Observed use / historical configuration / volumes / outcomes
```

These layers should not be collapsed into a single concept called "Salesforce service".

## Source authority

For this project, use the following source hierarchy:

| Question | Preferred source |
| --- | --- |
| What is the Salesforce service record? | Salesforce Product2 |
| What is the current service name/configuration? | Salesforce Product2 |
| Is the service active? | Salesforce Product2 |
| What customer-facing/friendly description is configured? | Salesforce Product2 |
| What grouped Customer Intelligence classification is applied? | `customer_intelligence` layer; exact derivation of `ci_service_name_grouped` still to validate |
| What specialist assignment/routing rule applies? | `Service_Assignment_Rule__c` |
| How has the service actually appeared on cases? | Case / `vwcase` |
| Has configuration changed historically? | Case history and Salesforce field history where available |

Organisational systems remain authoritative for source data. This repository records the **translation, interpretation and mapping method**.

## Architecture Translator method

When a business label in Salesforce is ambiguous, do not assume the visible field represents the underlying configuration object.

Use this sequence:

1. **Start from the business-visible field.**
   Example: Case → Service Name.

2. **Inspect the field definition in Object Manager.**
   Identify whether it is a formula, lookup, picklist, global value set or stored value.

3. **Follow the dependency.**
   In this case:
   `Case.Service_Name__c → Product.Name`.

4. **Inspect the underlying object.**
   Here: `Product2`.

5. **Identify configuration fields on that object.**
   Separate identity, classification, SLA, routing, automation and customer communication.

6. **Identify supporting configuration objects.**
   Example: `Service_Assignment_Rule__c`.

7. **Separate configuration from transactions.**
   Product2/configuration objects describe intended configuration; Case records describe observed operational use.

8. **Validate bulk extraction.**
   Prefer a direct Salesforce report/export or supported source extraction over manual record inspection.

9. **Record unresolved source semantics explicitly.**
   Do not promote inferred relationships to authoritative status without validation.

## Implication for the service-experience mapping

The Salesforce context layer should be organised around:

```text
Product2 core configuration
        +
CI grouped-service enrichment where validated
        +
Assignment-rule enrichment where applicable
        +
Case evidence where useful
```

The main mapping should not copy every Salesforce configuration field. It should retain the minimum fields needed to connect:

```text
Service Catalogue
→ Salesforce Service
→ CX Interaction Mode
→ Customer Segment
→ Measurement opportunity
```

Detailed Salesforce configuration belongs in the Salesforce context layer. For the service-mapping view, expose only `sf_service_name` from Salesforce and `ci_service_name_grouped` as the grouped CI classification, plus technical identifiers/status fields required for traceability and QA.

## Validation status

**Confirmed directly in Salesforce**

- Case `Service_Name__c` is a formula using `Product.Name`.
- Product / Service uses the standard Salesforce `Product2` object.
- Product2 contains service-specific configuration fields.
- `Service_Assignment_Rule__c` is a deployed custom configuration object.
- Service Assignment Rule uses a Service Type global value set and dependent Service Subtype.
- Service Assignment Rules are reportable in Salesforce.

**Confirmed through Databricks discovery**

- `datahub_refined.customer_intelligence.vwcase` exposes `Service_Name` and `Service_Group`.
- `customer_account_management` is outside the boundary for this lineage analysis.
- `sf_service_name` can be traced conceptually to Salesforce Case `Service_Name__c` / Product `Name`.

**Still source-lineage validation**

- The exact Customer Intelligence transformation that produces `ci_service_name_grouped`.
- Whether `ci_service_name_grouped` is derived exclusively from Salesforce-fed records or combines another upstream source.

**Still to validate**

- Direct bulk report/export coverage of Product2.
- Whether all Product2 records in scope represent CX operational services.
- Exact relationship between Product2 Service Type fields and Service Assignment Rule Service Type values.
- Exact upstream source and transformation for `ci_service_name_grouped` inside the Customer Intelligence layer.
- Whether Salesforce field history provides useful Product2 configuration-change history.

## Reuse note

This finding is valuable beyond the immediate mapping exercise because the location of Salesforce service configuration has been unclear to the business.

The reusable lesson is not merely "look in Product2". It is:

> Translate the business-visible Salesforce label by tracing the field definition to its underlying object and then separate master configuration, supporting configuration and transactional evidence.

That method should be reused when translating other Salesforce concepts whose UI labels obscure their technical architecture.


## Current state versus Customer Intelligence classification

### Architecture boundary

For this mapping, keep the model deliberately narrow:

```text
SALESFORCE
Case.Service_Name__c → Product.Name
        ↓
CUSTOMER INTELLIGENCE
customer_intelligence.vwcase
        ├── Service_Name → sf_service_name
        └── grouped service logic → ci_service_name_grouped
        ↓
SERVICE EXPERIENCE MAPPING
```

Interpret the two fields as follows:

- **`sf_service_name`** — the Salesforce service name represented in the Customer Intelligence layer.
- **`ci_service_name_grouped`** — the grouped service classification produced or exposed by Customer Intelligence.

### Lineage status

| Field | Customer Intelligence location | Salesforce-only status |
| --- | --- | --- |
| `sf_service_name` | `customer_intelligence.vwcase.Service_Name` | **Salesforce lineage supported** |
| `ci_service_name_grouped` | Customer Intelligence grouped-service logic / field to confirm | **Not yet verified** |

The validation must stay inside the Customer Intelligence lineage. Do not use `customer_account_management` as evidence for either field.

The next technical check is to inspect the definition or upstream lineage of the Customer Intelligence view/field that supplies the grouped service value and determine whether its inputs are exclusively Salesforce-derived.

## Databricks reusable access pattern

### Purpose

Verify the two fields used by the mapping through the Customer Intelligence layer only.

### Source boundary

Use:

```text
datahub_refined.customer_intelligence
```

Do not use `customer_account_management` for this validation.

### Fields in scope

1. **Salesforce service name**
   - Visible in: `customer_intelligence.vwcase.Service_Name`
   - Semantic alias: `sf_service_name`
   - Salesforce lineage: supported by the known Salesforce Case → Product relationship.

2. **Customer Intelligence grouped service**
   - Semantic alias: `ci_service_name_grouped`
   - Exact Customer Intelligence source field/view: still to confirm.
   - Salesforce-only lineage: still to confirm.

### Minimal validation procedure

1. Inspect the Customer Intelligence objects only.
2. Locate every occurrence or derivation of:
   - `Service_Name`
   - the grouped service field used for `ci_service_name_grouped`.
3. Inspect the view definition / lineage for those fields where permissions allow.
4. Identify the immediate upstream table/view and source-system fields.
5. Confirm whether every upstream input is Salesforce-derived.
6. Record any non-Salesforce input explicitly.
7. Do not infer lineage from naming similarity across other schemas.

### Reusable Genie / analyst prompt

```text
Validate the source lineage of two service fields used by the CX service-experience mapping.

BOUNDARY

Look only at datahub_refined.customer_intelligence and the upstream objects that directly feed its relevant views.

Do not use customer_account_management as evidence or as a source for this analysis.

FIELDS TO VALIDATE

1. sf_service_name
2. ci_service_name_grouped

KNOWN EVIDENCE

- customer_intelligence.vwcase exposes Service_Name and Service_Group.
- Salesforce Case.Service_Name__c is known to resolve to Product.Name.

TASK

A. Locate the exact Customer Intelligence field that should populate sf_service_name.
B. Locate the exact Customer Intelligence field or transformation that should populate ci_service_name_grouped.
C. For each field, trace the lineage upstream until the originating source system is clear.
D. State whether the field is:
   - SALESFORCE_ONLY
   - MIXED_SOURCE
   - DERIVED_IN_CUSTOMER_INTELLIGENCE
   - UNKNOWN
E. Show the exact table/view and column chain used to reach that conclusion.
F. If a view definition is accessible, show only the relevant transformation/join logic.
G. Do not infer lineage from similarly named fields in another schema.
H. Do not include Theme.

OUTPUT

Return:
field_name
customer_intelligence_object
customer_intelligence_column
immediate_upstream_object
immediate_upstream_column
originating_source_system
lineage_status
evidence
open_question

Do not create or modify any table or view.
```

### Governance rule

The Databricks Customer Intelligence layer is a **translation/analytical layer**, not a replacement source of truth.

- Salesforce remains the originating business system for `sf_service_name` where the lineage check confirms the Case/Product path.
- Customer Intelligence owns the analytical derivation exposed as `ci_service_name_grouped`.
- Do not describe `ci_service_name_grouped` as Salesforce-only until its Customer Intelligence upstream lineage is explicitly verified.
- `customer_account_management` is outside the scope of this lineage test.
- The Service Catalogue remains authoritative for its organisational service definitions.
