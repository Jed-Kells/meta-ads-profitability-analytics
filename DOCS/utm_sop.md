# UTM SOP

## Purpose

This document defines a recommended UTM parameter structure
for paid media links.

Well-structured UTMs make downstream analysis cleaner and
more scalable by ensuring that campaign context can be
recovered consistently outside the ad platform. They support:

- cleaner attribution in GA4 and other analytics tools
- easier campaign and creative classification
- stronger joins between ad platform data and
  ecommerce or session data
- more reliable landing-page analysis
- reduced dependence on manual metadata mapping

---

## Design principles

UTM parameters should:

- be consistent across all paid campaigns and platforms
- carry only the most analytically useful classification fields
- avoid duplicating dimensions already available structurally
  in platform exports
- preserve campaign intent and creative variation clearly
- support destination-page context where possible

UTMs should not try to carry every possible reporting
dimension. If country or placement is already available
in structured export data, it does not need to be
duplicated in utm_campaign. The purpose of UTMs is to
preserve the dimensions most valuable for downstream
attribution and session-level interpretation, not to
become a second naming system overloaded with every field.

---

## Recommended structure

UTM parameters should mirror the campaign naming convention
where possible, carrying category, product, funnel and
objective context at campaign level and creative format
and version at content level.

The core fields used are utm_source for the platform,
utm_medium for the channel type, utm_campaign for
business-defining campaign context, and utm_content
for creative-level variation.

An optional utm_term field can carry additional
distinguishing detail such as audience segment or
test name where needed.

As with naming conventions, not every campaign is
product-specific. Category should remain mandatory
in utm_campaign while product may be null where the
campaign is broader category-led or collection-led.
This preserves commercial intent while remaining
flexible enough for broader campaign structures.

A full UTM convention template is kept private outside 
this public portfolio.

---

## Destination context

UTMs should support not only channel and campaign
classification but also destination-page context
where possible. There are three practical approaches.

### Option 1 — Preserve the destination URL

The simplest approach. Retain the full landing page URL
and classify it later in BigQuery or the analytics layer.

Works well where URL structures are clean enough to
distinguish page types such as product detail pages,
collection pages, homepage or quiz pages.

### Option 2 — Use a landing-page key

A more structured approach where a standardised
destination identifier is maintained alongside the URL.

### Option 3 — Use a reusable link key

Where destination URLs are reused across many ads,
a link key maps each reusable link to a standardised
destination definition. This is often the cleanest
long-term setup for accounts with a stable set of
landing pages.

**Recommended approach for most businesses:**

Use Option 1 as a starting point. Move to Option 2
or 3 when landing-page analysis becomes a priority
and destination URLs are stable enough to classify.

---

## Analytical value of clean UTMs

A structured UTM approach helps unlock future reporting
capabilities including:

- cleaner joins between ad clicks and ecommerce orders
- landing-page type analysis
- category-level versus product-level performance comparisons
- cleaner campaign classification in GA4
- easier proportional or session-linked attribution models

Good UTM discipline is one of the key prerequisites for
moving beyond platform-only reporting. It is the connective
tissue between ad platform data, session analytics and
ecommerce order truth.

---

## Consistency with naming conventions

UTM parameters and campaign naming conventions should
be consistent with each other so that campaign context
is recoverable both within the ad platform and in
downstream analytics and session data.

This consistency allows campaign-level joins between
ad platform data and analytics data without requiring
additional transformation or manual reconciliation.

The recommended approach to naming conventions is
documented in the Naming SOP.

---

## What UTMs do not replace

UTM parameters support session-level and destination-level
analysis but they do not replace ad platform reporting
for performance metrics such as spend, impressions,
CPM and ROAS.

The most complete analytical setup combines:

- ad platform export data for performance metrics
- UTM-enriched analytics data for session and
  conversion behaviour
- ecommerce platform data for order-line truth

---

## Recommended operating approach

The most practical hierarchy for most businesses is:

- use naming conventions for platform governance
- use UTMs for analytics and attribution governance
- preserve destination-page context via URL or link key
- use mapping tables only where historical or messy
  campaigns require them

This creates a stronger analytical foundation than
platform naming alone and reduces long-term dependence
on manual metadata mapping.
