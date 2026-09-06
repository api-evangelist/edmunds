---
name: edmunds-find-local-inventory
description: >-
  Search new, used and certified pre-owned vehicles listed near a US zip code through the
  Edmunds curated inventory operation, and report results with the price framing Edmunds
  requires.
api: Edmunds Cars
operations:
  - findInventory
generated: '2026-09-06'
method: generated
source: >-
  openapi/_original/edmunds-cars-openapi.yaml (harvested verbatim from
  https://api.edmunds.com/openapi.yaml), conventions/edmunds-conventions.yml,
  errors/edmunds-problem-types.yml
---

# Find local vehicle inventory

## Before you start

Gated surface. Without a partner-issued Edmunds key this returns HTTP 401 "Developer
Inactive". Edmunds stopped issuing keys through the open program on 2018-02-15 and states
it does not expect to accept new applicants. If you have no key, say so rather than
retrying.

## Call

`GET https://api.edmunds.com/aiplugin/curated-inventory/v1`

Operation: `findInventory`

Required parameters:

| Parameter | In | Notes |
|---|---|---|
| `zip` | query | The US zip code to search in |
| `pagenum` | query | Page number. The contract says default this to `1` |
| `pagesize` | query | Items per page. The contract says default this to `3` |

The most useful optional facets: `radius` (miles), `inventoryType` (`NEW`, `USED`, `CPO`),
`make`, `model`, `year`, `mileage`, `bodyType`, `engineType`, `driveTrain`, `transmission`,
`exteriorColor`, `interiorColor`, `totalSeating`, `displayPrice`, `paymentType`
(`loan`/`lease`), `loanPayment`, `leasePayment`, `dealType` (`Great`, `Good`, `Fair`),
`historyInfo`, `vin`, `stockNumber`, and the feature groups `comfortConvenience`, `safety`,
`engineExterior`, `entertainment`. Sort with `sortby={field}:{direction}`.

Most facets take a comma-separated list. Numeric facets (`year`, `mileage`, `displayPrice`,
`loanPayment`, `leasePayment`, `combinedMPG`, `electricityRange`) also accept a range.
Several — the colour, engine-type and drivetrain lists — are documented as **case
sensitive**; use the exact published values or you will silently filter everything out.
The full parameter list is in `openapi/_original/edmunds-cars-openapi.yaml`.

## Read and report the response

The body carries `inventories` with `totalNumber` and a `results` array. Each result has
`vin`, `sellersComments` (truncated by Edmunds), `inTransit`, `dealerInfo.name`, and
`computedDisplayInfo.prices` with `msrp`, `price`, `dealerPrice`, `edmundsSuggestedPrice`
and `loanPaymentEstimate`.

Three things the operation's own description requires of you:

1. **Always state `totalNumber`.** This is a curated sample, not an exhaustive search —
   say how many matches exist, not just how many you are showing.
2. **Refer to `edmundsSuggestedPrice` as "Edmunds Suggested Price"**, verbatim. It is a
   distinct figure from `price`, `dealerPrice` and `msrp`; do not conflate them or present
   one as "the price" without saying which.
3. **Link each result back to Edmunds** in the form
   `https://edmunds.com/{make}/{model}/{year}/vin/{vin}/`.

Prices are perishable. If you cache or re-present them, show when they were retrieved.

## Errors

Same envelope as the rest of the API — `{status, errorType, message, moreInfoUrl}`, not
problem+json — and the same seven statuses. Read the HTTP status line, not the body's
`status` field. 401 and 403 are terminal; 502/503/504 are transient and safe to retry with
backoff, because this is a GET. No rate limits, no `Retry-After` and no request-id header
are published, so pick your own backoff ceiling and expect no correlation id to quote to
support.
