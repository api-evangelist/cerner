---
name: cerner-patient-clinical-summary
description: >-
  Assemble a clinical summary for one patient from the Oracle Health Millennium Platform FHIR R4 API —
  locate the patient, then pull problems, allergies, medications, results and encounters, with the
  provenance of each. Handles Millennium's required-parameter rule, its opaque paging, and the
  data-absent semantics that make "field missing" ambiguous.
api: openapi/cerner-millennium-fhir-r4-openapi.yml
operations:
  - searchPatient
  - readPatient
  - searchCondition
  - searchAllergyIntolerance
  - searchMedicationRequest
  - searchObservation
  - searchEncounter
  - searchProvenance
generated: '2026-08-14'
method: generated
source: openapi/cerner-millennium-fhir-r4-openapi.yml (derived from the provider CapabilityStatement)
---

# Build a patient clinical summary on Oracle Health Millennium

## Before you call anything

**Resolve the tenant.** The tenant id is a path segment of the service root, not a header:
`https://fhir-ehr.cerner.com/r4/{tenant}/`. Nothing works until you have it, and a wrong tenant
returns `403 Tenant not valid or accessible` — not 404. The public sandbox tenant is
`ec2458f2-1e24-41c8-b71b-0e701af7583d`.

**Pick the right host.**
- `fhir-open.cerner.com/r4/{tenant}` — read-only, **no token**. Use it to develop and to verify shapes.
- `fhir-ehr.cerner.com/r4/{tenant}` — production, provider persona, SMART OAuth 2.0 required.
- `fhir-myrecord.cerner.com/r4/{tenant}` — patient persona.

**Get a token.** Read `/.well-known/smart-configuration` at the tenant service root for that tenant's
authorize/token endpoints — they are tenant-scoped, so never hard-code them. Request the narrowest scopes
that cover the reads below (see `scopes/cerner-scopes.yml`; the sandbox tenant advertises 303).

## Step 1 — locate the patient (`searchPatient`)

`GET /Patient?identifier=...` or `?name=...`. **You cannot list patients unqualified.** Millennium
requires a qualifying parameter and answers `400 No supported search parameters provided` without one.
Prefer `identifier` (an MRN in the health system's identifier system) over `name` — name search returns
near-duplicates, and the sandbox proves it: `?name=smart` returns several distinct patient ids with
almost identical names.

Confirm with `readPatient` on the id you selected before you use it for anything clinical.

## Step 2 — pull the clinical resources

Every one of these requires `patient` (or `_id`). Fan them out in parallel:

| What | Operation | Call |
|---|---|---|
| Problems | `searchCondition` | `GET /Condition?patient={id}` |
| Allergies | `searchAllergyIntolerance` | `GET /AllergyIntolerance?patient={id}` |
| Medications | `searchMedicationRequest` | `GET /MedicationRequest?patient={id}` |
| Results / vitals | `searchObservation` | `GET /Observation?patient={id}&category=...` |
| Encounters | `searchEncounter` | `GET /Encounter?patient={id}` |

Read each resource's `searchParam` documentation in the CapabilityStatement before adding filters — the
legal combinations are stated there, and date parameters must form a **closed** range (`ge`/`le` pairs),
not an open-ended one.

## Step 3 — page correctly

Set `_count`, then **follow `Bundle.link[relation=next]` verbatim**. Do not build your own page URLs; the
cursor is an opaque `pageContext` UUID.

**The paging hazard is documented and it matters clinically:** the same resource id can appear on more
than one page while the underlying record changes mid-traversal. De-duplicate by resource id and keep only
the latest version. An agent that concatenates pages will double-count medications and problems.

## Step 4 — attach provenance

19 resources declare `_revinclude=Provenance:target`. Add it to get who recorded each fact alongside the
fact itself. For a summary that a clinician might act on, this is not optional garnish — it is the only
way to say where a statement came from.

## Reading absence correctly — the safety rule

A missing element on this API means one of three different things, and only the `DataAbsentReason`
extension distinguishes them:

- `valueCode: "unknown"` — the field is not recorded in Millennium.
- `valueCode: "masked"` — the resource is `entered-in-error` and the consumer is a patient; it is being
  withheld.
- genuinely absent — the server had nothing to say.

**Never render a missing field as a clinical negative.** "No allergies returned" is not "no known
allergies"; the latter is an explicit `AllergyIntolerance` with a no-known-allergies code.

## Errors you will actually hit

| Status | Meaning | Do |
|---|---|---|
| 400 | required search parameter missing | add `patient` or `_id` |
| 401 | no valid token on a secure host | re-authorize; or use the open host |
| 403 | tenant not valid or accessible | wrong tenant, or app not provisioned for it |
| 406 | asked for non-JSON | send `Accept: application/fhir+json`; the body is empty, which reads like "no data" |
| 500 | a FHIR-required field is missing from the record | a data-quality problem at the health system — retrying will not fix it |

Errors are FHIR `OperationOutcome`, not RFC 9457 problem+json. See `errors/cerner-problem-types.yml`.

## What this API will not give you

No rate-limit headers, no request-id header, no idempotency key, no webhooks. Back off conservatively on
`429`/`503` and expect no runtime budget signal.
