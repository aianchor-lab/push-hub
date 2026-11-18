# Roadmap

> **Last updated:** 2026-11-18

push-hub is built toward a single first release, **v1.0.0 (GA)**, with no alpha or beta releases and no target date ([ADR-0003](adr/0003-first-release-is-ga.md)). Each phase ends when its exit criterion is met. The v1.0 scope is defined in [ADR-0007](adr/0007-v1-feature-scope.md), and the release criteria in [ADR-0012](adr/0012-v1-quality-bar-and-test-strategy.md).

Phases are worked in order, but later phases may start early when they do not depend on unfinished work. Progress is tracked in GitHub milestones, one per phase.

| Phase | Contents | Exit criterion |
|-------|----------|----------------|
| **P0 Documents** | Architecture Decision Records; PRD, ARCHITECTURE, and README updated to the decisions; CLA replaced by the DCO | Public documents agree with the accepted ADRs |
| **P1 AI development harness** | Context structure for AI agents, tool-neutral agent instructions and skills, a single command entry point, a reproducible development environment, guardrails in git and CI | An issue handed to an AI agent comes back as a pull request with passing checks |
| **P2 Foundation** | Go project layout, CI, test infrastructure (simulated APNs and FCM, PostgreSQL integration tests), threat model, OpenAPI specification written first, data model (apps, users, devices, role assignments) | An empty API runs and is tested in CI |
| **P3 Delivery core** | Device registration with server API keys and client keys, immediate delivery, workers for APNs and FCM, retries, duplicate prevention, automatic deactivation of bounced tokens, `/metrics`, `/healthz`, `/readyz`, structured logs and audit events | A notification reaches a real device, and a bounced token is deactivated |
| **P4 Delivery features** | Scheduled delivery, tags and segments, delivery analytics, open events with JWT identity verification, rich push, rate limits on the client port | Every v1.0 delivery feature works through the API |
| **P5 Admin UI** | Sessions (BFF), local users with TOTP, OIDC, per-app roles, encrypted secrets with master key and KMS v2 plugins, screens for sending, history, analytics, recipients, and settings, branding and themes, the AI skill for custom screens | A team can do its daily sending work in the UI alone |
| **P6 Distribution and operations** | GoReleaser, container images, cosign, SBOM, Helm chart, Compose file, air-gapped procedure, operations documentation | Someone else can install push-hub by following the documentation |
| **P7 Release verification** | APNs and FCM rate limits checked; load test (10 million devices within 30 minutes); simulation and failure tests; 7-day endurance run; end-to-end tests against real APNs and FCM; AI-assisted adversarial assessment; KMS plugin check; test report | Every criterion in [ADR-0012](adr/0012-v1-quality-bar-and-test-strategy.md) is met |
| **P8 Release and announcement** | v1.0.0 release, landing page rebuilt for a working product, announcement in English first and Japanese a few days later | v1.0.0 is published and announced |

## P1 in Detail

The harness is designed as a context structure, not only as configuration files:

* **Layers of context:** a short root `AGENTS.md` (purpose, map, commands, hard rules) → [docs/README.md](README.md) (index) → specifications (OpenAPI, ADRs, ARCHITECTURE, PRD) → `AGENTS.md` files in subdirectories → skills for procedures. Agents read only what the task needs.
* **One place per fact:** documents link instead of copying.
* **Precedence on conflict:** OpenAPI and ADRs, then ARCHITECTURE, then PRD, then README. Code follows the specifications. Agents report conflicts instead of resolving them silently.
* **Tool-neutral:** `AGENTS.md` is the source; `CLAUDE.md` and other tool files only import it. Skills follow the Agent Skills format in one directory, linked from each tool's directory.
* **Enforcement in git and CI, not in AI tools:** formatting, linting, tests, DCO, secret scanning (gitleaks), and link checks run for every pull request, whoever wrote it. AI agents open pull requests; people merge them.
* **One entry point:** `make test`, `make e2e`, `make lint`, `make dev`, with fast local feedback.
* **Issues sized for agents:** templates with acceptance criteria and related ADRs.

## Test Techniques

Candidates from [ADR-0012](adr/0012-v1-quality-bar-and-test-strategy.md), set up in P2 and P7: deterministic simulation testing, property-based testing, contract tests from OpenAPI, Playwright with visual regression, simulated APNs and FCM, end-to-end tests on the iOS Simulator and Android Emulator, mutation testing, and exploratory testing by AI agents.

## After v1.0

Decided by user feedback. Candidates: Web Push, Huawei (HMS), SAML, an audit log viewer, more KMS integrations, official SDKs, ClickHouse for analytics, and separate scaling of roles.
