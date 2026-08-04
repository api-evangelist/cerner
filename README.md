# Cerner (Oracle Health) (cerner)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Cerner is a global healthcare technology company that designs and develops electronic health record (EHR) and health information technology solutions for hospitals, clinics, and integrated delivery networks. Cerner was acquired by Oracle in June 2022 and is now branded as Oracle Health, with the Cerner Millennium EHR platform's developer program operating as the Oracle Health Developer Program. Millennium exposes HL7 FHIR R4 and DSTU2 APIs, SMART on FHIR app launching, Bulk FHIR, the CareAware device and integration APIs, and the Code Console developer portal for registering applications and obtaining sandbox and production credentials.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/cerner/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/cerner/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- Cerner Millennium
- Code Console
- EHR
- Electronic Health Records
- FHIR
- HL7
- Healthcare
- Interoperability
- OAuth 2.0
- Oracle Health
- Patient Access
- Provider Directory
- SMART on FHIR

## Timestamps

- **Created:** 2026-03-23
- **Modified:** 2026-05-30

## APIs

### Oracle Health Millennium Platform FHIR R4 API

The Oracle Health Millennium Platform FHIR R4 API provides OAuth 2.0-secured access to Cerner Millennium EHR data in the HL7 FHIR R4 format, exposing USCDI-aligned resources such as Patient, Practitioner, Observation, Condition, MedicationRequest, DocumentReference, and Encounter for patient-access apps, provider apps, and interoperability partners.

- **Human URL:** [https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html](https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html)

#### Tags

- CMS Interoperability
- FHIR
- HL7
- Patient Access
- R4
- USCDI

#### Properties

- [Documentation](https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html)
- [Portal](https://docs.oracle.com/en/industries/health/millennium-platform-apis/index.html)
- [Developer Program](https://www.oracle.com/health/developer/)
- [OpenAPI](openapi/cerner-oracle-health-fhir-r4-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Oracle Health Millennium FHIR DSTU2 API

The Cerner Millennium DSTU2 FHIR API supports legacy SMART on FHIR applications and integrations with Meaningful Use 2015 CEHRT certification criteria, and remains available alongside the newer R4 implementation for backward compatibility.

- **Human URL:** [https://fhir.cerner.com/millennium/dstu2/](https://fhir.cerner.com/millennium/dstu2/)

#### Tags

- DSTU2
- FHIR
- Legacy
- SMART on FHIR

#### Properties

- [Documentation](https://fhir.cerner.com/millennium/dstu2/)
- [Portal](https://fhir.cerner.com/)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Oracle Health Code Console (Developer Portal)

The Oracle Health Code Console (formerly Cerner Code) is the developer portal used to register SMART on FHIR and system-level applications, configure redirect URIs and launch parameters, manage OAuth 2.0 client credentials, and access the Millennium sandbox for initial testing.

- **Human URL:** [https://code.cerner.com/](https://code.cerner.com/)

#### Tags

- Code Console
- Developer Portal
- OAuth 2.0
- Registration
- Sandbox

#### Properties

- [Website](https://code.cerner.com/)
- [A P I Access](https://www.oracle.com/health/developer/api/)
- [Authorization](https://fhir.cerner.com/authorization/)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Oracle Health Millennium Bulk FHIR API

Oracle Health Millennium supports the HL7 Bulk Data Access (Flat FHIR) specification for exporting group-level patient data in NDJSON format for population health, research, and payer-provider data exchange scenarios.

- **Human URL:** [https://fhir.cerner.com/millennium/r4/](https://fhir.cerner.com/millennium/r4/)

#### Tags

- Bulk Data
- FHIR
- Flat FHIR
- Population Health

#### Properties

- [Documentation](https://fhir.cerner.com/millennium/r4/)
- [Reference](https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Cerner CareAware Integration APIs

Cerner CareAware provides device and third-party application integration APIs for medical device data capture, bi-directional HL7 v2 messaging, and workflow embedding into Millennium, supporting medical device manufacturers and hospital biomedical teams.

- **Human URL:** [https://www.cerner.com/solutions/careaware-interoperability](https://www.cerner.com/solutions/careaware-interoperability)

#### Tags

- CareAware
- Device Integration
- HL7 v2
- Medical Device

#### Properties

- [Website](https://www.cerner.com/solutions/careaware-interoperability)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Oracle Health SMART on FHIR App Launch

Oracle Health implements the SMART on FHIR App Launch framework (standalone and EHR-launch) with OpenID Connect identity tokens, enabling third-party clinician and patient-facing applications to embed inside Millennium PowerChart and Oracle Health portals.

- **Human URL:** [https://fhir.cerner.com/authorization/openid-connect/](https://fhir.cerner.com/authorization/openid-connect/)

#### Tags

- App Launch
- Clinician App
- Patient App
- SMART on FHIR

#### Properties

- [Documentation](https://fhir.cerner.com/authorization/openid-connect/)
- [Portal](https://fhir.cerner.com/)
- [Postman Collection](collections/cerner-oracle-health-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/cerner-oracle-health-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/cerner-corporation)
- [Website](https://www.cerner.com)
- [Corporate](https://www.oracle.com/health/)
- [Developer](https://www.oracle.com/health/developer/)
- [API Reference](https://docs.oracle.com/en/industries/health/millennium-platform-apis/index.html)
- [F H I R](https://fhir.cerner.com/)
- [Code Console](https://code.cerner.com/)
- [Open Source](https://github.com/cerner)
- [Privacy Policy](https://www.oracle.com/legal/privacy/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
