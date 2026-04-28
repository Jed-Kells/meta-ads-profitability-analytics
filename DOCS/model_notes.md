# Model Notes

## Purpose

This document explains how the data model is structured,
what grain the main tables operate at, and why the current
model uses the relationships and dimensions that it does.

This is a public summary of the model design rather than
a full implementation specification.

---

## Model overview

The reporting model is built around a daily Meta performance
fact table supported by helper dimensions for metadata and
variable costs.

At a high level, the model combines:

- daily ad performance
- ad-level metadata classification
- product-category-level cost assumptions
- country-level payment fee assumptions
- country-and-category-level shipping logic
- a standard date dimension for time intelligence

---

## Core fact table

### `fact_meta_ads_daily`

**Grain:** one row per ad per day per country

This is the main reporting fact table used in Power BI.
It contains the cleaned daily Meta Ads performance data
required for profitability analysis.

Typical fields include:

- `date`
- `country`
- `campaign_name`
- `adset_name`
- `ad_name`
- `spend_gbp`
- `impressions`
- `unique_link_clicks`
- `website_purchases`
- `revenue_gbp`
- `cpm`
- `ctr`
- `ship_key`

---

## Supporting dimensions

### `dim_ad`

**Grain:** one row per `ad_name`

Used to classify ads into structured reporting dimensions
such as:

- `product_category`
- `funnel_stage`
- `creative_type`
- `campaign_key`
- `sloppy_name`

This table exists because source ad naming is not always
reliable enough for direct, consistent classification.

### `dim_product`

**Grain:** one row per `product_category`

Used to attach variable cost assumptions such as:

- unit cost
- packaging cost

The current model is intentionally at product-category
level rather than SKU level.

### `dim_shipping_cost`

**Grain:** one row per `country x product_category`

Used to apply estimated shipping cost assumptions by
market and category.

### `dim_fee_structure`

**Grain:** one row per country

Used to apply payment fee assumptions by market.

### `Date`

**Grain:** one row per calendar date

Used for time intelligence and standard filtering
across the report.

---

## Key joins

The main relationships are:

- `fact_meta_ads_daily[ad_name]` → `dim_ad[ad_name]`
- `dim_ad[product_category]` → `dim_product[product_category]`
- `fact_meta_ads_daily[country]` → `dim_fee_structure[country]`
- `fact_meta_ads_daily[ship_key]` → `dim_shipping_cost[ship_key]`
- `Date[Date]` → `fact_meta_ads_daily[date]`

---

## Design decisions

### Why the model is at category level rather than SKU level

The source Meta export does not provide order-line detail
or SKU-level attribution. Category-level modelling is a
practical and defensible midpoint between overly simplistic
platform reporting and a fully integrated ecommerce model —
it reflects the actual resolution of the available data
rather than implying a precision the source cannot support.

A production version with ecommerce platform integration
could extend this to SKU or variant level.

### Why dim_product connects via dim_ad rather than directly to the fact table

Product details in this model only vary at category level.
Routing through dim_ad reflects the analytical reality that
product classification is a property of the ad metadata,
not a direct property of each performance row. A direct
fact-to-dim_product join would imply SKU-level resolution
that the data does not support.

### Why dim_ad exists as a separate mapping layer

Ad naming in real-world paid media environments is often
inconsistent. Rather than treating naming quality as
reliable, this project uses a metadata mapping layer to
support stable analysis across product category, funnel
stage and creative type regardless of naming discipline
in the source account.

### Why ship_key is built upstream rather than in Power BI

Shipping cost depends on both country and product category
simultaneously. Power BI does not support composite key
relationships natively, so a shipping key is created
upstream in the warehouse layer so that the semantic model
consumes join-ready tables rather than building composite
relationship logic inside the report. This keeps
transformation logic where it belongs and allows the
report layer to remain focused on reporting.

### Why the pipeline is structured for safe daily reruns

The daily refresh pattern is designed so that any given
date can be rerun safely without creating duplicate rows
or requiring a full historical rebuild. This makes the
pipeline more resilient to failures and easier to
maintain operationally.

### Why the fact table is optimised for date-range queries

The fact table is structured to minimise query cost for
the filter patterns used most frequently across the report.
Date-based filtering and country or campaign-level
aggregation are the primary access patterns this
optimisation supports.

---

## Semantic model design principles

The semantic model is designed to keep transformation
logic upstream where practical and reporting logic
downstream where useful. In practice this means:

- helper keys are created in the warehouse layer
  where they simplify joins
- cost assumptions are applied through supporting
  dimensions rather than hard-coded report logic
- measures are centralised in Power BI to keep the
  field list cleaner and reporting logic easier to
  maintain and audit

This is intended as a clean, maintainable reporting
model rather than a full enterprise semantic layer.

---

## Default reporting view

Adjusted profitability is the default reporting view
used across the report. Tracked values are retained
on the geographic and attribution page for direct
comparison.

---

## Current scope and known limitations

The model is intentionally scoped to what can be
supported cleanly from the available data sources.
It does not currently include order-line ecommerce
data, session-level analytics, audience-level
enrichment or full multi-touch attribution. These
are documented in the methodology as future
extensions rather than gaps in the current
implementation.