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

### 2. Service Group reference — analytical classification

Databricks source identified during discovery:

```text
datahub_refined.customer.vwservice_group
```

Observed fields include:

- `ServiceName`
- `ServiceNameGrouped`
- `Theme`
- `isActive`
- `SourceSystem`

Use this as a reference/enrichment layer for **Service Group and Theme**, subject to source validation.

Do not treat it as a replacement for Product2 configuration.

A specific validation question remains open because the reference view was described as Salesforce-related while its observed `SourceSystem` values were `genesys`.

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
Service Name → Service Group → Theme
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
| What Service Group / Theme is used analytically? | `vwservice_group`, with source validation |
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
Service Group / Theme enrichment
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

Detailed Salesforce configuration belongs in the Salesforce context layer and can be joined to the main mapping by the relevant service identifier/name.

## Validation status

**Confirmed directly in Salesforce**

- Case `Service_Name__c` is a formula using `Product.Name`.
- Product / Service uses the standard Salesforce `Product2` object.
- Product2 contains service-specific configuration fields.
- `Service_Assignment_Rule__c` is a deployed custom configuration object.
- Service Assignment Rule uses a Service Type global value set and dependent Service Subtype.
- Service Assignment Rules are reportable in Salesforce.

**Confirmed through Databricks discovery; requires source-governance validation**

- `datahub_refined.customer.vwservice_group` provides Service Name / Service Group / Theme reference data.
- Case-level refined/source views expose service-related transactional fields.

**Still to validate**

- Direct bulk report/export coverage of Product2.
- Whether all Product2 records in scope represent CX operational services.
- Exact relationship between Product2 Service Type fields and Service Assignment Rule Service Type values.
- Governance/source semantics of `vwservice_group.SourceSystem = genesys`.
- Whether Salesforce field history provides useful Product2 configuration-change history.

## Reuse note

This finding is valuable beyond the immediate mapping exercise because the location of Salesforce service configuration has been unclear to the business.

The reusable lesson is not merely "look in Product2". It is:

> Translate the business-visible Salesforce label by tracing the field definition to its underlying object and then separate master configuration, supporting configuration and transactional evidence.

That method should be reused when translating other Salesforce concepts whose UI labels obscure their technical architecture.


## Current state versus forward classification

### Architecture caveat

Salesforce Product2 should be treated as the **current operational state**, not as the ideal or target service architecture.

The Salesforce service configuration has accumulated legacy from multiple architectural changes since 2017. Product2 therefore remains essential for traceability and for understanding how services are configured today, but its structure should not be reproduced uncritically in future service mapping or analytics.

The Customer Intelligence team developed the `ServiceName` / `ServiceNameGrouped` schema represented in `datahub_refined.customer.vwservice_group` to rationalise this legacy complexity.

For this project, that schema is the **line in the sand moving forward**:

```text
CURRENT OPERATIONAL STATE
Salesforce Product2
        ↓ translate / rationalise
FORWARD CLASSIFICATION
Customer Intelligence ServiceName / ServiceNameGrouped
        ↓
SERVICE EXPERIENCE MAPPING
Service Catalogue → CX Modes → Segments → Measurement
```

Interpret the layers as follows:

- **Product2** answers: what is configured in Salesforce now?
- **ServiceName / ServiceNameGrouped** answers: how should Salesforce services be consistently classified for analysis and mapping moving forward?
- **Service Catalogue** remains the organisational service model being mapped to the Salesforce/CX layer.
- A Product2 record that does not map to the Customer Intelligence classification is a **classification gap or legacy exception to investigate**, not automatically a missing category that should be added to the forward classification.

This distinction prevents legacy Salesforce architecture from becoming the target architecture by accident.

## Databricks reusable access pattern

### Purpose

Provide a repeatable way for analysts, Genie and downstream mapping artefacts to use existing Salesforce configuration without reconstructing it from Cases.

### Source layers

Use these layers in order:

1. **Current Salesforce configuration**
   - `datahub_refined_source.salesforce.vwproduct2`
   - Grain: one Salesforce Product2 record.
   - Stable technical join key: Product2 `Id`.
   - Prefer `Service_Unique_Name__c` as a semantic/business identifier where appropriate.
   - Do not rely on `Name` as a unique key.

2. **Forward Customer Intelligence classification**
   - `datahub_refined.customer.vwservice_group`
   - Relevant fields: `ServiceName`, `ServiceNameGrouped`, `Theme`, `isActive`, `SourceSystem`.
   - Use to enrich Product2 with the rationalised Service Name / Service Group / Theme classification.
   - Do not assume every Product2 record should have a match.

3. **Observed operational evidence**
   - Salesforce Case source/refined views.
   - Join to Product2 using the Product relationship / Product ID where available.
   - Use for activity, volumes, outcomes and historical observation, not to define current Product2 configuration.

4. **Specialist assignment configuration**
   - `Service_Assignment_Rule__c` is confirmed in Salesforce but is not currently replicated as a standalone Databricks source.
   - Treat assignment-rule configuration as unavailable in the reusable Databricks layer until replication is established. Do not reconstruct it from Case labels as if it were current master data.

### Canonical reusable output

The preferred reusable Databricks asset should expose one row per Product2 record and preserve both the current-state and rationalised layers.

Minimum recommended fields:

```text
sf_product_id
sf_service_unique_name
sf_product_name
sf_service_friendly_name
sf_service_description
sf_service_type
sf_reporting_service_type
sf_is_active
sf_service_calendar
sf_sla_customer_response
sf_sla_warning_notification
sf_auto_close
sf_auto_close_outcome
sf_disable_auto_notifications
sf_disable_auto_notifications_creation
sf_creation_description
sf_creation_description_2
sf_creation_description_sms
sf_work_order_auto_create

service_name
service_group
service_theme

classification_match_status
classification_review_note

sf_created_datetime
sf_last_modified_datetime
source_refresh_datetime
```

Names in the reusable output are semantic aliases. Implementations must first inspect the actual Databricks schema and map only confirmed source columns.

### Classification status

Use a controlled status rather than forcing unmatched Product2 records into the forward classification:

- `MATCHED` — Product2 can be mapped confidently to the Customer Intelligence Service Name / Service Group classification.
- `UNMATCHED_CURRENT_STATE` — Product2 exists but has no confident forward-classification match.
- `AMBIGUOUS` — more than one candidate classification exists or the join is not unique.
- `REVIEW_REQUIRED` — evidence is insufficient or a business decision is needed.

Do not interpret `UNMATCHED_CURRENT_STATE` as an error by default. It may represent legacy, technical, inactive or otherwise intentionally excluded Salesforce configuration.

### Reusable Genie / analyst procedure

When refreshing or rebuilding the Salesforce configuration context:

1. Inspect `vwproduct2` metadata and confirm the available columns and data types.
2. Confirm row grain and uniqueness of Product2 `Id`.
3. Retain active **and inactive** Product2 records unless the use case explicitly requires active only.
4. Profile duplicates for `Name` and `Service_Unique_Name__c`; do not silently deduplicate.
5. Join Product2 to `vwservice_group` only through a validated relationship.
6. Preserve unmatched Product2 records and assign a classification status.
7. Report match rate, unmatched count, ambiguous count and freshness.
8. Join Case data through Product ID for operational evidence when required.
9. Keep assignment-rule configuration separate until its source object is replicated.
10. Export or expose the canonical Salesforce context for downstream Power Query, mapping and measurement work.

### Reusable Genie prompt

```text
Build a current-state Salesforce service configuration context from Databricks.

SOURCE RULES

1. Treat datahub_refined_source.salesforce.vwproduct2 as the source for current Salesforce Product2 service configuration.
2. Treat datahub_refined.customer.vwservice_group as the Customer Intelligence forward classification layer for Service Name, Service Group and Theme.
3. Salesforce Product2 represents current operational configuration, including legacy created through architectural changes since 2017. Do not treat Product2 as the target service taxonomy.
4. The Customer Intelligence ServiceName / ServiceNameGrouped schema is the line-in-the-sand classification for analysis and service mapping moving forward.
5. Preserve Product2 records that do not match the forward classification. Flag them; do not invent or force a classification.
6. Do not infer current configuration from Case records when Product2 contains the field.
7. Use Product2 Id for Salesforce referential joins. Do not assume Product2 Name is unique.
8. Preserve inactive records and NULL values.
9. Do not invent object names, field names, joins or missing values.
10. Service_Assignment_Rule__c is not currently available as a standalone replicated Databricks source. Do not reconstruct it from Case data and label it as current assignment-rule configuration.

PROCESS

A. Inspect the actual schemas first and list the confirmed source columns.
B. Confirm Product2 row grain, Id uniqueness, duplicate Names, duplicate Service_Unique_Name__c values and source freshness.
C. Create one output row per Product2 Id.
D. Add available current-state Product2 configuration fields.
E. Enrich with Customer Intelligence ServiceName, ServiceNameGrouped and Theme using a validated join.
F. Add classification_match_status using:
   MATCHED
   UNMATCHED_CURRENT_STATE
   AMBIGUOUS
   REVIEW_REQUIRED
G. Do not discard unmatched Product2 records.
H. Report:
   - total Product2 records
   - active / inactive
   - matched to forward classification
   - unmatched current-state
   - ambiguous
   - duplicate Product2 Names
   - duplicate semantic keys
   - latest source timestamp / freshness
I. Where Case evidence is requested, join using ProductId = Product2.Id and keep transactional metrics separate from configuration fields.

OUTPUT

Return a reusable Salesforce service context with semantic aliases for:
sf_product_id
sf_service_unique_name
sf_product_name
sf_service_friendly_name
sf_service_description
sf_service_type
sf_reporting_service_type
sf_is_active
sf_service_calendar
sf_sla_customer_response
sf_sla_warning_notification
sf_auto_close
sf_auto_close_outcome
sf_disable_auto_notifications
sf_disable_auto_notifications_creation
sf_creation_description
sf_creation_description_2
sf_creation_description_sms
sf_work_order_auto_create
service_name
service_group
service_theme
classification_match_status
classification_review_note
sf_created_datetime
sf_last_modified_datetime
source_refresh_datetime

Before creating a persistent table or view, show the proposed source-to-output field mapping, join logic, QA results and any unavailable fields for approval.
```

### Governance rule

The reusable Databricks layer is a **translation/context asset**, not a replacement source of truth.

- Salesforce remains authoritative for Product2 configuration.
- Customer Intelligence's Service Name / Service Group schema is the preferred forward classification for this mapping and analytical use.
- The Service Catalogue remains authoritative for its organisational service definitions.
- Databricks makes those layers reusable and joinable; it does not erase their different meanings or authority.
