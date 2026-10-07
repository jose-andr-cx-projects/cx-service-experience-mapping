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

### 2. Customer Intelligence grouped-service reference — analytical classification

Databricks source identified during discovery:

```text
datahub_refined.customer.vwservice_group
```

For this architecture translation, use only:

- `ServiceNameGrouped` → semantic alias `ci_service_name_grouped`

Use `ci_service_name_grouped` only as a Customer Intelligence grouping applied to Salesforce service context.

Do not treat `vwservice_group` as a replacement for Product2 configuration.

**Source-lineage control:** `ci_service_name_grouped` is not currently proven to be Salesforce-only at source. The repository records observed `SourceSystem` values of `genesys`. Therefore the field must be treated as CI enrichment of Salesforce service records unless and until the contributing rows are explicitly validated as Salesforce-derived.

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

SUPPORTING CLASSIFICATION
vwservice_group
ci_service_name_grouped
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
| What forward grouped classification is applied? | `vwservice_group.ServiceNameGrouped` as `ci_service_name_grouped`, with source-lineage validation |
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

**Confirmed through Databricks discovery; requires source-governance validation**

- `datahub_refined.customer.vwservice_group` exposes `ServiceNameGrouped`, used here as `ci_service_name_grouped`.
- Case-level refined/source views expose service-related transactional fields.
- Current evidence does **not** establish that `ci_service_name_grouped` is sourced only from Salesforce; observed `SourceSystem` values include `genesys`.

**Still to validate**

- Direct bulk report/export coverage of Product2.
- Whether all Product2 records in scope represent CX operational services.
- Exact relationship between Product2 Service Type fields and Service Assignment Rule Service Type values.
- Whether the `vwservice_group` rows used to populate `ci_service_name_grouped` can be constrained and validated as Salesforce-derived only.
- Whether Salesforce field history provides useful Product2 configuration-change history.

## Reuse note

This finding is valuable beyond the immediate mapping exercise because the location of Salesforce service configuration has been unclear to the business.

The reusable lesson is not merely "look in Product2". It is:

> Translate the business-visible Salesforce label by tracing the field definition to its underlying object and then separate master configuration, supporting configuration and transactional evidence.

That method should be reused when translating other Salesforce concepts whose UI labels obscure their technical architecture.


## Current state versus forward classification

### Architecture caveat

Salesforce Product2 should be treated as the **current operational state**, not as the ideal or target service architecture.

For this mapping, keep the translation deliberately narrow:

```text
CURRENT SALESFORCE STATE
sf_service_name
(from Product2.Name)
        ↓
CI GROUPED CLASSIFICATION
ci_service_name_grouped
(from vwservice_group.ServiceNameGrouped)
        ↓
SERVICE EXPERIENCE MAPPING
```

Interpret the two fields as follows:

- **`sf_service_name`** answers: what is the service called in Salesforce now?
- **`ci_service_name_grouped`** answers: what grouped Customer Intelligence classification is applied for analysis and mapping?

### Lineage status

| Field | Immediate source | Salesforce-only status |
| --- | --- | --- |
| `sf_service_name` | `datahub_refined_source.salesforce.vwproduct2.Name` / Salesforce Product2 `Name` | **Confirmed Salesforce-derived** |
| `ci_service_name_grouped` | `datahub_refined.customer.vwservice_group.ServiceNameGrouped` | **Not yet confirmed Salesforce-only** |

The second field is a CI classification layer. Current repository evidence records `vwservice_group.SourceSystem = genesys` in observed rows, so the source cannot currently be described as Salesforce-only without explicit lineage validation.

If the downstream workbook is intended to contain Salesforce context only, populate `ci_service_name_grouped` only for rows joined to a Salesforce Product2 record through a validated relationship. This makes the output Salesforce-scoped while preserving the fact that the grouping itself is maintained in a CI reference view.

## Databricks reusable access pattern

### Purpose

Provide a repeatable way for analysts, Genie and downstream mapping artefacts to expose the two service fields needed for this mapping without reconstructing them from Cases.

### Source layers

1. **Salesforce service name**
   - Source: `datahub_refined_source.salesforce.vwproduct2`
   - Source field: `Name`
   - Semantic alias: `sf_service_name`
   - Grain: one Salesforce Product2 record.
   - Stable technical join key: Product2 `Id`.
   - Salesforce lineage: confirmed.

2. **Customer Intelligence grouped classification**
   - Source: `datahub_refined.customer.vwservice_group`
   - Source field: `ServiceNameGrouped`
   - Semantic alias: `ci_service_name_grouped`
   - Use only as an enrichment of Salesforce Product2 context.
   - Salesforce-only lineage: not yet confirmed; validate the source relationship/filter before describing it as Salesforce-derived.

3. **Observed operational evidence**
   - Salesforce Case source/refined views may be used for activity or historical observation.
   - Do not use Case records to define either `sf_service_name` or `ci_service_name_grouped`.

### Canonical reusable output

Keep the reusable mapping context minimal:

```text
sf_product_id
sf_service_name
ci_service_name_grouped
classification_match_status
classification_review_note
sf_last_modified_datetime
source_refresh_datetime
```

### Classification status

Use:

- `MATCHED` — Product2 has one validated grouped classification.
- `UNMATCHED_CURRENT_STATE` — Product2 exists but has no validated grouped classification.
- `AMBIGUOUS` — more than one candidate grouped classification exists or the join is not unique.
- `REVIEW_REQUIRED` — source lineage or mapping evidence is insufficient.

Do not force unmatched Salesforce service records into a CI grouping.

### Reusable Genie / analyst procedure

1. Inspect `vwproduct2` and confirm Product2 `Id` and `Name`.
2. Expose `Name` as `sf_service_name`.
3. Inspect `vwservice_group` and confirm `ServiceNameGrouped` plus the field(s) required to validate source lineage and the join.
4. Test whether the candidate `vwservice_group` rows used for enrichment are Salesforce-derived only.
5. If Salesforce-only lineage cannot be proven, label `ci_service_name_grouped` as CI enrichment rather than Salesforce source data.
6. Join the grouping only through a validated relationship to Product2.
7. Preserve unmatched Product2 records.
8. Report match rate, unmatched count, ambiguous count and source freshness.

### Reusable Genie prompt

```text
Build the minimal Salesforce service context needed for the CX service-experience mapping.

SOURCE RULES

1. Use datahub_refined_source.salesforce.vwproduct2 as the source of Salesforce services.
2. Map Product2 Name to sf_service_name.
3. Use datahub_refined.customer.vwservice_group.ServiceNameGrouped only as ci_service_name_grouped.
4. Before calling ci_service_name_grouped Salesforce-derived, verify the lineage of the vwservice_group rows used in the join. Current project evidence has observed SourceSystem = genesys, so do not assume Salesforce-only provenance.
5. If Salesforce-only provenance cannot be established, describe ci_service_name_grouped as Customer Intelligence enrichment applied to Salesforce Product2 records.
6. Use Product2 Id as the stable Salesforce technical key. Do not assume Product2 Name is unique.
7. Preserve unmatched Product2 records and NULL values.
8. Do not infer either field from Case records.
9. Do not invent joins, field names or source-system provenance.

OUTPUT

Return one row per Product2 record with:
sf_product_id
sf_service_name
ci_service_name_grouped
classification_match_status
classification_review_note
sf_last_modified_datetime
source_refresh_datetime

VALIDATION

Report:
- Product2 total
- Product2 Id uniqueness
- duplicate sf_service_name values
- matched / unmatched / ambiguous classification counts
- the exact join used
- the exact source-lineage test for ci_service_name_grouped
- whether the ci_service_name_grouped rows used are proven Salesforce-derived only
- source freshness

Before creating a persistent table or view, show the source-to-output mapping, join logic and lineage result for approval.
```

### Governance rule

The reusable Databricks layer is a **translation/context asset**, not a replacement source of truth.

- Salesforce remains authoritative for `sf_service_name`.
- Customer Intelligence remains the source of `ci_service_name_grouped`.
- Do not describe `ci_service_name_grouped` as Salesforce-derived unless its contributing rows have been explicitly validated as Salesforce-only.
- The Service Catalogue remains authoritative for its organisational service definitions.
- Databricks makes the layers reusable and joinable; it does not erase their different meanings or authority.
