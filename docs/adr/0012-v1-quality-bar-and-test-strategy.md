# ADR-0012: Quality Bar and Test Strategy for v1.0

* **Status:** Accepted
* **Date:** 2026-11-18

## Context

v1.0 is the first release and promises API compatibility ([ADR-0003](0003-first-release-is-ga.md)), so it has to be tested before anyone outside has used it. Manual testing on every change does not scale, and most development is done with AI coding agents.

## Decision

### Release criteria

| Area | Criterion |
|------|-----------|
| Load | A broadcast to **10 million devices completes within 30 minutes** (about 5,600 notifications per second) on the reference environment (EKS + RDS), against simulated APNs and FCM. Results are published as recommended configurations per scale |
| Resilience | No duplicate or lost sends when a worker is killed mid-batch; automatic recovery after a database outage; graceful shutdown |
| Real devices | Immediate, scheduled, segment, and rich sends reach real iOS and Android devices; bounced tokens are deactivated |
| Endurance | Continuous operation on the reference environment (for example 7 days) without memory growth or rising error rates |
| Security | A written threat model; OWASP ASVS Level 2 self-assessment of the UI; dependency scanning (govulncheck, Trivy); API fuzzing; an **AI-assisted adversarial assessment** of the reference environment |
| KMS | Encryption works with at least the AWS KMS plugin ([ADR-0010](0010-secret-encryption-and-kms-plugins.md)) |
| Distribution | GoReleaser binaries and images, cosign signatures, SBOM, Helm chart, Compose file, air-gapped procedure |
| Documentation | Installation, configuration reference, OpenAPI, operations (backup and restore, upgrades, key rotation), the AI skill, the compatibility promise, supported PostgreSQL, Kubernetes, and browser versions |
| Report | A test report for each release, generated from the pipeline |

* There is **no third-party penetration test** for v1.0. Public documents describe the security work as an AI-assisted self-assessment, never as a penetration test.
* Before development starts, the APNs and FCM rate limits are checked against the load target in the official documentation.

### Test layers (candidates)

| When | What |
|------|------|
| Every pull request (minutes) | Unit tests, property-based tests, contract tests generated from OpenAPI, end-to-end tests against simulated APNs and FCM, AI code and security review |
| Nightly | Long deterministic simulation runs, Playwright end-to-end and visual regression tests |
| Each release | Load test, end-to-end tests against real APNs and FCM, AI-assisted adversarial assessment, test report |

Candidate techniques: deterministic simulation testing (time, network, and randomness replaced by controllable fakes, reproducible by seed), property-based testing, contract testing from OpenAPI (for example Schemathesis), Playwright with visual regression, simulated APNs and FCM servers, end-to-end tests on the iOS Simulator and Android Emulator, mutation testing, and exploratory testing by AI agents. Which techniques are adopted is decided during development, with an ADR for each.

The goal is that the only manual step before a release is a visual check of notifications on real devices.

## Alternatives Considered

* **Third-party penetration test before v1.0:** reconsidered when users require one.
* **Manual testing per release:** does not scale.

## Consequences

* Building the test infrastructure is a phase of its own in the [Roadmap](../ROADMAP.md).
* Whether the iOS Simulator can receive remote notifications in CI is **not yet verified**.

## Evidence

* None yet. The rate limits and simulator support are verified before development.
