# ADR-0004: Ship an Admin UI in v1.0 and Keep Every Feature in the Open Source Edition

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

The PRD planned no UI for the first release. Teams moving from a push SaaS often have non-developers who create and send campaigns from a dashboard. With an API only, every send needs code. Features such as SSO and role-based access were earlier listed as candidates for a paid edition, but an admin UI without them does not pass the security reviews of the target users.

## Decision

* v1.0 includes a web admin UI that covers the daily work: creating and scheduling sends, delivery history and analytics, recipients and segments, and settings.
* **Every v1.0 feature is in the open source edition**, including OIDC sign-in, TOTP, and per-app roles ([ADR-0008](0008-admin-authentication-and-authorization.md)).
* The UI uses only the public API, and can be disabled.

## Alternatives Considered

* **API only for v1.0:** cheaper to build, but not a replacement for a push SaaS.
* **Role-based access free, SSO paid:** a common model, but often criticized as an "SSO tax", and it conflicts with putting adoption first.

## Consequences

* Roughly doubles the work before v1.0.
* The browser authentication question ([ARCHITECTURE §9](../ARCHITECTURE.md#9-open-questions)) must be answered before v1.0; see [ADR-0008](0008-admin-authentication-and-authorization.md).
* A possible paid edition needs features beyond those listed here.

## Evidence

* [PRD §4.1](../PRD.md#41-core-architecture) before this change stated that no UI would be built initially.
