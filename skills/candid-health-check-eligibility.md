---
name: Check patient eligibility with Candid Health
description: Run a real-time X12 270/271 eligibility and benefits check through Candid, either as a synchronous passthrough or as a batch, and test it first against Candid's published mock payer fixtures.
api: openapi/candid-health-eligibility-api-openapi.yml
operations:
  - getToken
  - submitEligibilityCheck
  - checkCoverageEligibility
  - post
  - batch
  - poll-batch
  - payer-search
generated: '2026-08-15'
method: generated
source: >-
  openapi/candid-health-eligibility-api-openapi.yml,
  openapi/candid-health-subpackage-pre-encounter-subpackage-pre-encounter-eligibilitychecks-subpackage-pre-encounter-eligibilitychecks-v1-api-openapi.yml,
  https://docs.joincandidhealth.com/api-reference/pre-encounter/eligibility-checks/mocking-eligibility-checks
---

# Check patient eligibility with Candid Health

An eligibility check confirms active coverage and returns the patient's benefits before
you bill. Underneath it is an X12 **270** inquiry and a **271** response; Candid types the
result rather than handing you raw EDI, and its coverage schemas cite the X12 element
numbers they came from.

There are **two** surfaces. Pick one deliberately.

## Surface A — pre-encounter eligibility checks (`pre-api` host)

The current surface, routed through Stedi.

1. **Authenticate** — `getToken`.
2. **Find the payer id** — `payer-search` (`GET /eligibility-checks/v1/payer/search`).
   Payer ids are the number one source of failed checks; never hardcode one you did not look up.
3. **Run the check** — `post` (`POST /eligibility-checks/v1`) with `payer_id`, `provider`
   (organization_name + NPI), `subscriber`, optional `dependent`, and
   `encounter.service_type_codes`.
4. **For volume, batch it** — `batch` (`POST /eligibility-checks/v1/batch`) then
   `poll-batch` (`GET /eligibility-checks/v1/batch/{batch_id}`) until it completes.
   There are no webhooks; polling is the only completion signal Candid offers.

## Surface B — eligibility v2 (`api` host)

1. `submitEligibilityCheck` (`POST /api/eligibility/v2`) — the encounter-side check.
2. `checkCoverageEligibility` (`POST /api/pre-encounter/coverages/v1/check_eligibility`) —
   check against a coverage already stored in Candid.

Production eligibility routes through **Change Healthcare** and **Availity**. Both are
tracked as separate components on `status.joincandidhealth.com`; when eligibility fails
broadly, check there before you debug your payload.

## Test it first — Candid publishes real fixtures

In Staging, eligibility is served by Stedi's Test API. Candid publishes exact request
bodies that trigger named outcomes. Use them verbatim; do not invent test members.

- Provider NPI for every fixture: `1999999984`, service type code `30`.
- Aetna (`payer_id` `60054`): member `AETNA9wcSu` (with dependent), `AETNA12345` (subscriber only) → success.
- UHC/AAA (`payer_id` `87726`): `UHCAAA42` payer unreachable · `UHCAAA43` missing provider id ·
  `UHCAAA72` missing subscriber insured id · `UHCAAA73` missing subscriber insured name ·
  `UHCAAA75` subscriber not found · `UHCAAA79` invalid participant id.
- `payer_id` `BAD_PAYER_ID` → a clearinghouse HTTP 400.

Full bodies, including the deliberately malformed date of birth in the 400 fixture, are in
`sandbox/candid-health-sandbox.yml`.

## Rules

- **Exercise the failure fixtures, not just the happy path.** Six of the nine published
  fixtures are failures, which is an accurate picture of live eligibility traffic. An
  integration that only handles `UHCAAA` success will break on day one.
- **PHI is prohibited in Sandbox.** The fixture identities above are fabricated vendor test
  data — that is the point of them.
- **Eligibility is a passthrough, so payer latency is your latency.** Use `batch` +
  `poll-batch` for anything above interactive volume, and remember the 1000 req / 10 s
  per-IP ceiling applies to the polling too.
- Coverage detail is deep: benefit type, coverage level, network type, insurance type and
  service type codes all arrive as typed enums carrying their X12 provenance. Store the
  codes, not just a boolean "eligible".

## Related

- `candid-health-submit-encounter.md` — bill after you have confirmed coverage
- `sandbox/candid-health-sandbox.yml` — every published fixture
