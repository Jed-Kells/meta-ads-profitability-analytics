# Meta Ads Profitability Analytics

A profit-aware paid social analytics system built on BigQuery and
Power BI, designed to support weekly budget allocation decisions
for ecommerce brands running Meta advertising.

Most paid social reporting stops at spend, revenue and ROAS.
This project goes further by incorporating variable costs,
consent-mode tracking adjustment and funnel-aware evaluation
to produce a commercially honest view of paid media performance.

This public repository focuses on the reporting design, analytical
logic and documentation behind the model rather than the full
implementation artefacts.

---

## What it answers

- Are campaigns profitable after variable costs and ad spend?
- Which markets and product categories should be scaled or paused?
- Which creative types and funnel combinations are strongest?
- How should performance in Germany and France be interpreted
  given consent-mode tracking gaps?
- What should be actioned next week?

---

## What makes it different

**Profit-aware rather than revenue-only**
Variable costs including unit cost, packaging, shipping and
payment fees are incorporated into every profitability metric.
A campaign can look strong on ROAS and still be loss-making
once costs are included.

**Tracking adjustment for consent markets**
Germany and France use a user-controlled capture rate assumption
to adjust for the systematic underreporting of conversions in
consent-restricted markets. Adjusted profitability is the
default reporting view.

**Blended funnel evaluation**
Prospecting and retargeting are evaluated on a blended basis
rather than in isolation. Pausing a prospecting campaign solely
on direct ROAS without considering its role in building
retargeting audiences is a commercially costly mistake.
The model is designed to surface that context.

**Action-oriented output**
The report classifies every country, product and creative
combination as Scale, Watch, Pause or Insufficient Data.
It is designed to produce decisions, not just observations.

**Documented assumptions and limitations**
The model is explicit about what it can and cannot measure.
Attribution is simplified. Costs are variable only. Tracking
adjustment is a modelling assumption not a measured fact.
These boundaries are documented clearly rather than hidden.

---

## Stack

- Google BigQuery — data pipeline and transformation
- Power BI Desktop — semantic model and reporting
- Meta Ads export data — primary performance source
- Helper dimensions for ad metadata, product costs,
  shipping costs and payment fees

---

## Architecture

Meta CSV exports → BigQuery (raw → staging → fact) → Power BI Semantic Model → Report

---

## Report pages

| Page | Purpose |
|---|---|
| Executive Summary | Profitability and trend overview |
| Market & Product Drivers | Country and category performance |
| Creative & Funnel Analysis | Creative type and funnel stage |
| Geographic & Tracking | Market performance and attribution |
| Action Framework | Classification and budget decisions |

---

## Repository contents

| Path | Contents |
|---|---|
| `docs/methodology.md` | Analytical approach, assumptions and limitations |
| `docs/model_notes.md` | Data model structure and design decisions |
| `docs/commercial_logic.md` | Metric definitions and action framework logic |
| `docs/data_dictionary.md` | Field definitions and notes |
| `docs/naming_sop.md` | Recommended campaign naming convention |
| `docs/utm_sop.md` | Recommended UTM structure |
| `assets/pipeline-diagram.png` | BigQuery pipeline overview |
| `assets/semantic-model.png` | Power BI semantic model |
| `assets/screenshots/` | Report page screenshots |

---

## Key assumptions

- Adjusted metrics are the default reporting view
- Germany and France use a tracking capture rate adjustment
- Net Profit includes ad spend and selected variable costs only
- Attribution is ad-linked category level, not order-line truth
- Metadata classification uses a mapping table where naming
  is inconsistent

---

## Licence

MIT Licence — see LICENCE file for details.
