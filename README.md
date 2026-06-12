# Candid Health (candid-health)

Candid Health is a medical billing automation platform providing REST APIs for submitting claims, checking real-time eligibility, managing encounters, processing remittances, handling patient collections, credentialing, and full revenue cycle management (RCM) for healthcare providers and digital health companies. Their API-first approach enables seamless integration with EHRs, practice management systems, and healthcare technology stacks.

APIs.json: https://raw.githubusercontent.com/api-evangelist/candid-health/refs/heads/main/apis.yml

Naftiko: https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=candid-health-api-evangelist&utm_content=repo

## Tags

Medical Billing, Revenue Cycle Management, Healthcare, Claims, Eligibility, Prior Authorization, Remittance, Patient Collections, Credentialing, Insurance

## APIs

| Name | Description |
|------|-------------|
| Eligibility API | Real-time eligibility verification via Availity and Change Healthcare with batch support |
| Encounters API | Create and manage professional and institutional encounter records for claims billing |
| Patient Collections API | Patient invoicing, payments, refunds, and accounts receivable management |
| Charge Capture API | Create and manage charge capture claims and bundles for batch processing |
| Exports API | Download CSV exports of claim status changes and reporting data |
| Patients API | Core patient management including coverages, appointments, images, and notes |
| Credentialing API | Provider and facility credentialing, contracts, payers, and fee schedules |
| Auth API | OAuth 2.0 client credentials token generation for API access |

## Plans / Rate Limits / FinOps

| Resource | Description |
|----------|-------------|
| [Plans & Pricing](plans/candid-health-plans-pricing.yml) | Enterprise custom pricing based on claim volume; contact sales for terms |
| [Rate Limits](rate-limits/candid-health-rate-limits.yml) | 1000 requests per 10-second rolling window per IP; HTTP 429 on excess; SDK exponential backoff |
| [FinOps](finops/candid-health-finops.yml) | FOCUS-aligned cost guidance; primary cost drivers are claim volume and eligibility check volume |

## Timestamps

- **Created:** 2026-06-12
- **Modified:** 2026-06-12

## Common

| Type | URL |
|------|-----|
| Website | https://candidhealth.com/ |
| Documentation | https://docs.joincandidhealth.com/ |
| GitHub | https://github.com/candidhealth |
| LinkedIn | https://www.linkedin.com/company/candid-health |
| Blog | https://candidhealth.com/blog |
| Pricing | https://candidhealth.com/integrations |
| Status Page | https://status.joincandidhealth.com |
| X (Twitter) | https://x.com/candid_health |

## Maintainers

| Name | Email |
|------|-------|
| Kin Lane | kin@apievangelist.com |
