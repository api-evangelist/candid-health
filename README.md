# Candid Health (candid-health)

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
