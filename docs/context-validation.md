# Context Validation and Retrieval

## Purpose and evidence boundary

Prepare enough service context to resolve mapping ambiguity and return to measurement-opportunity validation. This is supporting project work, not a new architecture or an enterprise catalogue-completion exercise.

Review recorded **2 October 2026**. This is the workbook assessment date; the supplied transcript does not establish the meeting date.

Sources examined:
- supplied working workbook `Service Catalogue to Sale1.xlsx`;
- supplied transcript `Services ,Modes & segments.vtt`;
- live repository method, dictionary, decisions and validation plan.

The updated working workbook is `Service-Catalogue-Context-and-Checks.xlsx`. Keep the workbook, raw transcript and configuration extracts in the appropriate working/organisational environment. This repository retains method, caveated findings and reusable retrieval guidance.

## Workbook assessment

| Finding | What the evidence establishes | Practical implication |
| --- | --- | --- |
| 645 mapping rows, 198 distinct catalogue offerings | Counts from the supplied workbook snapshot | Context preparation is not validation of every association. |
| 160 distinct Salesforce group/name pairs | Distinct raw pairs with a populated service name | These are labels, not verified distinct Salesforce record IDs. |
| 117 pairs associated with multiple catalogue offerings | Candidate association ambiguity in the mapping | Preserve candidates until source descriptions support a relationship. |
| No exact duplicates across the five source fields | No fully repeated catalogue/group/name row found | Do not confuse repeated names across offerings with exact duplicates. |
| 179 rows with literal NULL Salesforce service names | Missing representation in this workbook | Does not prove absence from CRM configuration or another system. |
| Business and Investment reviewed | Transcript 00:23:22–00:24:25 reports area review; 00:08:18–00:08:48 supports leaving unclear enquiries blank | Log review coverage independently of classification completion. |
| Arts, Events and Tourism partially reviewed | Transcript 00:24:49–00:32:49 exposes busking and ownership/context questions | Validate the association rather than force customer navigation and organisational hierarchies to agree. |

These counts are a dated diagnostic snapshot. Recompute after source changes.

## Questions retained for verification

- **Service lifecycle:** transcript 00:19:35–00:21:29 reports a shopfront program closed in March 2023 while its CRM service appeared active. Current closure/active state remains unverified.
- **Record type, SLA and ownership:** transcript 00:06:43–00:08:11 reports an outdoor-dining configuration with Report type, a 30-day SLA and possibly stale ownership. These observations do not override a current system extract or determine the customer Mode.
- **Funding enquiry versus journey:** transcript 00:17:24–00:18:44 distinguishes grants information from an interpreted Apply → Transact funding journey. Do not assume the CRM enquiry record represents application and payment.
- **Busking coverage:** no busking label appeared in the supplied mapping. Transcript 00:25:43–00:31:37 identifies potentially different business-permit and arts-enquiry representations. Exact IDs and catalogue association need verification.
- **Unclear labels and encoding:** the workbook contains a one-character service label and three catalogue labels with encoding artefacts. Preserve raw values while verifying meaning or correcting the source.
- **Workbook dropdown scope:** original validation ranges overlap adjacent/future columns. Before expanding the original mapping table, limit Mode validation to F2:G646, Resident Segment validation to H2:H646 and Non-Resident Segment validation to I2:I646, then extend deliberately as needed.
- **Secondary customer lens:** landlords were mentioned in the discussion; no additional segment field or automatic assignment was introduced.

## Context layers in the working workbook

| View | Purpose |
| --- | --- |
| Context mapping | Existing mapping rows linked to supporting context. |
| Catalogue context | One row per catalogue offering: description, working context, customer task, public content structure, ownership evidence and review state. |
| Salesforce context | One row per current group/name pair: stable-ID/description fields, configuration observations, source freshness and candidate associations. |
| Check log | Review coverage, findings, evidence, follow-up and resolution. |
| Retrieval guide | Genie prompt and conditional Lightning inspection route. |

The original seven sheets and existing classifications were preserved. Source Mode edits and context-description edits were tested to flow into the linked view. Original worksheet/table parts were checked for preservation; new-sheet formulas were checked for errors. Native Excel application behaviour was not independently exercised.

Authoritative catalogue/Salesforce descriptions remain blank until retrieved. Name-based context is an interpretation. The workbook's direct row links are a snapshot: rebuild them after adding/removing rows or changing catalogue/Salesforce identities.

## Corporate website mapping

Use website section → topic → subtopic → relevant service page as a customer-facing layer beside the catalogue hierarchy. An internal capability may legitimately have no dedicated public page.

Live corporate website reads returned HTTP 403. Some published navigation and official link targets were recovered through an older cached Business Concierge page and an accessible official Participate Melbourne reference. Suggested topics and offering associations remain provisional, and current breadcrumbs/canonical pages are not verified.

References:
- [Official Business Concierge reference](https://www.melbourne.vic.gov.au/business/Pages/business-concierge-service.aspx) — recovered published navigation; live verification blocked.
- [Business ready](https://participate.melbourne.vic.gov.au/prepared-communities/business-ready) — official supporting context and corporate link.

The smallest useful next input is one accessible corporate sitemap or CMS navigation export. Confirm current hierarchy, canonical URLs, page titles, relevant content and offering associations in bulk. Do not infer delivery ownership from navigation.

## Read-only Genie prompt

Paste the following into the relevant organisational Genie Agent/Space:

```text
Find City of Melbourne Salesforce service CONFIGURATION data for the CX service-experience mapping. Complete accessible discovery and extraction in one read-only pass. I need configuration records, not descriptions of individual Cases.

1. State the Agent/Space, available tables and search scope. Search permitted metadata and attached sources for service/group, description, active/effective dates, record type, SLA, responsible team/officer, queue and routing. Custom objects, reference tables, custom metadata and settings are candidates, not assumed names. Inspect Unity Catalog information_schema.tables and columns if available. Distinguish CONFIGURATION FOUND, CASE LABELS ONLY, NOT VISIBLE, ACCESS DENIED and UNKNOWN. Missing results establish only that data was not visible in this scope. Do not invent table, object or field API names.

2. For candidate sources show catalog.schema.table, original Salesforce object where known, grain, actual fields/types, descriptions, row counts and available ingestion timestamps. Distinguish configuration from Case-derived labels and table/column comments. Show SQL and join keys. Exclude customer/account data and Case subjects/descriptions.

3. Investigate outdoor-dining configuration, start-a-business services, business funding, shopfront activation and all busking/street-entertainment/Big Issue services. Preserve exact raw names, group NULL variants and stable IDs. Include inactive records and investigate one-character/placeholder labels. Transcript observations suggest a closed program may still be active and an outdoor-dining record may have Report type, a 30-day SLA and stale ownership. Treat these only as leads to verify.

4. If configuration exists, export all service configuration records with these canonical columns, aliasing only fields actually discovered:
sf_record_id, sf_service_group, sf_service_name, sf_description, record_type, is_active, sla_value, sla_unit, responsible_team, responsible_officer, config_source, source_last_modified, extract_timestamp.
Provide the actual API-field map. Add routing/queue, effective dates and knowledge references separately where available. Leave unavailable fields NULL and explain whether not ingested, hidden by permissions or absent from the source schema. Specify SLA calendar/business clock and start/stop basis where available. Preserve source values.

5. Identify current versions using documented effective timestamps where history exists. Retain version counts. Do not keep an arbitrary first duplicate. Report duplicate stable IDs and conflicting group/name pairs. Show extraction SQL, completeness counts and freshness limitations. Return downloadable CSV where supported, otherwise the result table and executable SQL.

6. If blocked, finish accessible checks, state what remains unknown and identify the smallest additional dataset or permission needed. Zero Case usage does not prove inactive service or absent configuration. Do not change data or configuration.
```

Genie and information-schema visibility are permission-scoped. The prompt is prepared guidance, not evidence that configuration has been found.

## Salesforce Lightning fallback

1. **Inspect records:** App Launcher → search Services or Service Configuration in the existing Council app → open the relevant record → Details and Related. The exact CoM tab/object is unverified.
2. **Discover the object:** Gear → Setup → Object Manager → search Service. If unclear, open Case → Fields & Relationships and inspect the service field's API name/type/referenced object. A lookup can identify the configuration object; a picklist requires inspection of values/dependencies.
3. **Inspect configuration fields:** on the discovered object, locate description, active status, group, record type, SLA and ownership fields, then inspect actual records. Capture ID and Last Modified. Layout or field permissions can hide an existing field.
4. **Check alternate stores:** Setup Quick Find → Custom Metadata Types / Custom Settings → relevant Manage Records or Manage view where accessible. These are possible stores, not confirmed CoM implementations.
5. **Inspect routing separately:** Setup → Quick Find → Case Assignment Rules → active rule → entry criteria, order and assigned queue/user. If relevant routing is absent, inspect active record-triggered Flows or custom routing records with the CRM owner. Assignment rules are not the complete service definition.
6. **Export once:** if a service-configuration report type exists, Reports → New Report → discovered report type → include active and inactive records → add required fields → Run → Export → Details Only → CSV. Otherwise obtain one read-only configuration export and API-field map from CRM. Do not substitute Case exports for configuration.
7. **Review exceptions only:** resolve lifecycle, ownership, SLA and association questions using current records and source evidence. No configuration mutation is part of this inspection.

Technical references:
- [Salesforce API-name discovery](https://help.salesforce.com/s/articleView?id=000385500&language=en_US&type=1)
- [Salesforce assignment-rule location](https://help.salesforce.com/s/articleView?id=000385609&language=en_US&type=1)
- [Databricks Genie setup](https://docs.databricks.com/aws/en/genie-agents/set-up)
- [Databricks information schema](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-information-schema)

## Next contained action

Run the Genie prompt once and return the configuration export. Join by confirmed stable ID where possible; otherwise accept only an unambiguous current group/name match. Keep conflicting candidates visible. Obtain website structure in one export if accessible, then resolve only the remaining material exceptions.

Return to the representative interaction sample and Measurement Opportunity Register once context is sufficient. Do not turn configuration discovery into an enterprise cleanup or a new KPI framework.
