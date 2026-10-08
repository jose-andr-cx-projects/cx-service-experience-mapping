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

For this validation, the Databricks boundary is the **Customer Intelligence** lineage.

Relevant Customer Intelligence fields:

- `Service_Name` → semantic alias `sf_service_name`
- `Service_Group` → semantic alias `ci_service_name_grouped`

Do not include `ci_theme`.

Do not use `customer_account_management` in this lineage analysis.

### Confirmed lineage

#### `sf_service_name`

```text
datahub_datamart.customer_intelligence.vwcase.Service_Name
        ↓ masking only
datahub_datamart_eng.customer_intelligence.vwcase.Service_Name
        ↓
datahub_refined.customer.vwcase.Service_Name
        ↓ masking only
datahub_refined_eng.customer.vwcase.Service_Name
        ↓
datahub_refined_source.salesforce.vwcase.Service_Name__c
        ↓
Salesforce Case.Service_Name__c
        ↓ formula
Salesforce Product2.Name
```

Validation evidence:

- `refined.customer.vwcase.Service_Name` matched Salesforce `vwcase.Service_Name__c` across more than 2.15 million joined Case rows.
- Salesforce `Service_Name__c` matched Product2 `Name` through `ProductId = Id` across the same population.
- `refined.customer.vwcase.SourceSystem` contained only `salesforce`.

Classification:

`SALESFORCE_ONLY`

#### `ci_service_name_grouped`

```text
datahub_datamart.customer_intelligence.vwcase.Service_Group
        ↓ masking only
datahub_datamart_eng.customer_intelligence.vwcase.Service_Group
        ↓
datahub_refined.customer.vwcase.Service_Group
        ↓ lookup
datahub_refined.customer.vwservice_group.ServiceNameGrouped
        ↑
join on vwcase.Service_Name = vwservice_group.ServiceName
        ↑
Salesforce Product2.Name
```

The grouped value is not a native Salesforce field. It is a **Customer Intelligence-maintained many-to-one classification overlay** applied to Salesforce service names.

Validation evidence:

- `refined.customer.vwcase.Service_Group` matched `vwservice_group.ServiceNameGrouped` across more than 2.15 million Case rows.
- The grouping uses Salesforce-derived `Service_Name` as the lookup key.
- The reference maps roughly 571 distinct service names into 55 grouped service categories.
- Nearly all tested reference service names aligned to Salesforce Product2 names; one unmatched legacy/reference exception was observed.

Classification:

`DERIVED_IN_CUSTOMER_INTELLIGENCE_FROM_SALESFORCE`

### Metadata anomaly

`vwservice_group.SourceSystem` is populated with `genesys`, but this value conflicts with the validated content lineage:

- the lookup key is Salesforce-derived Service Name;
- the reference content overwhelmingly aligns with Salesforce Product2 names; and
- the grouping is consumed by Salesforce Case-derived Customer Intelligence views.

Treat `SourceSystem = 'genesys'` as an **unresolved metadata/load-path anomaly**, not evidence that `ci_service_name_grouped` is Genesys-derived.

The engineering-layer view definitions were not accessible during validation, so the exact loading mechanism remains unconfirmed.

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
| What grouped Customer Intelligence classification is applied? | Customer Intelligence `Service_Group`, derived from `vwservice_group.ServiceNameGrouped` using Salesforce-derived `Service_Name` |
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

**Confirmed through Databricks lineage validation**

- `sf_service_name` is Salesforce-only: Customer Intelligence `Service_Name` resolves to Salesforce Case `Service_Name__c`, which resolves to Product2 `Name`.
- `ci_service_name_grouped` is derived in Customer Intelligence from Salesforce-sourced `Service_Name` through the `vwservice_group.ServiceNameGrouped` lookup.
- No substantive non-Salesforce data source was identified in either field lineage.
- `vwservice_group.SourceSystem = 'genesys'` is inconsistent with the validated data lineage and is treated as a metadata/load-path anomaly rather than field provenance.
- `customer_account_management` is outside the boundary for this lineage analysis.

**Still to validate**

- Direct bulk report/export coverage of Product2.
- Whether all Product2 records in scope represent CX operational services.
- Exact relationship between Product2 Service Type fields and Service Assignment Rule Service Type values.
- Exact engineering/load mechanism that populates `vwservice_group`, including why its `SourceSystem` metadata is `genesys`.
- Whether Salesforce field history provides useful Product2 configuration-change history.

## Reuse note

This finding is valuable beyond the immediate mapping exercise because the location of Salesforce service configuration has been unclear to the business.

The reusable lesson is not merely "look in Product2". It is:

> Translate the business-visible Salesforce label by tracing the field definition to its underlying object and then separate master configuration, supporting configuration and transactional evidence.

That method should be reused when translating other Salesforce concepts whose UI labels obscure their technical architecture.


## Current state versus Customer Intelligence classification

### Architecture boundary

For this mapping, the model is:

```text
SALESFORCE
Product2.Name
        ↓
Case.Service_Name__c
        ↓
CUSTOMER INTELLIGENCE
Service_Name → sf_service_name
        ↓ lookup by Service_Name
vwservice_group.ServiceNameGrouped
        ↓
Service_Group → ci_service_name_grouped
        ↓
SERVICE EXPERIENCE MAPPING
```

Interpret the fields as follows:

- **`sf_service_name`** — direct Salesforce-derived service name represented in Customer Intelligence.
- **`ci_service_name_grouped`** — Customer Intelligence-maintained grouped classification built from Salesforce-derived service names.

### Lineage status

| Field | Customer Intelligence field | Lineage status |
| --- | --- | --- |
| `sf_service_name` | `vwcase.Service_Name` | **SALESFORCE_ONLY** |
| `ci_service_name_grouped` | `vwcase.Service_Group` | **DERIVED_IN_CUSTOMER_INTELLIGENCE_FROM_SALESFORCE** |

This distinction is important:

> Both fields are Salesforce-rooted, but only `sf_service_name` is a direct Salesforce passthrough. `ci_service_name_grouped` is a CI classification overlay on Salesforce service names.

The `genesys` value present in the reference table's `SourceSystem` metadata does not override the validated field lineage.

## Databricks reusable access pattern

### Purpose

Expose the two validated service fields used by the mapping through Customer Intelligence without bringing in unrelated schemas.

### Source boundary

Use the Customer Intelligence lineage for these outputs.

Do not use `customer_account_management`.

### Fields in scope

1. **`sf_service_name`**
   - Customer Intelligence field: `Service_Name`
   - Origin: Salesforce Product2 `Name` via Case `Service_Name__c`
   - Status: `SALESFORCE_ONLY`

2. **`ci_service_name_grouped`**
   - Customer Intelligence field: `Service_Group`
   - Derivation: `vwservice_group.ServiceNameGrouped`, joined using Salesforce-derived `Service_Name`
   - Status: `DERIVED_IN_CUSTOMER_INTELLIGENCE_FROM_SALESFORCE`

### Minimal reusable output

```text
sf_service_name
ci_service_name_grouped
```

Add technical traceability fields only where needed for QA or refresh control.

### Validation controls

On refresh or rebuild:

1. confirm `Service_Name` remains sourced from the Salesforce Case/Product lineage;
2. confirm `Service_Group` still resolves through the same CI grouping lookup;
3. test unmatched or ambiguous service names rather than silently forcing a grouping;
4. keep any metadata/source-system anomaly separate from field lineage;
5. do not add Theme unless a future use case specifically requires it.

### Governance rule

The Databricks Customer Intelligence layer is a **translation/analytical layer**, not a replacement source of truth.

- Salesforce is the originating business system for `sf_service_name`.
- Customer Intelligence owns the grouping logic represented by `ci_service_name_grouped`.
- `ci_service_name_grouped` is Salesforce-rooted but should not be described as a native Salesforce field.
- `customer_account_management` is outside the scope of this lineage.
- The Service Catalogue remains authoritative for its organisational service definitions.
- Metadata such as `SourceSystem` must not be used as provenance evidence when it conflicts with validated field-level lineage.
