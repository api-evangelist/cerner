---
name: cerner-write-back-clinical-resource
description: >-
  Write clinical data back into Oracle Health Millennium safely — which resources actually accept writes,
  how to update without losing someone else's edit, and how to survive the fact that this API has no
  idempotency primitive and will happily create duplicates on a retry.
api: openapi/cerner-millennium-fhir-r4-openapi.yml
operations:
  - createCondition
  - updateCondition
  - createAllergyIntolerance
  - updateAllergyIntolerance
  - createObservation
  - updateObservation
  - createDocumentReference
  - updateDocumentReference
  - patchPatient
  - readCondition
generated: '2026-08-14'
method: generated
source: >-
  openapi/cerner-millennium-fhir-r4-openapi.yml (derived from the provider CapabilityStatement),
  conventions/cerner-conventions.yml, errors/cerner-problem-types.yml
---

# Write back to Millennium without creating duplicates

Writing into someone's EHR is the highest-consequence thing on this API. Read this whole skill before the
first `POST`.

## Step 0 — check the resource actually accepts the write

The write surface is narrow and uneven. From the CapabilityStatement on the secure endpoint:

- **create** — 19 resource types
- **update** — 7
- **patch** — 8
- **delete** — 1 (Coverage)

Never assume a resource is writable because FHIR defines the interaction. Read
`rest.resource[].interaction[].code` for that resource on **that tenant** first — the open endpoint
advertises no writes at all, and tenants can differ.

Also read `updateCreate`. Seven resources declare `updateCreate: false`, meaning **`PUT` will not create**
a resource that does not exist. There is no upsert.

## Step 1 — you have no idempotency key. Plan for it.

This API documents no `Idempotency-Key` header, and the CapabilityStatement declares **no**
`conditionalCreate` and **no** `conditionalUpdate` on any resource. That means:

> A retried `POST /Condition` creates a **second** Condition.

So the de-duplication has to live in your client:

1. Before creating, **search for what you would create**
   (`GET /Condition?patient={id}&...`) and match on the clinically identifying fields.
2. Treat a timed-out or connection-reset `POST` as **UNKNOWN**, never as failed. Re-search before retrying.
3. Keep your own idempotency ledger keyed on your intent, and record the returned resource id from the
   `Location` header the moment a create succeeds.

## Step 2 — update with `If-Match`, always

The one concurrency guarantee the platform documents is version-aware update:

1. `readCondition` → take the version from the `ETag`.
2. `PUT /Condition/{id}` with `If-Match: <that ETag>`.
3. On `409 Conflict`, someone else changed the record. **Re-read, re-decide, then retry** — do not
   blind-overwrite. In an EHR a lost update is a clinical event, not a data-consistency footnote.

Omitting `If-Match` is how you silently discard a clinician's edit.

## Step 3 — send only what Millennium accepts

`422 Unprocessable Entity` has three documented causes, each with its own `OperationOutcome.issue.code`:

| Cause | `issue.code` |
|---|---|
| body contained unsupported fields | `business-rule` |
| body contained modifier extensions | `extension` |
| body contained implicit rules | `not-supported` |

Modifier extensions and `Resource.implicitRules` are **rejected**, not ignored. Send the elements Oracle
Health documents for the resource and nothing else. Where a resource declares a `supportedProfile`
(`us-core-condition`, `us-core-observation-lab`, …), conform to that profile.

## Step 4 — patch where patch is the right verb

Eight resources support `patch` with `application/json-patch+json` (the only patch format the server
advertises). Prefer `patch` over `update` when you are changing one field — it carries less risk of
clobbering elements you did not intend to touch. `If-Match` still applies.

## Step 5 — no transactions

System-level `batch` is supported ("Implemented per the specification"); **`transaction` is not**. A batch
is not atomic. If a multi-resource write must be all-or-nothing, you have to build compensation yourself —
and in an EHR, compensating a clinical write usually means a human, not a rollback.

## Before you go to production

- Rehearse the whole flow on `fhir-ehr-code.cerner.com/r4/{tenant}` against the sandbox tenant.
  The **only** difference in production is the service root, which means there is no visual guardrail
  against pointing a test at a real patient. Make the host an explicit, loudly-logged configuration value.
- Scope down: request the narrowest `.write`/`.c`/`.u` scopes for the resources you touch, not
  `patient/*.write`.
- There is no rate-limit signal and no status page; back off on `429`/`503` and fail closed.
