---
name: Authenticate with Candid Health
description: Exchange a client_id and client_secret for a Candid Health bearer token, cache it for its full 5-hour lifetime, and attach it to every subsequent call. Every other Candid skill starts here.
api: openapi/candid-health-auth-api-openapi.yml
operations:
  - getToken
generated: '2026-08-15'
method: generated
source: openapi/candid-health-auth-api-openapi.yml + https://docs.joincandidhealth.com/introduction/getting-started
---

# Authenticate with Candid Health

Candid Health uses OAuth 2.0 **client credentials**. There are no scopes: one token
grants everything the tenant can do. Treat the token as a tenant-wide secret.

## Before you start

- You need a `client_id` / `client_secret` pair **for the environment you are calling**.
  Credentials are environment-scoped — a Staging pair fails against Production and vice
  versa. Candid's implementation team issues them; there is no self-serve signup.
- Pick the host:
  - Production core: `https://api.joincandidhealth.com`
  - Production pre-encounter: `https://pre-api.joincandidhealth.com`
  - Sandbox core: `https://api-staging.joincandidhealth.com`
  - Sandbox pre-encounter: `https://pre-api-staging.joincandidhealth.com`

## Steps

1. **Get a token** — `getToken` (`POST /api/auth/v2/token`).
   Send `client_id` and `client_secret` in the JSON body. You get back an access token
   and its expiry. The token is an Auth0-issued JWT valid for **5 hours**.

2. **Cache it.** This is not optional. Candid rate-limits the token endpoint itself and
   returns `TooManyRequestsError` (HTTP 429) if you mint a token per request. Store the
   token, store its expiry, and refresh only when it is close to expiring.

3. **Attach it** to every other call as `Authorization: Bearer <access_token>`.

4. **Verify a token you did not mint**, if you need to: Candid publishes the signing
   public key at `https://candidhealth.auth0.com/pem`.

## Rules

- **Never mint per request.** The failure mode is a 429 on the auth endpoint, which
  takes down every downstream call at once.
- **Never mix environments.** A cross-environment credential returns an auth failure,
  not a helpful message. Key your token cache by `(environment, client_id)`.
- **Do not ask for scopes.** Candid issues none. If your agent needs least-privilege
  separation, it must be enforced on your side of the call, not by the token.
- The whole surface is rate-limited **per IP** at 1000 requests / 10 seconds rolling,
  and no `RateLimit-*` headers are returned. Back off exponentially on 429; the official
  SDKs already do this.

## Errors

Candid returns `{ errorName, content }` on `application/json` — branch on `errorName`,
not on the status code. See `errors/candid-health-problem-types.yml`.
