# Product Requirements Document (PRD)

> **Status:** Draft
> **Last updated:** 2026-11-18
>
> This document is a working draft. Its contents, including licensing and scope, may change as development proceeds and feedback arrives. Undecided items are marked **TBD** and tracked in [Open Questions](#5-open-questions).

## 1. Project Overview

* **Project name:** `push-hub` (`aianchor-lab/push-hub`)

* **Concept:** A self-hosted push notification delivery engine (middleware) for enterprises.

* **Vision:** Provide a robust, lightweight push delivery platform that can be deployed with a single command inside a company's own infrastructure (VPC / on-premises), for companies struggling with the high cost (pay-per-use pricing) and the data governance / regional constraints of SaaS-based push delivery services.

* **Approach:** Build a working product first, then promote it and learn from real usage ([ADR-0002](adr/0002-build-first.md)). The first release is **v1.0.0 (GA)**, with no alpha or beta releases and no target date ([ADR-0003](adr/0003-first-release-is-ga.md)). Until then, interest is gauged by GitHub stars and issues. See the [Roadmap](ROADMAP.md).

* **Support:** Best-effort, with no SLA. See the [Support Policy](../README.md#support-policy).

* **Continuity:** Continued development is not guaranteed. Because push-hub is Apache 2.0 and never connects to us, users can keep running and maintaining it themselves. Discontinuation will be announced at least 6 months in advance. See [Project Continuity](../README.md#project-continuity).

## 2. Target Personas & Pain Points

### 2.1 Target Users

* **B2B enterprises / security-focused startups**

  * Companies in healthcare, finance, SaaS, government, and similar sectors that cannot send customer data or push tokens to external SaaS providers (e.g., in the US).

  * Development teams and DevOps engineers who deliver millions to hundreds of millions of push notifications per month and are squeezed by the pay-per-use pricing of existing SaaS (the "SaaS Tax").

### 2.2 Problems to Solve

1. **Data sovereignty / compliance violations:**

   * Companies do not want customer behavior logs, notification message bodies, or device tokens to be stored on external SaaS servers.

2. **High SaaS pay-per-use costs:**

   * A cost structure that rises exponentially as delivery volume grows.

3. **Vendor lock-in:**

   * Breaking free from dependence on specific cloud services or SaaS providers.

## 3. Business Model & Licensing

* **Model:** Open Source first. Adoption and real-world usage come before revenue. Every v1.0 feature, including the admin UI, SSO, and roles, is in the open source edition ([ADR-0004](adr/0004-admin-ui-in-ga-all-features-open-source.md)). Revenue, if any, comes later from corporate sponsorship and paid help with deployments. An open-core commercial edition may be considered once demand is proven.

* **License:** **Apache License 2.0** — see [LICENSE](../LICENSE) and [NOTICE](../NOTICE). Free for any use, including commercial production use.

  * **Copyright notice:** `Copyright 2026 Atsushi Inoue (https://github.com/aianchor-lab)`.

  * **Why Apache 2.0:** many enterprise legal teams pre-approve OSI-approved licenses, while a source-available license requires a case-by-case review that slows adoption. Apache 2.0 also includes an explicit patent grant.

  * **Hosted offerings by third parties are allowed.** push-hub's value is running inside the user's own infrastructure, so a third-party SaaS serves a different market. The project name is protected by trademark rather than by the license.

  * **Alternatives considered:** BSL 1.1 (2026-10-28 to 2026-11-12), AGPLv3, FSL, and the Elastic License. See [ADR-0001](adr/0001-apache-2-0-license.md).

* **Contributions:** made under the **Developer Certificate of Origin (DCO) 1.1** and licensed under the Apache License 2.0 ([ADR-0013](adr/0013-dco-instead-of-cla.md)). Each commit carries a `Signed-off-by` line, checked in CI. There is no CLA. See [CONTRIBUTING.md](../CONTRIBUTING.md).

## 4. Technical Requirements

See [ARCHITECTURE.md](ARCHITECTURE.md) for the architecture direction that implements these requirements.

### 4.1 Core Architecture

* **Deployment:** Single-command startup via Docker / `docker-compose.yml`.

* **Interface:** RESTful JSON API (application server ⇔ this engine ⇔ APNs / FCM).

* **Admin UI:** v1.0 includes a web admin UI for sending, scheduling, delivery history and analytics, recipients and segments, and settings ([ADR-0004](adr/0004-admin-ui-in-ga-all-features-open-source.md)). It is built without a build step ([ADR-0005](adr/0005-no-build-admin-ui.md)), uses only the public API, and can be disabled. Customization is limited to branding and themes; custom screens are built on the public API with a bundled AI skill ([ADR-0006](adr/0006-ui-customization-and-ai-skill.md)).

* **Operational monitoring** is based on integration with standard external tools (Prometheus / Grafana, etc.).

### 4.2 Observability Requirements

* **Metrics:** Provide a Prometheus-format endpoint (`/metrics`).

* **Logs:** Structured JSON logs written to stdout (compatible with collection by Datadog / CloudWatch / ELK).

* **Health Check:** Support for liveness / readiness probes (`/healthz`, `/readyz`).

* **Dashboard:** Include a sample Grafana dashboard definition (JSON).

### 4.3 Scope of v1.0

Defined in [ADR-0007](adr/0007-v1-feature-scope.md): immediate delivery, device registration, bounce handling, multiple apps per installation, scheduled delivery, delivery analytics, users and devices with tags and segments, open tracking through the client API, rich push, the admin UI, and Helm with signed images and an SBOM. Huawei (HMS) and Web Push are not in v1.0.

### 4.4 Security Requirements

* **Admin sign-in:** server-side sessions, local users with TOTP, optional OIDC, and roles (Admin, Sender, Viewer) assigned per app ([ADR-0008](adr/0008-admin-authentication-and-authorization.md)).
* **Keys:** secret server API keys with scopes, and public client keys that can only register devices and report events, served on a separate client port ([ADR-0011](adr/0011-client-keys-and-client-port.md)).
* **Stored secrets:** encrypted with envelope encryption; the key-encryption key is a master key or a KMS through the Kubernetes KMS v2 plugin protocol ([ADR-0010](adr/0010-secret-encryption-and-kms-plugins.md)).
* **Audit:** audit events are written to the structured log ([ADR-0009](adr/0009-audit-events-to-stdout.md)).
* **Release criteria:** see [ADR-0012](adr/0012-v1-quality-bar-and-test-strategy.md).

## 5. Open Questions

The following items are undecided and will be updated in this document once decided.

| # | Topic | Current status |
|---|-------|----------------|
| 1 | License | **Decided:** Apache License 2.0 (changed from BSL 1.1 on 2026-11-12) |
| 2 | Monetization (corporate sponsorship, paid deployment help, possible open-core edition) | Not yet defined; revisit once real-world usage is established |
| 3 | Web admin console (Dashboard UI) | **Decided:** included in v1.0 ([ADR-0004](adr/0004-admin-ui-in-ga-all-features-open-source.md)) |
| 4 | Contribution terms | **Decided:** DCO instead of a CLA ([ADR-0013](adr/0013-dco-instead-of-cla.md)) |
| 5 | Trademark protection for the project name | Under consideration |

## 6. Change Log

| Date | Change |
|------|--------|
| 2026-10-27 | Initial draft |
| 2026-10-28 | Adopt BSL 1.1 as license; add CLA and CONTRIBUTING |
| 2026-10-29 | Switch to license-type CLA with successors, moral rights and governing law clauses and CLA Assistant; add open questions |
| 2026-11-05 | Add architecture draft (ARCHITECTURE.md) and WIP README |
| 2026-11-06 | Add observability plan to README and ARCHITECTURE.md |
| 2026-11-08 | Raise the Small Business revenue limit to US$10M; state that replies to issues and questions are not guaranteed; move operating and commercial notes out of this document |
| 2026-11-09 | Add project continuity commitments (README) and related open question |
| 2026-11-12 | Change the license from BSL 1.1 to Apache License 2.0; keep the CLA; simplify the continuity commitments |
| 2026-11-13 | Replace the "Demand Validation First" approach with building a minimal version first; drop the survey |
| 2026-11-18 | Add ADRs and the roadmap; first release is v1.0.0 (GA); add the admin UI, v1.0 scope, and security requirements; replace the CLA with the DCO |
