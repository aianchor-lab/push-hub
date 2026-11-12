# Product Requirements Document (PRD)

> **Status:** Draft
> **Last updated:** 2026-11-12
>
> This document is a working draft. Its contents, including licensing and scope, may change based on the results of demand validation. Undecided items are marked **TBD** and tracked in [Open Questions](#5-open-questions).

## 1. Project Overview

* **Project name:** `push-hub` (`aianchor-lab/push-hub`)

* **Concept:** A self-hosted push notification delivery engine (middleware) for enterprises.

* **Vision:** Provide a robust, lightweight push delivery platform that can be deployed with a single command inside a company's own infrastructure (VPC / on-premises), for companies struggling with the high cost (pay-per-use pricing) and the data governance / regional constraints of SaaS-based push delivery services.

* **Approach:** Strictly follow a **"Demand Validation First"** approach: evaluate demand before seriously writing production code.

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

* **Model:** Open Source first. Adoption and real-world usage come before revenue. Revenue, if any, comes later from corporate sponsorship and paid help with deployments. An open-core commercial edition may be considered once demand is proven.

* **License:** **Apache License 2.0** — see [LICENSE](../LICENSE) and [NOTICE](../NOTICE). Free for any use, including commercial production use.

  * **Copyright notice:** `Copyright 2026 Atsushi Inoue (https://github.com/aianchor-lab)`.

  * **Why Apache 2.0:** many enterprise legal teams pre-approve OSI-approved licenses, while a source-available license requires a case-by-case review that slows adoption. Apache 2.0 also includes an explicit patent grant.

  * **Hosted offerings by third parties are allowed.** push-hub's value is running inside the user's own infrastructure, so a third-party SaaS serves a different market. The project name is protected by trademark rather than by the license.

  * **Alternatives considered:** BSL 1.1 was adopted on 2026-10-28 and replaced on 2026-11-12, because it slows enterprise adoption, which is the priority at this stage. AGPLv3 was not adopted because many enterprises do not allow it. Licenses that forbid competing services (FSL, Elastic License) are not Open Source and have the same adoption problem as the BSL.

* **CLA (Contributor License Agreement):** Required for all external contributors, so that contributions can also be included in a possible future commercial edition. See [CLA.md](../CLA.md) and [CONTRIBUTING.md](../CONTRIBUTING.md).

  * **License-type CLA** (based on the Apache ICLA): contributors keep their copyright and grant a non-exclusive license.

  * The CLA allows sublicensing on any terms, including commercial licenses.

  * The license extends to the Project Owner's **successors and assigns**.

  * Contributors agree not to exercise their **moral rights** (Articles 18–20 of the Japanese Copyright Act), so contributions can be modified and included in commercial versions.

  * Governing law is Japan, and the Tokyo District Court has exclusive jurisdiction.

  * The CLA is versioned. External PRs are not accepted until a version reviewed by legal counsel has been published.

  * Signatures are collected per PR with **CLA Assistant**. It must be installed before the first external PR is accepted, and every PR goes through it, including small ones. Code posted in issues is not incorporated.

## 4. Technical Requirements

See [ARCHITECTURE.md](ARCHITECTURE.md) for the architecture direction that implements these requirements.

### 4.1 Core Architecture

* **Deployment:** Single-command startup via Docker / `docker-compose.yml`.

* **Interface:** RESTful JSON API (application server ⇔ this engine ⇔ APNs / FCM).

* **UI policy (web admin console, TBD):**

  * In the initial phase, focus solely on providing the core features (API and delivery middleware); no UI will be built.

  * Whether a web admin console (Dashboard UI) is needed will be considered in the future, based on user feedback and needs after the initial release.

  * Operational monitoring is based on integration with standard external tools (Prometheus / Grafana, etc.).

### 4.2 Observability Requirements

* **Metrics:** Provide a Prometheus-format endpoint (`/metrics`).

* **Logs:** Structured JSON logs written to stdout (compatible with collection by Datadog / CloudWatch / ELK).

* **Health Check:** Support for liveness / readiness probes (`/healthz`, `/readyz`).

* **Dashboard:** Include a sample Grafana dashboard definition (JSON).

## 5. Open Questions

The following items are undecided and will be updated in this document once decided.

| # | Topic | Current status |
|---|-------|----------------|
| 1 | License | **Decided:** Apache License 2.0 (changed from BSL 1.1 on 2026-11-12) |
| 2 | Monetization (corporate sponsorship, paid deployment help, possible open-core edition) | Not yet defined; revisit once real-world usage is established |
| 3 | Web admin console (Dashboard UI) | To be decided based on feedback after the initial release |
| 4 | Corporate CLA | Not yet drafted; contributions on behalf of companies are not accepted until it exists |
| 5 | Legal review of the CLA | Required before accepting external PRs |
| 6 | Trademark protection for the project name | Under consideration |

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
