# ADR-0001: License push-hub under the Apache License 2.0

* **Status:** Accepted
* **Date:** 2026-11-12 (merged into `main` on 2026-11-17 in #42)

## Context

push-hub was first published under the Business Source License 1.1 (2026-10-28). The target users are enterprises in regulated sectors. Their legal teams usually pre-approve OSI-approved licenses, while a source-available license needs a case-by-case review before adoption. At this stage adoption matters more than revenue, and there is no code and no external contributor yet, so a license change costs the least now.

## Decision

* License push-hub under the **Apache License 2.0**, with a NOTICE file.
* Allow hosted offerings by third parties. The project name is protected by trademark, not by the license.
* Revenue, if any, comes later from sponsorship and help with deployments. An open-core edition may be considered once demand is proven ([PRD §3](../PRD.md#3-business-model--licensing)).

## Alternatives Considered

* **Keep BSL 1.1:** slows enterprise adoption, which is the priority.
* **AGPLv3:** many enterprises do not allow it.
* **FSL / Elastic License:** not Open Source, with the same adoption problem as the BSL.
* **MIT:** no explicit patent grant.

## Consequences

* Anyone can use push-hub in production, including commercially, and can fork it.
* Versions released under Apache 2.0 stay under it. Moving to a more restrictive license later is the hard direction.
* The CLA is no longer needed for commercial use of contributions (see [ADR-0013](0013-dco-instead-of-cla.md)).

## Evidence

* [LICENSE](../../LICENSE), [NOTICE](../../NOTICE)
* Pull requests #40 and #42
