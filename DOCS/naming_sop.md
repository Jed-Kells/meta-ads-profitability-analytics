# Naming SOP

## Purpose

This document defines a recommended naming standard for 
Meta campaigns, ad sets and ads.

The goal is not to encode every possible reporting field 
into a name. The goal is to ensure that naming captures 
the business-defining dimensions that are not otherwise 
reliably available in structured source data.

Poor naming creates analytical problems that are expensive 
to fix retrospectively — it increases dependence on manual 
mapping tables, reduces consistency over time, and makes 
it harder to scale reporting as accounts grow.

---

## Design principles

Naming conventions should be:

- short enough that teams will actually use them consistently
- focused on dimensions not already cleanly available 
  from the platform export
- flexible enough to handle both product-led and 
  category-led campaigns
- structured enough to support extraction and classification 
  at scale

An important principle: naming should not duplicate dimensions 
already available as structured fields in the platform export. 
For example, if country and placement are already present 
in source data at a usable grain, they do not need to be 
forced into campaign or ad names as well.

This is particularly relevant in modern Meta structures 
such as Advantage+, where delivery surface, placement and 
audience breakdown may already be represented in exported 
performance data.

---

## What naming should capture

Naming conventions are most valuable for dimensions such as:

- product category or commercial focus
- specific product where applicable
- funnel role
- campaign objective

These tend to be business-defined and are not always 
recoverable reliably from raw platform fields alone.

---

## What naming does not need to capture

These dimensions may already exist as structured platform 
fields and generally do not need to be forced into naming:

- country or market
- placement
- delivery surface
- most campaign delivery settings

This is particularly relevant in structures such as Advantage+, where mixed placements and delivery surfaces may already be represented in the exported performance data.


---

## Recommended structure

Campaign names should encode the business-defining dimensions 
that are not otherwise recoverable from platform source data — 
typically category, product focus where applicable, funnel role 
and campaign objective.

Ad set names should encode audience and targeting logic at the 
level that supports consistent analysis.

Ad names should encode creative format, message angle and 
version to support creative performance analysis over time.

A fuller operational version is kept private outside this public portfolio. 

---

## Handling legacy and inconsistent naming

Where historical campaigns do not follow the convention, 
a metadata mapping table should be maintained to bridge 
the gap.

The recommended operating sequence is:

1. Implement the naming SOP for all new campaigns immediately
2. Maintain a mapping table for legacy campaigns
3. Phase out legacy campaigns over time
4. Reduce dependence on manual mapping as clean naming 
   becomes the standard

---

## Relationship to UTM governance

Naming conventions and UTM parameters serve complementary 
but distinct purposes.

Naming conventions govern how campaigns are identified 
and classified within the ad platform.

UTM parameters govern how campaign and creative context 
is carried through to web analytics and downstream data 
systems.

Both should be consistent with each other where possible. 
The recommended approach is documented in the UTM SOP.
