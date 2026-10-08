# Product specification sources

Reference for the n8n product-specification enrichment workflow: which sources may be used, how far each is trusted, and what was actually reachable in testing.

- Machine-readable version: [`sources.json`](sources.json)
- Access test date: **2026-10-08**, plain HTTP request (no browser, no login), the same way the workflow fetches pages.
- Constraint: **no source that requires registration, an account or an API key.** Icecat and the EU EPREL API are therefore excluded.

## Trust tiers

| Tier | Meaning | Rule in the workflow |
|---|---|---|
| **1 — Official** | The brand's own product, support and spec pages, and its PDF datasheets | Can fill any field on its own (after the quote check and independent review) |
| **1b — Certification / regulatory** | Databases run by regulators or industry bodies, with data submitted by the brands | Authoritative **only** for the fields that body certifies (e.g. Wi-Fi Alliance → Wi-Fi standard) |
| **2 — Identity** | Open datasets used to resolve the exact product/variant, not to supply values | Never fills a cell; strengthens the "is this the exact variant?" check |
| **3 — Secondary / confirmation** | Encyclopedias, spec aggregators, review and measurement sites | Never the only source for a value, never produces a "No", always loses to Tier 1/1b |

A value is only written when its exact quote appears in a page the workflow fetched itself **and** an independent reviewer agrees. Everything else is written as `-`.

## Sources and access status

### Usable now (no login, reachable)

| Source | Tier | Licence / access | Good for |
|---|---|---|---|
| Brand websites, support pages, PDF datasheets | 1 | Public | Everything |
| Wi-Fi Alliance product finder — wi-fi.org | 1b | Public | Wi-Fi generation, bands, WPA3 |
| Bluetooth SIG qualified listings | 1b | Public *(search URL has moved — needs the current address)* | Bluetooth version, profiles |
| USB-IF product listings — usb.org | 1b | Public | USB-C / Power Delivery certification |
| Wikidata (SPARQL) | 2 | CC0, open API | Brand, model family, release year |
| Google Play "supported devices" list | 2 | Public file from Google | Android model code ↔ marketing name (phones, tablets, watches, Android TVs) |
| Open Products Facts | 2 | ODbL, open API | Barcode → product name and brand (very few specs for electronics) |
| Wikipedia (API) | 3 | CC BY-SA, open API | Spec infoboxes for phones, tablets, smartwatches, major laptops/cameras |
| GSMArena | 3 | Public, rate-limits heavy use | Phones, tablets, smartwatches |
| PhoneDB | 3 | Public, not open-licensed | Phones, tablets |
| DisplaySpecifications | 3 | Public | TVs, monitors |
| RTINGS | 3 | Public, some content paid | TVs, monitors, headphones, speakers |
| DPReview | 3 | Public | Cameras |
| Lenovo PSREF | 1 | Public, JavaScript app (may need its JSON endpoint) | Lenovo laptops and tablets |

### Excluded — registration required

| Source | Result | Reason |
|---|---|---|
| Icecat | Data API returned **401** | Needs an Open Icecat account |
| EU EPREL | Website public, data API returned **403** | Needs a requested API key |

### Excluded — blocks automated access

| Source | Result |
|---|---|
| CSA (Matter / Zigbee certified products) | Cloudflare bot challenge |
| Intel ARK | 403 |
| Notebookcheck | 403 / bot challenge |
| VESA DisplayHDR certified list | Bot challenge |
| FCC ID (fccid.io) | 403 |

The workflow never bypasses bot challenges or CAPTCHAs.

### Not yet confirmed

| Source | Note |
|---|---|
| Qi / Wireless Power Consortium product database | Public, but the URL tried returned 404 — current address needed |
| Apple MFi accessory search | Public, but the URL tried returned 404 — current address needed |

## Sources per product category

| Category | Tier 1 / 1b | Tier 2 (identity) | Tier 3 (confirmation) |
|---|---|---|---|
| Phones | Brand, Wi-Fi Alliance, Bluetooth SIG | Google Play list, Wikidata | Wikipedia, GSMArena, PhoneDB |
| Tablets | Brand, Wi-Fi Alliance, Bluetooth SIG | Google Play list, Wikidata | Wikipedia, GSMArena, PhoneDB |
| Laptops | Brand spec sheets (PSREF, HP, Dell…), Wi-Fi Alliance | Wikidata | Wikipedia |
| Monitors | Brand | Wikidata | DisplaySpecifications, RTINGS |
| TVs | Brand | Google Play list (Android TVs), Wikidata | DisplaySpecifications, RTINGS |
| Smartwatches | Brand, Bluetooth SIG | Google Play list, Wikidata | Wikipedia, GSMArena |
| Headphones | Brand, Bluetooth SIG | Wikidata | RTINGS |
| Speakers / other audio | Brand, Bluetooth SIG | Wikidata | RTINGS |
| Cameras | Brand | Wikidata | DPReview, Wikipedia |
| Routers / other network devices | Brand datasheets, Wi-Fi Alliance | Wikidata | — |
| Robot vacuums | Brand | — | — |
| Smart home devices | Brand, Wi-Fi Alliance, Bluetooth SIG | — | — |
| Phone accessories | Brand, USB-IF (Qi / MFi once URLs confirmed) | Open Products Facts (if EAN) | — |
| General accessories / other | Brand | Open Products Facts (if EAN) | — |

## Expected fill rate (estimates, not measured)

Share of template cells filled with a checked value, assuming redirects are followed and PDF datasheets are read. Without Icecat and EPREL:

| Category | Expected fill |
|---|---|
| Phones, tablets | 70–85% |
| Smartwatches | 55–70% |
| Laptops, cameras | 60–80% |
| TVs, monitors | 60–75% |
| Headphones, speakers, other audio | 45–65% |
| Routers, network devices | 50–70% |
| Robot vacuums, smart home | 35–55% |
| Accessories, other | 30–50% |

These are educated guesses. A real run on about 20 products across the categories is needed to replace them with measurements.
