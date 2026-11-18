# ADR-0008: Admin Authentication and Authorization

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

The admin UI ([ADR-0004](0004-admin-ui-in-ga-all-features-open-source.md)) needs browser-safe authentication. Server-to-server API keys must never reach a browser. With multiple apps per installation ([ADR-0007](0007-v1-feature-scope.md)), access must be limited per app. Security reviews in regulated sectors usually ask for SSO and multi-factor authentication.

## Decision

| Topic | Decision |
|-------|----------|
| Browser sessions | **BFF:** server-side sessions in an `HttpOnly`, `SameSite` cookie, with CSRF protection. No tokens are stored in the browser |
| Local users | Username and password, hashed with a memory-hard algorithm (for example Argon2id) |
| Multi-factor | **TOTP** for local users. Users signing in through OIDC rely on their identity provider |
| SSO | **OIDC**, optional. SAML is not in v1.0 |
| Roles | Admin, Sender, Viewer, **assigned per app**, plus installation-wide administrators |
| First administrator | Created with a CLI command or a one-time environment variable. No default password |
| API keys | Issued per app, stored as hashes only, with scopes (for example send-only or read-only) |
| Authorization | Enforced on the server for every request, for the UI and the API alike |

## Alternatives Considered

* **Short-lived OIDC tokens in the browser:** requires an identity provider for every installation and keeps tokens in the browser.
* **Global roles only:** simpler, but cannot separate teams that own different apps.
* **SAML in v1.0:** the main identity providers (Okta, Entra ID, Google Workspace) also support OIDC.

## Consequences

* Answers [ARCHITECTURE Open Question 1](../ARCHITECTURE.md#9-open-questions).
* TOTP secrets and the OIDC client secret must be stored encrypted ([ADR-0010](0010-secret-encryption-and-kms-plugins.md)).
* Custom screens ([ADR-0006](0006-ui-customization-and-ai-skill.md)) use the same session and cannot bypass authorization.

## Evidence

* No external sources; this follows common practice for browser sessions.
