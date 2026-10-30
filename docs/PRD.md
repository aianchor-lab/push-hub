# Product Requirements Document (PRD)

> **Status:** Draft
> **Last updated:** 2026-10-30
>
> This document is a working draft. Its contents, including licensing and scope, may change based on the results of demand validation. Undecided items are marked **TBD** and tracked in [Open Questions](#5-open-questions).

## 1. Project Overview

* **Project name:** `push-hub` (`aianchor-lab/push-hub`)

* **Concept:** A self-hosted push notification delivery engine (middleware) for enterprises.

* **Vision:** Provide a robust, lightweight push delivery platform that can be deployed with a single command inside a company's own infrastructure (VPC / on-premises), for companies struggling with the high cost (pay-per-use pricing) and the data governance / regional constraints of SaaS-based push delivery services.

* **Team & constraints:**

  * Solo development (side project, worked on during weekends).

  * Zero capacity for support / maintenance operations (no SLA; relies on community contributions).

  * Strictly follow a **"Demand Validation First"** approach: evaluate demand before seriously writing production code.

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

* **Model:** Dual Licensing / Source Available

  * **Personal / PoC / non-commercial use:** Free of charge.

  * **Commercial production use:** Requires a paid license.

* **License:** **BSL 1.1 (Business Source License)** — see [LICENSE](../LICENSE). push-hub is described as **source-available**, not Open Source. The license text is used unmodified except for its parameters.

  * **Licensor / copyright notice:** For now the Licensor is an individual, since no legal entity exists yet: `Copyright (c) 2026 Atsushi Inoue (https://github.com/aianchor-lab)`.

  * **Additional Use Grant:** Production use is free for Small Businesses (fewer than 100 employees and contractors, and less than US$1M in annual revenue, including affiliates) and for Non-Commercial Use. Offering push-hub to third parties as a hosted or managed service is not permitted.

  * **Change Date / Change License:** Each tagged release converts to the **Apache License 2.0** three years after its release date.

  * AGPLv3 was also considered but not adopted: since push-hub runs inside the customer's own infrastructure, AGPL's network clause rarely applies, and it could not tie commercial production use to a paid license.

* **CLA (Contributor License Agreement):** Required for all external contributors so that contributions can be included in commercial licenses. See [CLA.md](../CLA.md) and [CONTRIBUTING.md](../CONTRIBUTING.md).

  * **License-type CLA** (based on the Apache ICLA): contributors keep their copyright and grant a non-exclusive license.

  * The CLA allows sublicensing on any terms, including commercial licenses.

  * The license extends to the Project Owner's **successors and assigns**, which covers future incorporation or acquisition.

  * Contributors agree not to exercise their **moral rights** (Articles 18–20 of the Japanese Copyright Act), so contributions can be modified and included in commercial versions.

  * The CLA notes that contributions become available under the Change License after the Change Date. This is information only and places no obligation on the Project Owner.

  * Governing law is Japan, and the Tokyo District Court has exclusive jurisdiction.

  * The CLA is versioned. External PRs are not accepted until a version reviewed by legal counsel has been published.

  * Signatures are collected per PR with **CLA Assistant**. It must be installed before the first external PR is accepted, and every PR goes through it, including small ones. Code posted in issues is not incorporated.

* **Future procedures:**

  * **Incorporation:** Changing the copyright notice is not enough. A copyright assignment agreement from the individual to the company is required. It must specify the assigned works, explicitly include the rights under Articles 27 and 28 of the Japanese Copyright Act, and include a covenant not to exercise moral rights. The agreement must also transfer the individual's contractual positions: the contributor CLAs, any commercial license agreements, and the Licensor position for released versions. The official repository designation should also be updated. Tax implications are to be confirmed with a tax accountant.

  * **Acquisition:** Thanks to the successors-and-assigns clause, contributor consent is not required. Keeping complete CLA signature records is essential. Because the CLA is a non-exclusive license, a buyer may ask to switch to assignment-type agreements.

## 4. Technical Requirements

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
| 1 | License | **Decided:** BSL 1.1 (Change License: Apache 2.0 after 3 years) |
| 2 | Commercial license pricing and terms | Key terms outlined in [commercial-license-terms.md](commercial-license-terms.md); pricing not yet defined |
| 3 | Web admin console (Dashboard UI) | To be decided based on feedback after the initial release |
| 4 | Corporate CLA | Not yet drafted; contributions on behalf of companies are not accepted until it exists |
| 5 | Timing of commercial license sales | **Direction:** small deals may be sold as an individual, with a liability cap (fees paid in the prior 12 months), exclusion of indirect damages, no SLA, and liability insurance. Incorporate before signing large or high-risk deals (see [incorporation triggers](commercial-license-terms.md#5-incorporation-triggers)). The contract value threshold is TBD |
| 6 | Legal review of LICENSE parameters and CLA | Required before accepting external PRs |

## 6. Change Log

| Date | Change |
|------|--------|
| 2026-10-27 | Initial draft |
| 2026-10-28 | Adopt BSL 1.1 as license; add CLA and CONTRIBUTING |
| 2026-10-29 | Set individual Licensor; switch to license-type CLA with successors, moral rights and governing law clauses and CLA Assistant; add future procedures and open questions |
| 2026-10-30 | Add commercial license terms outline; set direction for liability limitation and incorporation timing |
