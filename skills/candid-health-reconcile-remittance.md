---
name: Track claim status and reconcile remittance in Candid Health
description: Keep a downstream system in sync with Candid by polling the events scan endpoint, then pull the 835/ERA adjudication detail and CSV exports — the closing half of the revenue cycle, done without webhooks because Candid ships none.
api: openapi/candid-health-events-api-openapi.yml
operations:
  - getToken
  - scanEvents
  - getEvent
  - getInsuranceAdjudication
  - get-exports
generated: '2026-08-15'
method: generated
source: >-
  openapi/candid-health-events-api-openapi.yml,
  openapi/candid-health-insurance-adjudications-api-openapi.yml,
  openapi/candid-health-subpackage-exports-subpackage-exports-v3-api-openapi.yml
---

# Track claim status and reconcile remittance in Candid Health

**Candid has no webhooks.** They are not on the roadmap. Everything downstream of claim
submission is pull-based, so the shape of this integration is a durable cursor and a loop,
not a listener. Build it that way from the start.

## Steps

1. **Authenticate** — `getToken`.

2. **Poll the event feed** — `scanEvents` (`GET /api/events/v1`), which returns billing
   lifecycle events ordered by modification time. Parameters: `limit`, `event_types`,
   `page_token`.
   - Filter with `event_types` so you are not paging the whole tenant to find the three
     transitions you care about.
   - **Persist the page token.** It is the cursor. If you lose it you re-scan from the
     beginning, which at claim volume will hit the 1000 req / 10 s per-IP ceiling.
   - Poll on an interval your volume justifies. There is no long-poll and no push.

3. **Fetch an individual event** — `getEvent` (`GET /api/events/v1/{event_id}`) when the
   scan payload is not enough.

4. **Pull the remittance** — `getInsuranceAdjudication`
   (`GET /api/insurance_adjudications/v1/{insurance_adjudication_id}`) returns the ERA / 835
   adjudication detail: what the payer allowed, paid, adjusted and denied, down to the
   service line.

5. **Reconcile in bulk** — `get-exports` (`GET /api/exports/v3`) downloads CSV exports of
   claim status changes and financial data. Use exports for daily/periodic reconciliation
   and the event feed for near-real-time state; they answer different questions and a
   serious integration runs both.

## Rules

- **Design for at-least-once, not exactly-once.** A polled feed plus a cursor you persist
  yourself means you will reprocess events after a crash. Make the downstream write
  idempotent on `event_id` — Candid will not do it for you.
- **Reconcile against exports, not against your own memory.** The event feed tells you what
  changed; the export tells you what is true. If they disagree, the export wins.
- **Sandbox cannot exercise this path.** Staging claims are never submitted and never
  adjudicated, so no real 835 comes back. The remittance half of your integration is
  effectively untestable before production — write it defensively and roll it out on a
  small claim cohort first.
- **Watch the right status components.** `status.joincandidhealth.com` tracks "ERA
  processing pipeline", "Claim Submission" and "Exports" as components separate from "API".
  A healthy API does not mean remittance is flowing.
- **429 has no headers.** No `RateLimit-*`, no `Retry-After`. Back off exponentially and
  keep your poll interval well clear of the ceiling, remembering the limit is per **IP** —
  every worker behind one NAT egress shares the budget.

## Related

- `candid-health-submit-encounter.md` — the claim this reconciles
- `lifecycle/candid-health-lifecycle.yml` — status page components and dependencies
