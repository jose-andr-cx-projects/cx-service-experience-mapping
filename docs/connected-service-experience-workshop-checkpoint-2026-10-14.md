# Connected service experience mapping — workshop checkpoint

**Date:** 14 October 2026  
**Status:** Agreed direction; connections and UX interpretations remain working hypotheses.

## Purpose

Connect the existing Service Catalogue, Salesforce/Customer Intelligence (CI), and website content structures to understand customer experience and discover reusable CX measurement opportunities. **Do not create three independent experience maps or a new enterprise service taxonomy.**

## Model and source boundaries

- **Service Catalogue:** authoritative organisational spine, `Service Area → Service → Service Offering`. Current workbook baseline: **198 offerings**, with a protected one-row-per-offering `Summary` grain.
- **Salesforce/CI:** independent operational and reporting layer. `Product2` is the Salesforce operational service configuration object; Cases provide activity evidence. CI `ServiceNameGrouped` is a standardised reporting overlay on historically evolving Salesforce service names, **not** an organisational Service Catalogue hierarchy or an existing confirmed catalogue-to-CRM relationship.
- **Website sitemap:** independent customer-facing information/navigation layer, with topics, pages, guidance and actions. It is not a confirmed catalogue mapping.
- **Seven controlled CX Interaction Modes:** Access, Apply, Comply, Have a Say, Report an Issue, Take Part, Transact. Apply these as a shared **customer-intent and expectation lens** across reviewed service connections, not as fields inferred automatically from source names, reporting groups or Salesforce Record Types.
- **Information needs:** explore what customers need to find, understand, decide and confirm for each Mode (e.g. eligibility, obligations, cost, timeframes, progress, ownership, resolution). These are analysis prompts, **not** an additional controlled taxonomy.
- **Customer Segments:** design lenses for testing whether expectations vary, not deterministic assignments.

The intended relationship model is **many-to-many**: one catalogue offering can connect to multiple Product2/CI representations, multiple website entry points, and several relevant Modes. A Mode transition (`Initial → Targeted`) is an experience hypothesis and must not be mistaken for a confirmed technical source relationship.

## Miro board structure (revised)

**01 · Reference sources**  
Keep Service Catalogue, Salesforce configuration/CI, and website sitemap reference visuals. Existing semantic affinity images are helpful navigation aids only.

**02 · Connect our service structures**  
Anchor on **selected Service Catalogue offerings** and draw candidate/verified connections to Salesforce/CI records and relevant website pages. Distinguish **confirmed**, **working hypothesis**, and **Mode transition** connectors; naming similarity alone does not establish a relationship.

**03 · Discover shared customer experiences**  
Apply the seven Modes and corresponding information-needs prompts to the **connected offering context**. Challenge assumptions with customer Segments and service knowledge. The prior eight experience affinity clusters are superseded as the working lens. Semantic grouping is reference only, not three independent classifications.

**04 · Build reusable CX intelligence**  
Discuss and review in Miro; translate reviewed source connections into refresh-safe structured Excel relationships, retaining original keys, evidence and review status. Use cross-service expectations and available evidence to identify **candidate measurement opportunities**, not prematurely governed KPIs.

**05 · Outcome**  
A connected, documented service-experience relationship model supporting a small, evidence-aware **Measurement Opportunity Register** and handoff into subsequent CX measurement and service improvement work.

## Excel implementation direction (not yet implemented)

- **Do not modify** the authoritative `Service_catalogue` or expand the 198-row `Summary` grain via direct many-to-many joins.
- Store Miro-reviewed relationships in an ordinary, refresh-safe `Experience_connections` input table or equivalent approved review-input design; one row per connection with a stable local connection ID, catalogue offering key, source type, source record key, original source label, evidence, review status and optional Mode/transition/information-need interpretation.
- Prefer stable `Product2.Id` for Salesforce, exact website URL/source ID for website, and verified unique composite catalogue key where needed. Treat CI group as reporting context, not a surrogate verified Salesforce-to-catalogue join.
- Do not assume that earlier provisional naming normalisations represent validated unique services. The **Ask-only** suffix exploration is an analytical review candidate, not an approved modification to source CI classification; leave other record types unchanged.
- Preserve blank/open/not-represented relationships rather than manufacture matches.
- Only build and test mapping logic against the **latest** workbook after inspecting its current query/table schema. No workbook modification is recorded by this checkpoint.

## Next session: one contained pilot

1. Open the latest workbook and validate its `Summary`, `Salesforce_context`, `Website_context` and human-review persistence structure.
2. Pick **one representative Catalogue Service Offering** (parking permitting is an illustrative candidate, **not** a verified mapping).
3. With Dennis, draw 1–3 evidence-backed or explicitly provisional CRM/website connections, then examine Mode(s), information needs and possible `Initial → Targeted` transitions.
4. Trial capture of those connections in Excel without altering source classifications or multiplying the catalogue `Summary`.
5. Check whether the pilot reveals one decision-relevant cross-service CX measurement question before expanding the sample.

## Open for human review

- Which exact service offering starts the pilot?
- What evidence establishes each Salesforce/website connection?
- Which Modes genuinely describe each customer interaction, and which information needs recur?
- Does the latest workbook already provide sufficient durable reviewed-connection storage, or is one additional input table needed?

**Guardrail:** Stop at enough connected evidence to demonstrate useful CX measurement opportunities; do not undertake exhaustive mapping of 198 offerings.
