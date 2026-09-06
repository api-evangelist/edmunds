---
name: edmunds-vehicle-expert-review
description: >-
  Retrieve the Edmunds editorial expert review for one vehicle by make, model and year,
  and present it with the attribution Edmunds contractually requires.
api: Edmunds Cars
operations:
  - getExpertReview
generated: '2026-09-06'
method: generated
source: >-
  openapi/_original/edmunds-cars-openapi.yaml (harvested verbatim from
  https://api.edmunds.com/openapi.yaml), conventions/edmunds-conventions.yml,
  errors/edmunds-problem-types.yml
---

# Get an Edmunds expert review

## Before you start

This API is gated. Edmunds retired its open API program effective 2018-02-15 and no
longer issues keys through self-service signup. Without a partner-issued key every call
below returns HTTP 401 with `{"errorType":"AUTHENTICATION_ERROR","message":"Developer
Inactive"}`. If you do not hold a key, stop here and tell the user the surface is closed —
do not retry, and do not treat the 401 as a transient failure.

## Call

`GET https://api.edmunds.com/aiplugin/editorial/v3/makes/{make}/models/{model}/years/{year}/expertcontent`

Operation: `getExpertReview`

| Parameter | In | Required | Notes |
|---|---|---|---|
| `make` | path | yes | Lower case, e.g. `toyota`, `mercedes-benz` |
| `model` | path | yes | Lower case, e.g. `camry`, `amg-gt` |
| `year` | path | yes | 1990 through the current year |

Authentication is an `api_key` query parameter on the main API surface. Note that Edmunds'
own plugin manifest declares a bearer scheme for `/aiplugin/*` while the OpenAPI it points
at declares no security at all — the two first-party documents disagree, so confirm which
your key expects before assuming.

Slugs are not guessable. Where you are unsure of the correct `make` or `model` value, look
up the `niceName` rather than transliterating the display name.

## Read the response

The 200 body carries `vehicleReview` (`summary`, `overallRating`, `pros[]`, `cons[]`), a
`scorecard` with `overall`, `driving`, `utility`, `technology`, `comfort` and `interior`
ratings, `youTubeVideo` and `allYouTubeVideos[]`, `safety`, `featuresSpecs`
(`mostPopularStyleName`, `manufacturersSuggestedRetailPrice`,
`milesPerGallonCityAndHighway`, `seats`, `transmission`, `horsepower`), and
`edmundsBackLink`.

**A 200 does not mean a review exists.** The contract states that when only
`edmundsBackLink` is returned, there is no review for that vehicle. Check for a populated
`vehicleReview` before reporting a review; otherwise say no Edmunds review is published for
that make/model/year and offer the back link.

## Present it correctly

Edmunds attaches binding display obligations to this payload. Reproduce the review as
provided — do not edit, abridge or summarise away the source. Credit it "From Edmunds.com",
keep the published/revised dates, carry the `edmundsBackLink`, and add the
`rel="canonical"` link to the supplied URL if you are rendering it on a web page. Refresh
cached editorial content at least every 30 days. Full rules:
https://developer.edmunds.com/special_requirements.html

## Errors

Errors arrive as `{status, errorType, message, moreInfoUrl}` — not RFC 9457 problem+json.
Read the HTTP status line, not the envelope's `status` field: Edmunds returns HTTP 401 with
`"status":"FORBIDDEN"` in the body.

- 400 — malformed URL syntax. Fix the path; do not retry unchanged.
- 401 — wrong or inactive key. Terminal for this surface today.
- 403 — not authorised for this resource. Terminal.
- 404 — check the path for typos, and check the vehicle is model year 1990 or later.
- 502 / 503 / 504 — transient. Retry with exponential backoff and jitter. No `Retry-After`
  header is published, so choose your own ceiling.

There is no idempotency key and none is needed: this is a GET, safe to retry. There is no
request-id header, so a failed call has no identifier to quote to support.
