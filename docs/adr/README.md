# Architecture Decision Records

This directory records the significant decisions about push-hub and why they were made. The [PRD](../PRD.md) and [ARCHITECTURE](../ARCHITECTURE.md) describe the current state; these records keep the reasoning, the alternatives, and the evidence behind it.

## Rules

* **One decision per record.** File names are `NNNN-short-title.md`, numbered in order.
* **Accepted records are not rewritten.** To change a decision, add a new record and set the old one to `Superseded by ADR-NNNN`. Typos and broken links may be fixed.
* **Same pull request as the change.** A record is added in the pull request that applies the decision to the code or documents.
* **Evidence is required.** List the sources the decision relied on (official documentation, specifications, source files, court rulings). Mark anything unverified as such.
* **One place per fact.** Other documents link to a record instead of copying its reasoning.

Use [template.md](template.md) for new records.

## Status Values

| Status | Meaning |
|--------|---------|
| Proposed | Under discussion, not yet in effect |
| Accepted | In effect |
| Superseded by ADR-NNNN | Replaced by a later record |
| Deprecated | No longer applies, with no replacement |

## Index

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| [0001](0001-apache-2-0-license.md) | License push-hub under the Apache License 2.0 | Accepted | 2026-11-12 |
| [0002](0002-build-first.md) | Build a working product before promoting it | Accepted | 2026-11-13 |
| [0003](0003-first-release-is-ga.md) | Make the first release v1.0.0 (GA), with no target date | Accepted | 2026-11-17 |
| [0004](0004-admin-ui-in-ga-all-features-open-source.md) | Ship an admin UI in v1.0 and keep every feature in the open source edition | Accepted | 2026-11-17 |
| [0005](0005-no-build-admin-ui.md) | Build the admin UI without a build step | Accepted | 2026-11-17 |
| [0006](0006-ui-customization-and-ai-skill.md) | Limit built-in UI customization to branding, and ship an AI skill for custom screens | Accepted | 2026-11-17 |
| [0007](0007-v1-feature-scope.md) | Feature scope of v1.0 | Accepted | 2026-11-17 |
| [0008](0008-admin-authentication-and-authorization.md) | Admin authentication and authorization | Accepted | 2026-11-17 |
| [0009](0009-audit-events-to-stdout.md) | Write audit events to the structured log | Accepted | 2026-11-17 |
| [0010](0010-secret-encryption-and-kms-plugins.md) | Encrypt stored secrets, with KMS through the Kubernetes KMS v2 plugin protocol | Accepted | 2026-11-17 |
| [0011](0011-client-keys-and-client-port.md) | Public client keys on a separate client port, without official SDKs | Accepted | 2026-11-18 |
| [0012](0012-v1-quality-bar-and-test-strategy.md) | Quality bar and test strategy for v1.0 | Accepted | 2026-11-18 |
| [0013](0013-dco-instead-of-cla.md) | Use the Developer Certificate of Origin instead of a CLA | Accepted | 2026-11-18 |
