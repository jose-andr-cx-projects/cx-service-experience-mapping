# Databricks Pattern — Salesforce Service Context

## Purpose

Provide a reusable Databricks/Genie pattern for exposing existing Salesforce service configuration while preserving the distinction between **current operational state** and the **Customer Intelligence forward classification**.

This pattern is derived from the Salesforce Architecture Translator finding documented in:

`docs/architecture-translator-salesforce-service-configuration.md`

## Architecture principle

```text
Salesforce Product2
CURRENT OPERATIONAL STATE
        ↓
Customer Intelligence
ServiceName / ServiceNameGrouped
FORWARD CLASSIFICATION
        ↓
Service experience mapping and analytics
```

Product2 contains the current Salesforce configuration and its legacy. It is evidence of how Salesforce operates today, not the target taxonomy.

The Customer Intelligence Service Name / Service Group schema is the line in the sand for consistent classification moving forward.

## Sources

| Layer | Databricks / Salesforce source | Use |
| --- | --- | --- |
| Current service configuration | `datahub_refined_source.salesforce.vwproduct2` | Product2 master/current state |
| Forward classification | `datahub_refined.customer.vwservice_group` | Service Name / Service Group / Theme |
| Operational evidence | Salesforce Case source/refined views | Activity and historical observation |
| Assignment configuration | `Service_Assignment_Rule__c` | Confirmed in Salesforce; standalone replication not currently available |

## Required controls

- Inspect metadata before querying; do not assume source fields.
- One reusable output row per Product2 Id.
- Use Product2 Id for referential joins.
- Do not assume Product2 Name is unique.
- Preserve inactive Product2 records unless explicitly filtered by the use case.
- Preserve NULLs.
- Preserve unmatched current-state Product2 records.
- Never force Product2 legacy values into the forward classification.
- Do not use Cases to reconstruct a Product2 field that is available directly.
- Do not claim assignment-rule configuration is available from Databricks until the source object is replicated.

## Classification status

```text
MATCHED
UNMATCHED_CURRENT_STATE
AMBIGUOUS
REVIEW_REQUIRED
```

## Canonical output

Use semantic aliases and map them to confirmed source columns at runtime:

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

## QA contract

Every refresh/build should report:

1. Product2 row count.
2. Active and inactive counts.
3. Product2 Id uniqueness.
4. Duplicate Product2 Names.
5. Duplicate semantic keys.
6. Matched classification count/rate.
7. Unmatched current-state count/rate.
8. Ambiguous classification count.
9. NULL rates for key fields.
10. Latest source timestamp / freshness.

Changes in these results are signals to investigate, not values to silently clean.

## Genie prompt

```text
Build a current-state Salesforce service configuration context from Databricks.

Treat datahub_refined_source.salesforce.vwproduct2 as the source for current Salesforce Product2 configuration.

Treat datahub_refined.customer.vwservice_group as the Customer Intelligence forward classification for Service Name, Service Group and Theme.

Architecture rule:
- Product2 represents current operational state, including legacy from architectural changes since 2017.
- Product2 is not the target taxonomy.
- Customer Intelligence ServiceName / ServiceNameGrouped is the line-in-the-sand classification moving forward.
- Unmatched Product2 records must be retained and flagged, not forced into the classification.

Before querying:
1. inspect both schemas;
2. show confirmed columns and data types;
3. identify candidate join fields;
4. test join uniqueness and cardinality.

Build one output row per Product2 Id.

Use Product2 Id for Salesforce referential joins.
Do not assume Name is unique.
Preserve active and inactive records and NULL values.
Do not infer Product2 configuration from Cases.
Do not invent missing fields or relationships.

Add classification_match_status:
MATCHED
UNMATCHED_CURRENT_STATE
AMBIGUOUS
REVIEW_REQUIRED

Report QA:
- Product2 total
- active/inactive
- Id uniqueness
- duplicate Names
- duplicate semantic keys
- matched
- unmatched current-state
- ambiguous
- key-field NULL rates
- source freshness

Where operational Case evidence is required, join Case ProductId to Product2 Id and keep transactional measures separate from configuration.

Service_Assignment_Rule__c is confirmed in Salesforce but is not currently available as a standalone replicated Databricks source. Do not reconstruct it from Case data and call it current assignment-rule configuration.

Before creating any persistent table or view, return:
A. source-to-output field mapping
B. proposed join logic and cardinality
C. QA results
D. unavailable fields
E. proposed output schema
for approval.
```

## Intended use

Use this pattern to generate a refreshable Salesforce context extract for:

- the CX service-experience mapping workbook;
- service measurement discovery;
- configuration QA;
- mapping exceptions;
- future analytical work requiring a consistent bridge between Salesforce current state and the Customer Intelligence classification.

Do not use it to redesign Salesforce or to treat legacy Product2 structure as the future service model.
