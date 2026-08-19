---
name: Submit an encounter (claim) to Candid Health
description: Create a professional or institutional encounter, the required minimum integration with Candid Health, using a client-supplied external_id so a retry cannot create a duplicate claim.
api: openapi/candid-health-encounters-api-openapi.yml
operations:
  - getToken
  - createEncounter
  - getEncounter
  - getAllEncounters
generated: '2026-08-15'
method: generated
source: openapi/candid-health-encounters-api-openapi.yml + https://docs.joincandidhealth.com/introduction/integration-guide
---

# Submit an encounter (claim) to Candid Health

Candid's own integration guide says the **minimum** integration is the Create Encounter
API. An encounter is the claim: patient, subscriber/coverage, rendering and billing
providers, diagnoses, and service lines.

## Steps

1. **Authenticate** — `getToken`. See `candid-health-authenticate.md`. Reuse the cached token.

2. **Decide who owns the patient record.** This is the fork in the whole integration:
   - Patient and appointment live in **your** system → build the encounter yourself and
     call `createEncounter` (`POST /api/encounters/v4`).
   - Patient and appointment live **in Candid** (pre-encounter services) → use the
     create-from-pre-encounter variant so Candid resolves patient and coverage for you.

3. **Set `external_id` on every create.** Candid has **no `Idempotency-Key` header**.
   `external_id` is your only protection: it is enforced unique server-side, so a duplicate
   create fails with HTTP 409 `EncounterExternalIdUniquenessError` instead of producing a
   second claim. Derive it deterministically from your own record id — never from a clock
   or a random value, or a retry will mint a new one.

4. **Create the encounter** — `createEncounter` (`POST /api/encounters/v4`). You get back
   the encounter with its `encounter_id` and claim status.

5. **Handle the retry case explicitly.** On a timeout or a network error you do **not**
   know whether the encounter was created. Do not blind-retry:
   - Retry once with the **same** `external_id`.
   - If you get 409, the earlier attempt succeeded. Read it back rather than assuming —
     the 409 does not replay the original response body and does not return the id.
   - Recover the id with `getAllEncounters` (`GET /api/encounters/v4`) filtered on your
     external id, then `getEncounter` (`GET /api/encounters/v4/{encounter_id}`).

6. **Confirm** — `getEncounter` returns the stored claim and its current status.

## Rules

- **Sandbox never adjudicates.** In Staging, claims are created and validated but never
  submitted to a payer, so you can exercise steps 1–6 end to end and still learn nothing
  about payer acceptance. PHI is prohibited in Sandbox — use fabricated patients.
- **Validation errors are typed.** HTTP 422 carries the largest family of named errors
  (85 operations declare one). Branch on `errorName`, log the `content` payload, and fix
  the request — these are not retryable.
- **Versions run in parallel.** `encounters/v4` is current; older versions of other
  resources stay live indefinitely and Candid publishes no retirement date. Pin the
  version in your path and do not follow a "latest" convention.
- **You cannot detect a deprecation from the API.** No `Sunset` or `Deprecation` header is
  sent and the OpenAPI marks zero operations deprecated. Subscribe a human to Candid's
  breaking-changes mailing list via support@joincandidhealth.com.

## Related

- Eligibility before you bill: `candid-health-check-eligibility.md`
- Getting paid back: `candid-health-reconcile-remittance.md`
- Conventions: `conventions/candid-health-conventions.yml`
