# Meta Ads Analytics
## Public Methodology, Assumptions & Analytical Approach

---

## 1. Purpose

This document explains the analytical approach behind the Meta Ads profitability report, the assumptions used in the current implementation, the main limitations of the available data, and the broader thinking behind how the model could be extended in a real ecommerce environment.

The report is designed to support weekly paid media decision-making. It is not intended to function as a full accounting model, a perfect attribution engine, or a complete ecommerce intelligence stack. Its role is narrower and more practical: to provide a commercially useful view of paid social performance that helps identify what appears strong, weak, scalable, or in need of investigation.

This public version is intended to show the structure of the thinking behind the model rather than disclose full implementation detail.

More detailed supporting notes are provided separately in:

- `model_notes.md`
- `commercial_logic.md`
- `data_dictionary.md`
- `naming_sop.md`
- `utm_sop.md`

---

## 2. What the report is designed to answer

The report is built to answer a focused set of recurring commercial questions.

**1. Are paid social campaigns profitable after variable costs and ad spend?**  
The model answers this by moving beyond spend and revenue alone and evaluating performance through profit-aware reporting that incorporates selected variable costs into the commercial view.

**2. Which markets and product categories are driving the strongest commercial returns?**  
The report answers this through country and category-level views that compare spend, revenue, profitability and action status side by side.

**3. Which creative and funnel combinations appear strongest or weakest?**  
The report answers this by separating creative type and funnel-stage behaviour and surfacing differences between prospecting and retargeting performance at combination level.

**4. How should lower-visibility markets be interpreted where tracked platform revenue may understate true performance?**  
The report addresses this through a tracking-capture adjustment framework for selected consent-restricted markets, allowing the user to model different attribution assumptions.

**5. What should be scaled, watched, paused or investigated next week?**  
The report answers this through an action-oriented view that converts performance analysis into a classified decision framework rather than leaving interpretation entirely to the reader.

The emphasis is on commercial decision-making, not descriptive reporting alone.

---

## 3. Current implementation at a glance

| Area | Current approach | Main limitation | Future direction |
|---|---|---|---|
| Attribution | Ad-linked category approximation | Cross-sell and assist value understated | Ecommerce order-line joins and richer attribution logic |
| Naming | Metadata mapping layer for inconsistent naming | Ongoing maintenance | Stronger naming and UTM governance |
| Tracking | Adjustment layer for selected lower-visibility markets | Assumption, not measured fact | Better validation through broader analytics inputs |
| Cost model | Variable-cost-focused profitability | Excludes fixed overheads and returns | Wider financial and operational integration |
| Funnel logic | Blended evaluation of prospecting and retargeting | Time lag not explicitly modelled | Time-aware funnel linkage |
| Landing page | Not implemented as an active reporting variable | No destination-page comparison | Landing-page classification |
| Placement / surface | Present in source data but not a central reporting view | Not surfaced as a primary diagnostic lens | Placement-level diagnostics |
| Angle / hook | Recognised conceptually but not implemented in full | Can become noisy without strong metadata discipline and sufficient aggregation | Structured creative metadata |

---

## 4. Scope

### Included

- Meta ad performance by date, country, campaign, ad set and ad
- metadata classification for product category, funnel stage and creative type
- variable cost logic using product, packaging, shipping and payment fee assumptions
- adjusted profitability logic for selected lower-visibility markets
- report pages focused on performance, drivers, creative and funnel behaviour, geographic interpretation and action-oriented recommendations

### Not included

- ecommerce order-line attribution
- returns or refund adjustments
- VAT, duties, or broader overhead allocation
- GA4 session-level analysis
- CRM or customer-level analysis
- API-based audience or objective enrichment
- full multi-touch or incrementality modelling
- landing page, placement, hook or angle as fully implemented reporting dimensions

These exclusions are deliberate. The model is scoped to what can be defended analytically from the selected data sources without overstating what the data can reliably support.

---

## 5. Data sources and architecture

The report is powered by a BigQuery pipeline and a Power BI semantic model.

Source data includes Meta Ads export tables and manually maintained helper dimensions for ad metadata, product costs, shipping costs and payment fees.

The warehouse layer consolidates, standardises and structures the source data before it reaches the semantic model. Supporting dimensions are connected in the reporting layer, with key harmonisation handled upstream where practical.

Further modelling detail is covered in `model_notes.md`.

---

## 6. Profitability approach

The report is intentionally profit-aware rather than revenue-only.

Most paid media reporting stops at spend, revenue and ROAS. Those metrics are useful, but they are not sufficient for commercial decision-making in ecommerce. A campaign can appear strong on ROAS while still being loss-making once product cost, packaging, shipping and payment fees are included. A business optimising towards revenue ROAS without accounting for variable costs may therefore be scaling activity that is actively destroying margin.

For that reason, the current model evaluates performance using a variable-cost-aware profitability view rather than revenue alone.

In this report, **Net Profit** should be understood as:

**Revenue minus ad spend and selected variable costs**

It does not represent statutory or management-accounting net profit. It is better understood as an operating profit view for paid media decision-making.

Because returns and refunds are excluded, the report should be read as a contribution-style paid media view rather than realised margin.

The supporting logic for included and excluded costs is summarised separately in `commercial_logic.md`.

---

## 7. Attribution approach

The current report uses a simplified attribution assumption:

**Revenue is attributed to the category associated with the ad that generated the reported conversion value in the Meta export.**

This creates a practical paid-media performance model, but not a full order-level attribution model.

That distinction matters. Platform-level export data provides useful performance signals, but it does not provide:

- full order-line composition
- full cross-sell truth
- a clean bridge between ad interaction and downstream commercial outcomes
- full multi-touch customer journey logic

The current approach therefore prioritises transparency and analytical defensibility over false precision.

### Main attribution limitations

The current model cannot fully resolve:

- **Cross-sell value**: a user may buy multiple categories after clicking one ad, and the additional value is not captured in the attributed row
- **Category versus product ambiguity**: some campaigns are tightly product-led, others are broader category or collection campaigns, and a single attribution rule can overstate precision where campaign intent differs
- **Assisted conversion value**: prospecting influence is not fully captured when value appears later through retargeting rather than in the direct prospecting conversion window
- **Cross-device or consent-related loss**: platform tracking may understate true value in some markets and environments

The report should therefore be understood as a **commercially useful attribution approximation**, not a definitive attribution truth.

### What a fuller model would require

A more advanced model would require ecommerce order-line data, structured UTM parameters, session or click identifiers, landing-page context, and in some cases API-based or experiment-based attribution logic.

---

## 8. Product-led versus category-led campaign logic

Not all campaigns should be interpreted in the same way for attribution purposes.

Some campaigns are effectively:

- **single-product campaigns**, where the ad, creative and destination are tightly aligned to one product type

Others are more like:

- **category campaigns**, where the ad promotes a broader category, collection or purchase intent

This matters because the commercial meaning of attributed revenue differs between those cases.

For a strongly product-led ad, assigning attributed value to the mapped category is generally more reasonable.

For a category-led campaign, a fuller model may need to evaluate performance at category level or use order-line logic to allocate value more honestly rather than forcing all value into a narrow product interpretation.

This distinction also has implications for metadata design. A model that forces every campaign into a product-specific classification can overstate precision where the campaign was actually designed to promote a broader category or collection intent.

The current report does not attempt to separate these campaign types algorithmically, but it recognises that a real-world system may need different attribution handling depending on campaign intent and landing page structure.

---

## 9. Tracking adjustment rationale

Selected markets are treated differently where tracked platform revenue may understate true commercial performance under stricter consent conditions.

The report uses a **Tracking Capture Rate** concept for those markets. This represents the proportion of true revenue believed to be captured by the platform.

If capture is assumed to be complete, tracked and adjusted values are identical. If capture is assumed to be lower, adjusted revenue and profit can be interpreted through that assumption.

This adjustment is not presented as a measured fact. It is a modelling layer designed to make the commercial interpretation more realistic in environments where under-tracking is a known structural issue.

The intention is not to claim certainty, but to avoid treating raw platform reporting as complete truth when the reporting context suggests otherwise. The true capture rate in any given market would depend on consent rates, browser and device mix, server-side tracking maturity and reconciliation with other analytics sources.

---

## 10. Metadata, naming and cleaning philosophy

The current report uses a metadata mapping layer because real paid media environments often contain inconsistent, incomplete or historically messy naming.

This is not unusual. In practice, analytics quality often depends not only on raw data availability, but on whether campaign and ad naming has been governed consistently over time.

An important principle behind this project is that naming should capture **business-defining dimensions that are not otherwise reliably available in structured source data**.

If a field already exists cleanly in the export, it does not need to be repeated in naming.

This is particularly relevant for dimensions such as country, placement and delivery surface. In more automated campaign delivery structures, these dimensions may already be available in the data at the relevant reporting grain and are therefore better treated as source-data fields rather than naming components.

This principle matters analytically because it means the naming convention should be designed around what the data cannot already tell you, rather than duplicating what it can.

The long-term goal is not to depend on manual mapping indefinitely. It is to reduce that dependence through better governance and cleaner data design over time.

Practical guidance is documented in `naming_sop.md` and `utm_sop.md`.

---

## 11. Funnel logic and blended evaluation

Prospecting and retargeting do not play the same commercial role.

Prospecting often appears weaker on direct return because it is building future demand. Retargeting often appears stronger because it harvests demand that has already been created.

A common mistake in paid media management is to evaluate prospecting only on direct immediate return and then cut it too aggressively. That reduces the retargeting audience pool over time and can weaken overall portfolio performance even when individual retargeting ROAS appears strong.

For that reason, the report uses a blended evaluation logic for action support while still exposing prospecting and retargeting separately for diagnostic context.

The model is therefore not only asking:

- was this funnel stage profitable in isolation?

It is also asking:

- how commercially useful is this combination when viewed across the full funnel?

### Important limitation

The model does not explicitly connect audience creation in one period with monetisation in a later period. There is typically a lag between prospecting exposure and retargeting conversion that varies by product, market and audience temperature. The current model is therefore stronger as a practical operating tool than as a full causal funnel model. A fuller version would require time-lagged analysis or stronger session-level and order-level linkage.

---

## 12. Future analytical dimensions

Several useful dimensions are recognised as valuable but are not implemented as active reporting dimensions in the current model.

### Landing page
Useful for separating destination-page performance from creative or audience effects. The same creative may perform differently when linked to a product detail page versus a collection page, and without this dimension those effects cannot be cleanly separated.

### Placement and delivery surface
Useful for understanding whether performance differs across surfaces such as Feed, Reels, Stories and Audience Network. In many campaign structures this data already exists in structured export fields and therefore does not necessarily need to be encoded in naming. It can be treated directly as a source-data dimension.

### Creative angle
Useful for comparing broader message themes such as product-led, lifestyle, UGC, testimonial, discount-led or quality-led approaches. This goes beyond creative-format classification to capture what the creative is trying to communicate.

### Hook or headline
Potentially useful for message-level comparison, although this dimension becomes noisy quickly if the reporting grain is too fine or the spend per variant too low. It is better suited to structured metadata at broader aggregation levels than to highly fragmented row-level analysis.

These are intentionally treated as future analytical dimensions rather than current capabilities, because they require stronger metadata discipline, URL extraction, UTM governance or additional enrichment logic to implement reliably.

---

## 13. Main limitations

The current report is designed to be commercially useful, not exhaustively complete.

Its key limitations include:

- platform-level attribution rather than order-line truth
- variable costs only, not full accounting profit
- adjustment logic that reflects a modelling assumption rather than measured validation
- blended funnel interpretation without explicit time-lag linkage between prospecting and retargeting periods
- metadata dependence where naming is historically inconsistent
- no active implementation of landing page, placement, hook or angle as core reporting dimensions

These limitations are not hidden. They are part of the deliberate design boundary of the model and are documented here so that the report is interpreted correctly.

---

## 14. Future direction

A more complete real-world version of this model could be extended through additional data sources and stronger operational discipline.

Potential extensions include:

- ecommerce platform data for order-line attribution, refunds and SKU-level analysis
- GA4 or equivalent analytics for session and landing-page behaviour
- richer campaign and ad-set metadata from platform APIs
- stronger naming and UTM governance to reduce dependence on manual mapping
- CRM data for repeat purchase, cohort and lifetime value analysis
- inventory and supplier data for stock-aware decision-making
- more formal placement, angle and hook diagnostics

The current project should therefore be understood as a strong and commercially honest first analytical layer, not a claim to represent the full maturity of a larger ecommerce intelligence environment.

---

## 15. Final interpretation

This report is best understood as:

**a profit-aware paid social decision-support model built from Meta performance data, metadata classification and variable-cost assumptions, designed to support practical weekly optimisation decisions**

It is strongest when used to answer:

- what appears commercially strong
- what appears weak
- what looks scalable
- what requires caution
- where platform reporting may materially understate value

It is not designed to answer with complete precision:

- full multi-item order attribution
- full cross-channel causality
- full accounting profit
- lifetime customer value
- incrementality

That is not a weakness. It is part of the analytical honesty of the model.

---

## 16. Summary

This model intentionally prioritises:

- analytical transparency
- practical decision usefulness
- profit-aware interpretation
- clear limitation awareness
- realistic extensibility

Its purpose is to bridge the gap between standard paid media reporting and more commercially useful decision-making, showing how paid social data can be turned into a more honest operating view while remaining explicit about where the current data stops and where stronger future integration would be required.