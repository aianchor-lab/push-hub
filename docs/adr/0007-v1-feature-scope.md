# ADR-0007: Feature Scope of v1.0

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

Since the first release is GA ([ADR-0003](0003-first-release-is-ga.md)), it must cover what a team needs to replace a push SaaS. An established open source gateway, gorush, already sends to APNs, FCM, and HMS with queues, retries, statistics, and Prometheus metrics. It does not store devices or users, and it reports failed sends through a webhook instead of deactivating tokens. Immediate delivery alone would not be different from it.

## Decision

v1.0 includes:

| Feature | Notes |
|---------|-------|
| Immediate delivery, device registration, bounce handling | Invalid tokens are deactivated automatically |
| **Multiple apps per installation** | "App" is the top-level scope of devices, users, keys, and roles |
| Scheduled delivery | |
| Delivery analytics | API, Prometheus metrics, and the admin UI |
| Users linked to devices, tags, segment delivery | |
| Open tracking | Client API only; no SDK ([ADR-0011](0011-client-keys-and-client-port.md)) |
| Rich push | Image URLs, action buttons, and `mutable-content` passed through to APNs and FCM |
| Admin UI | [ADR-0004](0004-admin-ui-in-ga-all-features-open-source.md) |
| Helm chart, signed images, SBOM | [ARCHITECTURE §5](../ARCHITECTURE.md#5-distribution) |

Not in v1.0:

* **Huawei (HMS):** the main market is English-speaking; reconsidered on request.
* **Web Push:** needs VAPID keys and a browser service worker. Added after v1.0. The design leaves room for more platforms.

## Alternatives Considered

* **Immediate delivery only:** no different from existing gateways.
* **Single app per installation:** adding apps later would change almost every table and endpoint.

## Consequences

* The work before v1.0 is large, which the roadmap accepts in exchange for a product that can replace a SaaS.
* The data model is scoped by app from the start.

## Evidence

* [appleboy/gorush README](https://github.com/appleboy/gorush) (checked 2026-11-17; 8,799 stars, MIT, last pushed 2026-07-25). Features list: FCM, APNs, HMS, queues (local, NSQ, NATS, Redis), retries, success and failure counts, Prometheus, gRPC, `feedback_hook_url` for failed sends. No device registry, scheduling, segments, or UI found in the README; the source code was not checked.
