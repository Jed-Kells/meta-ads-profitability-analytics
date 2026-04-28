# Data Dictionary

## Purpose

This document defines the key fields and measures used 
in the Meta Ads profitability model.

It is intended to support consistent interpretation of 
report values and to clarify where fields originate, 
what they measure, and any important caveats.

---

## Source fields

These fields originate from the Meta Ads export and 
are loaded through the BigQuery pipeline.

| Field | Table | Definition | Caveats |
|---|---|---|---|
| `date` | `fact_meta_ads_daily` | Reporting date for the performance row | One row per ad per day per country |
| `country` | `fact_meta_ads_daily` | Market associated with the row | Harmonised to consistent labels across all tables |
| `campaign_name` | `fact_meta_ads_daily` | Raw campaign label from source export | Used for reference and grouping, not primary classification |
| `adset_name` | `fact_meta_ads_daily` | Raw ad set label from source export | Used for hierarchy context |
| `ad_name` | `fact_meta_ads_daily` | Ad identifier used to join to metadata | Primary metadata join field |
| `spend_gbp` | `fact_meta_ads_daily` | Ad spend for the row in GBP | Base cost input for all profitability metrics |
| `impressions` | `fact_meta_ads_daily` | Number of impressions served | Used for CPM and reach context |
| `unique_link_clicks` | `fact_meta_ads_daily` | Unique link clicks reported by Meta | Used in CTR and CVR logic |
| `website_purchases` | `fact_meta_ads_daily` | Purchases attributed by Meta | Platform attribution only, not order-line truth |
| `revenue_gbp` | `fact_meta_ads_daily` | Conversion value attributed by Meta | Tracked revenue, not verified order-line revenue |
| `cpm` | `fact_meta_ads_daily` | Cost per thousand impressions | Platform diagnostic metric |
| `ctr` | `fact_meta_ads_daily` | Click-through rate | Platform diagnostic metric |
| `ship_key` | `fact_meta_ads_daily` | Composite join key for shipping cost | Built upstream in BigQuery from country and product category |

---

## Dimension fields

These fields originate from helper dimension tables 
and are joined to the fact table in the semantic model.

| Field | Table | Definition | Caveats |
|---|---|---|---|
| `product_category` | `dim_ad` / `dim_product` | Product or category classification assigned to the ad | Classification via metadata mapping where naming is inconsistent |
| `creative_type` | `dim_ad` | Creative format classification | Values include IMAGE, VIDEO, CAROUSEL |
| `funnel_stage` | `dim_ad` | Funnel role classification | Values include PROS, RET |
| `sloppy_name` | `dim_ad` | Flag indicating inconsistent or unreliable ad naming | Used as a governance signal in creative analysis |
| `campaign_key` | `dim_ad` | Supporting metadata field for campaign grouping | Not used as a primary join key |
| `unit_cost_gbp` | `dim_product` | Estimated unit cost by product category | Variable cost input |
| `packaging_cost_gbp` | `dim_product` | Estimated packaging cost by product category | Variable cost input |
| `shipping_cost_gbp` | `dim_shipping_cost` | Estimated shipping cost by country and category | Variable cost input, joined via ship_key |
| `payment_fee_percent` | `dim_fee_structure` | Payment processing fee rate by country | Applied to revenue in fee calculations |

---

## Measures

These are calculated metrics defined in the Power BI
semantic model. Formulas are not published here but
the commercial definition of each measure is documented
below.

---

### Performance measures

| Measure | Definition |
|---|---|
| Spend | Total ad spend in GBP |
| Revenue (Tracked) | Platform-reported conversion value before adjustment |
| Revenue (Adjusted) | Conversion value adjusted for tracking capture in DE and FR |
| Purchases | Total attributed website purchases |
| Impressions | Total impressions served |
| Clicks | Total unique link clicks |
| AOV | Average order value — Revenue divided by Purchases |
| CTR | Click-through rate — Clicks divided by Impressions |
| CPC | Cost per click — Spend divided by Clicks |
| CPM | Cost per thousand impressions |
| CVR | Click-to-purchase rate — Purchases divided by Clicks |

---

### Profitability measures

| Measure | Definition |
|---|---|
| Net Profit (Tracked) | Tracked revenue minus spend and variable costs |
| Net Profit (Adjusted) | Adjusted revenue minus spend and variable costs |
| Net Profit ROAS | Net Profit (Adjusted) divided by Spend — break-even is 0 |
| Break-even ROAS | The ROAS level at which ad spend is exactly covered |
| Tracking Capture Rate (Selected) | User-selected capture rate assumption for DE and FR |

---

### Action classification

| Measure | Definition |
|---|---|
| Performance Classification | Scale, Watch, Pause or Insufficient Data based on Net Profit ROAS and minimum spend threshold |

---

### Internal reporting measures

The semantic model includes additional internal measures
to support:

- variable cost rollups by category and market
- period-on-period trend comparisons
- funnel-specific diagnostics
- blended action logic across prospecting and retargeting
- action classification counts

These are described conceptually in the methodology and
commercial logic documentation. The full set of internal
measures is not enumerated in this public reference.

---

## Important notes on interpretation

**Net Profit ROAS break-even is 0, not 1**
This metric measures profit relative to spend, not
revenue relative to spend. A value of 0 means the
campaign covers all variable costs and ad spend exactly.
A value above 0 means the campaign is generating net profit.

**Adjusted metrics are the default view**
Revenue (Adjusted) and Net Profit (Adjusted) are used
as the primary reporting view throughout the report.
Tracked equivalents are shown on the geographic page
for comparison.

**Attribution is platform-level**
Revenue and purchase figures reflect Meta's attributed
values. They do not represent verified order-line truth
and should be interpreted in the context of the
attribution assumptions documented in the methodology.

**Variable costs only**
All cost measures reflect variable costs only. Fixed
overheads, returns, VAT and duties are excluded.
Net Profit in this report is a contribution-style
measure, not full accounting net profit.

**Scope of active dimensions**
Some dimensions discussed conceptually in the
documentation — such as landing page, placement,
angle and hook — are not implemented as active
reporting dimensions in the current model. They are
documented as analytical extensions rather than
current capabilities.