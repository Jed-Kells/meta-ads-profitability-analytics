# Commercial Logic

## Purpose

This document explains the business reasoning behind 
the report's metrics, thresholds and action framework.

It is intended for anyone who wants to understand not 
just what the report shows but why it is structured 
the way it is.

---

## Why this model is profit-aware

Standard paid media reporting typically focuses on:

- Spend
- Revenue
- ROAS
- CTR
- CPM

These metrics are useful for media buying decisions 
but insufficient for commercial decisions.

A campaign can show strong ROAS while still being 
loss-making once product cost, packaging, shipping 
and payment fees are included. A business optimising 
toward revenue ROAS without accounting for variable 
costs may be scaling campaigns that are actively 
destroying margin.

This report is built to prevent that error by making 
profitability the primary evaluation metric rather 
than revenue.

---

## Variable costs included

| Cost type | Source |
|---|---|
| Ad spend | Meta export |
| Unit cost of goods | dim_product |
| Packaging cost | dim_product |
| Shipping cost | dim_shipping_cost |
| Payment processing fees | dim_fee_structure |

---

## Variable costs excluded

| Cost type | Reason for exclusion |
|---|---|
| Staff and salaries | Fixed overhead, not variable per sale |
| Rent and premises | Fixed overhead |
| Software subscriptions | Fixed overhead |
| Returns and refunds | Not captured in Meta export data |
| VAT | Varies by registration and market structure |
| Import duties | Requires separate product and shipping data |
| Agency fees | Not universally applicable |

The report should therefore be interpreted as a 
variable-cost contribution model for paid media 
decision-making, not a full management accounting view.

---

## Net Profit definition in this report

Net Profit in this report means:

**Revenue minus ad spend and selected variable costs**

It does not represent full accounting net profit.

---

## Net Profit ROAS

Net Profit ROAS is the primary commercial metric 
in this report.

**Formula:** Net Profit divided by Spend

**Break-even point:** 0

This is different from standard revenue-based ROAS 
where break-even is typically 1. Net Profit ROAS 
measures profit generated per pound of ad spend. 
A value of 0 means the campaign covers all variable 
costs and ad spend exactly. A value above 0 means 
the campaign is generating net profit after all 
included costs.

---

## Action classification thresholds

The report classifies combinations using the 
following logic:

| Classification | Condition |
|---|---|
| Insufficient Data | Spend below minimum threshold |
| Pause | Net Profit ROAS below 0 |
| Watch | Net Profit ROAS between 0 and 0.3 |
| Scale | Net Profit ROAS above 0.3 |

These thresholds are illustrative defaults. 
In a production deployment they would be calibrated 
to the specific margin profile and risk tolerance 
of the business.

---

## Why minimum spend thresholds matter

Combinations with very low spend can show misleadingly
extreme ROAS values — either very high or very negative —
without sufficient evidence to support action.

A minimum spend threshold is applied to filter out
low-signal rows from the action framework. Combinations
below the threshold are classified as Insufficient Data
rather than Scale, Watch or Pause.

These thresholds should not be treated as universal
truths. At very granular levels — such as placement,
sub-creative breakdown, hook or headline — a low-spend
row may still sit inside a broader structure with
meaningful total spend. Threshold logic therefore
depends partly on reporting grain.

---

## Why adjusted metrics are the default view

Germany and France operate under stricter consent 
frameworks than other markets. Users who decline 
cookie consent are not tracked by Meta's pixel, 
resulting in systematic underreporting of conversions.

Adjusted metrics divide tracked revenue by a 
Tracking Capture Rate assumption to produce a 
more commercially representative view of true 
performance in those markets.

The default capture rate is 1.0 — meaning no 
adjustment — which can be modified in the report 
to model different attribution assumptions.

Adjusted profitability is used as the default 
commercial view. Tracked values are retained on 
the geographic page for comparison.

---

## Funnel logic and blended evaluation

### Why direct ROAS by funnel stage is not enough

Prospecting and retargeting serve different 
commercial roles.

Prospecting targets cold audiences and typically 
generates lower direct conversion return because 
it is building future demand rather than harvesting 
existing demand.

Retargeting typically generates stronger direct 
return because it reaches audiences already 
warmed by prior brand exposure.

Evaluating prospecting on direct ROAS alone and 
pausing underperforming campaigns is a common 
and commercially costly mistake. It reduces the 
retargeting audience pool over time, eventually 
reducing overall portfolio performance.

### How the report handles this

The Action Framework uses blended profitability 
logic — evaluating combinations across both 
funnel stages combined — while still exposing 
prospecting and retargeting performance separately 
for diagnostic context.

The report is therefore asking:

**How commercially useful is this combination 
across the full funnel?**

Rather than:

**Was prospecting directly profitable in isolation?**

### Practical implication

A combination showing weak prospecting ROAS but 
strong retargeting ROAS should not be paused 
on prospecting performance alone. The blended 
view should be the primary decision input, with 
funnel-stage detail used for diagnostic investigation 
rather than immediate action.

---

## Action logic summary

In practice, action logic is intended to support budget 
reallocation towards combinations with stronger blended 
profitability, while filtering out low-signal combinations 
that do not yet justify action.

The thresholds and classification logic are summarised 
in the Action classification thresholds section above.

---

## What this report does not measure

The following cannot be reliably measured from
the current data sources and are therefore
outside the scope of this model:

- True incrementality — whether sales would
  have occurred without the ads
- Cross-sell value — revenue from non-advertised
  products in the same order
- Customer lifetime value — repeat purchase
  behaviour beyond the initial conversion
- Full multi-touch attribution — credit across
  multiple touchpoints in the customer journey
- Fixed cost recovery — the report does not
  account for overhead allocation

These boundaries are not limitations of the
analytical approach. They are limitations of
the available data source. A more complete model
would require integration with ecommerce platform
data, session analytics and CRM data.

---

## What this logic is trying to achieve

The commercial aim of the model is not just to
describe performance. It is to support better
weekly decisions by answering questions such as:

- what appears commercially strong
- what appears weak
- what deserves scaling
- what deserves caution
- where platform reporting may be materially
  understating value

That is the core distinction between this model
and a standard channel dashboard.