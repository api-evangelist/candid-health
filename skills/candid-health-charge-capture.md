---
name: Capture charges in Candid Health
description: Push clinical charges into Candid as charge captures, bundle them, and let Candid build the claim — the path for teams whose EHR emits charges rather than complete claims.
api: openapi/candid-health-charge-capture-api-openapi.yml
operations:
  - getToken
  - createChargeCapture
  - getAllChargeCaptures
  - getChargeCapture
  - getFeeScheduleMatch
generated: '2026-08-15'
method: generated
source: >-
  openapi/candid-health-charge-capture-api-openapi.yml,
  openapi/candid-health-fee-schedules-api-openapi.yml,
  https://docs.joincandidhealth.com/api-reference/charge-capture/v-1
---

# Capture charges in Candid Health

Charge capture is the alternative to building a complete encounter yourself. Your clinical
system emits charges as they happen; Candid groups them into a bundle and assembles the
claim. Use this when the billing decision does not belong in your EHR.

## Steps

1. **Authenticate** — `getToken`.

2. **Create each charge** — `createChargeCapture` (`POST /api/charge_captures/v1`).
   Set `charge_external_id` from your own record id. As with encounters, this is Candid's
   only deduplication mechanism: a repeat create returns HTTP 409
   `ChargeExternalIdConflictError` rather than silently doubling the charge.

3. **Let Candid bundle.** Charges sharing a bundle key are grouped into a charge capture
   bundle, which is what becomes the claim. Bundles have their own resource
   (`charge-capture-bundles/v1`) — read it to see what will actually be billed before it is.

4. **Check the rate before you bill** — `getFeeScheduleMatch`
   (`GET /api/fee-schedules/v3/service-line/{service_line_id}/match`) shows which configured
   fee schedule rate a service line matches. There is also a `test-match` variant documented
   for dry runs. Use it when a charge is billing at an unexpected amount; the answer is
   almost always a fee schedule that did not match.

5. **Reconcile** — `getAllChargeCaptures` (`GET /api/charge_captures/v1`) with
   `page_token` + `limit` for the working list, `getChargeCapture`
   (`GET /api/charge_captures/v1/{charge_capture_id}`) for one.

## Rules

- **Charges are money — treat every create as non-idempotent.** There is no
  `Idempotency-Key`. `charge_external_id` must be deterministic and derived from the source
  record, or a retry storm becomes a billing incident.
- **Post-billed changes are a separate operation.** Once a bundle has been billed, editing
  a charge is not an ordinary update — there is a dedicated changes endpoint
  (`PATCH /api/charge_captures/v1/changes`). Do not attempt to mutate a billed charge through
  the normal update path.
- **Money is not a float.** Amounts are integer cents (`charge_amount_cents`); rates that
  cannot be expressed in cents are carried as a `Decimal` **string**. Parse them as decimals,
  never as IEEE floats.
- **Be a permissive reader.** Candid defines adding an enum or union member as
  non-breaking, and requires clients to handle an `_other` union branch. A charge status your
  code has never seen is expected behaviour, not a bug to crash on.

## Related

- `candid-health-submit-encounter.md` — the complete-claim path instead
- `candid-health-reconcile-remittance.md` — what comes back
