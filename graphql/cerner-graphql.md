# Cerner (Oracle Health) GraphQL

Cerner Millennium, now operated as Oracle Health, exposes clinical and administrative data through HL7 FHIR R4 REST APIs. There is no native GraphQL endpoint offered by Oracle Health or the Cerner Millennium platform. The schema below is a conceptual GraphQL representation derived from the FHIR R4 resource model documented at https://fhir.cerner.com/millennium/r4/ and https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html.

## Source APIs

- FHIR R4 REST API: https://fhir.cerner.com/millennium/r4/
- Oracle Health Millennium Platform API Reference: https://docs.oracle.com/en/industries/health/millennium-platform-apis/mfrap/r4_overview.html
- Oracle Health Developer Program: https://www.oracle.com/health/developer/
- Code Console (developer portal): https://code.cerner.com/

## Schema Notes

The conceptual schema maps FHIR R4 resources to GraphQL types. FHIR uses a RESTful interaction model with resource types addressed by logical IDs, references using relative or absolute URLs, and extensions for non-core data elements. A GraphQL layer over Cerner Millennium would need to:

1. Translate GraphQL queries to FHIR search/read operations against tenant-specific base URLs (e.g., `https://<tenant>.millennium.com/fhir/r4/`).
2. Resolve FHIR references (e.g., `subject.reference = "Patient/12345"`) as nested GraphQL object lookups.
3. Handle OAuth 2.0 SMART on FHIR bearer tokens (patient-level or system-level scopes).
4. Support FHIR pagination via `Bundle.link[relation=next]` cursors.

## Resource Coverage

The schema covers the following FHIR R4 resource categories as implemented by Cerner Millennium:

### Individuals
- Patient, Person, RelatedPerson, Practitioner, PractitionerRole

### Organizations and Locations
- Organization, Location

### Clinical Encounters
- Encounter, EpisodeOfCare

### Scheduling
- Appointment, Slot, Schedule

### Clinical Summary
- Condition, AllergyIntolerance, Procedure, CareTeam, CarePlan, Goal

### Medications
- MedicationRequest, MedicationStatement, Medication

### Diagnostics
- Observation, DiagnosticReport, ServiceRequest, Specimen, ImagingStudy

### Immunizations
- Immunization

### Devices
- Device, DeviceRequest

### Documents
- DocumentReference, Binary, QuestionnaireResponse, Questionnaire

### Financial
- Coverage, Claim, ClaimResponse

### Workflow
- Task, Communication, CommunicationRequest, Subscription

### Consent and Provenance
- Consent, Provenance, AuditEvent

### Infrastructure
- CapabilityStatement, OperationOutcome

## Authentication

SMART on FHIR OAuth 2.0 with scopes such as:
- `patient/Patient.read`
- `patient/Observation.read`
- `system/*.read`
- `launch` (EHR launch context)
- `openid fhirUser` (OpenID Connect identity)

## GitHub

https://github.com/cerner
