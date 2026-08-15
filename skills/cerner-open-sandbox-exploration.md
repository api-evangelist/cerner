---
name: cerner-open-sandbox-exploration
description: >-
  Explore the Oracle Health Millennium Platform FHIR R4 API with no credentials at all, using the open
  read-only endpoint and the anonymously-served CapabilityStatement and SMART discovery documents. The
  fastest way to learn the real contract before registering an application.
api: openapi/cerner-millennium-fhir-r4-openapi.yml
operations:
  - getCapabilityStatement
  - searchPatient
  - searchCondition
  - searchObservation
  - readPatient
generated: '2026-08-14'
method: generated
source: >-
  openapi/cerner-millennium-fhir-r4-openapi.yml, sandbox/cerner-sandbox.yml — every call below was run
  anonymously on 2026-08-14
---

# Explore Millennium with no credentials

Oracle Health runs an open, unauthenticated, read-only FHIR endpoint. That is unusual and it is the single
best on-ramp to this platform: you can read the real contract and real (synthetic) data before you talk to
anyone.

Service root:

```
https://fhir-open.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d
```

`ec2458f2-1e24-41c8-b71b-0e701af7583d` is the published public sandbox tenant.

## Step 1 — read the contract (`getCapabilityStatement`)

```
GET https://fhir-open.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d/metadata
Accept: application/fhir+json
```

Returns a ~59KB FHIR `CapabilityStatement`. This is the authoritative contract, not the docs. Read:

- `rest.resource[].type` — 42 resource types on the open endpoint, 44 on the secure one.
- `rest.resource[].interaction[].code` — which of read / search-type / create / update / patch / delete
  each resource supports. The open endpoint has only read and search-type.
- `rest.resource[].searchParam[]` — the real parameters, with `documentation` that states which
  combinations are **required**. This is where you learn that most resources cannot be listed unqualified.
- `rest.resource[].supportedProfile` — the US Core profile each resource conforms to (23 of them).
- `instantiates` — the server declares itself an instance of the US Core server and Bulk Data
  capability statements.
- `rest.resource[].operation[]` — named `$` operations: `$export` on Group, `$credit`/`$modify` on
  ChargeItem, `$docref` on DocumentReference, `$health-cards-issue` on Patient.

## Step 2 — read the auth contract, still anonymously

```
GET https://fhir-ehr-code.cerner.com/r4/ec2458f2-1e24-41c8-b71b-0e701af7583d/.well-known/smart-configuration
```

Served **without a token** even though the resources behind it are not. It gives you the tenant's
authorize/token/revoke/introspect endpoints, the grant types, PKCE method, client authentication methods
(`client_secret_basic`, `private_key_jwt` with RS384/ES384) and the full advertised scope list — 303 on
the provider persona, 145 on the patient persona.

Note the path: discovery is served relative to the **tenant service root**, not the host root. The host
root answers 502 for every `/.well-known/*` path.

## Step 3 — pull real data

```
GET /Patient?name=smart
```

Returns a `searchset` Bundle of synthetic sandbox patients with real Millennium ids. Take an id and fan
out:

```
GET /Condition?patient={id}
GET /Observation?patient={id}
GET /Encounter?patient={id}
```

Follow `Bundle.link[relation=next]` for paging; do not construct page URLs.

## Step 4 — provoke the errors on purpose

The sandbox has no failure-injection tooling, so trigger error paths naturally:

- `GET /Condition` with no parameters → `400`, OperationOutcome "No supported search parameters provided".
- `Accept: application/xml` → `406` with `Content-Length: 0` and **no body** — easy to misread as an
  empty result; handle it explicitly.
- Any secure host without a token → `401`.
- A bad tenant in the path → `403 Tenant not valid or accessible`.

## What the open endpoint cannot show you

Writes. `create`/`update`/`patch`/`delete` exist only on the secure endpoints, which need a registered
app and a SMART token. The secure CapabilityStatement (`fhir-ehr-code.cerner.com/.../metadata`, also
anonymous) lists the whole write surface, so you can design against it before you register:
create on 19 resources, update on 7, patch on 8, delete on 1.

## Carry these facts forward

- No idempotency key. A retried `POST` creates a duplicate. Concurrency is `ETag` + `If-Match` on update,
  with a documented `409` on a stale version.
- No rate-limit headers and no published limits.
- Errors are `OperationOutcome`, JSON only.
