---
name: cerner-backend-bulk-export
description: >-
  Run a population-scale bulk export from Oracle Health Millennium as a backend service — SMART Backend
  Services authorization with private_key_jwt and system/ scopes, then the FHIR Bulk Data Access
  kick-off / poll / download / delete cycle.
api: openapi/cerner-millennium-bulk-data-openapi.yml
operations:
  - kickOffGroupExport
  - kickOffPatientListExport
  - getBulkExportJob
  - getBulkExportFileUrls
  - deleteBulkExportJob
generated: '2026-08-14'
method: generated
source: >-
  openapi/cerner-millennium-bulk-data-openapi.yml,
  https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfbda/rest-endpoints.html,
  well-known/cerner-smart-configuration.json
---

# Bulk export from Millennium as a backend service

This is the no-user path: no browser, no patient sitting there, a scheduled job pulling a population.

## Step 1 — authorize as a backend service

Millennium implements the full SMART Backend Services profile, and the tenant's
`/.well-known/smart-configuration` proves it:

- `grant_types_supported` includes `client_credentials`
- `token_endpoint_auth_methods_supported` includes `private_key_jwt`
- `token_endpoint_auth_signing_alg_values_supported` is `["RS384", "ES384"]`
- 103 `system/` scopes are advertised

Register the app, publish your JWKS, then mint a signed client assertion (RS384 or ES384) and exchange it
at the tenant's token endpoint for a token carrying only the `system/` scopes you need — for a Group
export that is typically `system/Group.read` plus `system/<Resource>.read` for each resource type you
intend to receive.

Read the token endpoint from the discovery document for **that tenant**. It is tenant-scoped and it is not
the same string across tenants or hosts.

## Step 2 — kick off (`kickOffGroupExport` / `kickOffPatientListExport`)

```
GET  /Group/{Group_ID}/$export      # every member of a Group
POST /Patient/$export               # a caller-supplied patient list
Accept: application/fhir+json
Prefer: respond-async
```

A successful kick-off returns `202 Accepted` with a `Content-Location` header naming the job. `$export`
on Group is declared in the CapabilityStatement itself (OperationDefinition
`https://fhir-ehr.cerner.com/r4/OperationDefinition/group-export`), so you can confirm support on a tenant
before you try.

## Step 3 — poll (`getBulkExportJob`)

```
GET /bulk-export/jobs/{Job_ID}
```

`202` while running, `200` with the output manifest when complete. Poll with backoff — there are **no**
rate-limit headers on this platform, so you get no budget signal and should be conservative. A population
export is a heavy job; poll in minutes, not seconds.

## Step 4 — download (`getBulkExportFileUrls`)

```
GET /bulk-export/files/{File_ID}
```

Returns the download URL(s) for one NDJSON output file listed in the manifest. Output is newline-delimited
FHIR — one resource per line — so stream it; do not load a population export into memory.

## Step 5 — clean up (`deleteBulkExportJob`)

```
DELETE /bulk-export/jobs/{Job_ID}
```

Delete the job when you have the data. Leaving completed exports sitting on the server holds PHI you have
already taken delivery of.

## Handling and safety

- **Everything you download is PHI.** Encrypt at rest, scope retention to the agreement with the health
  system, and log access. The bulk surface is the largest-blast-radius operation on this API by a wide
  margin.
- **No idempotency key.** If a kick-off request times out, do **not** blindly retry — poll for an existing
  job first, or you will start a second population export.
- **No status page and no SLA.** If a job stalls there is no first-party availability signal to check; the
  escalation path is the health system's Oracle Health account channel.
- Errors are FHIR `OperationOutcome`. A `403` means the tenant or the app's provisioning, not the Group.
