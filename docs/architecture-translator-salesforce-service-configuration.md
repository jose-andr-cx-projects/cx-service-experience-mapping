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
