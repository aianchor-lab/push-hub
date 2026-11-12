# Architecture

> **Status:** Draft
> **Last updated:** 2026-11-06
>
> This document is a working draft of the architecture direction for push-hub. No production code exists yet, and the design may change based on the results of demand validation. Undecided items are marked **TBD** and tracked in [Open Questions](#9-open-questions). For product goals and licensing, see the [PRD](PRD.md).

## 1. Design Principles

* **Cloud-agnostic:** Customers run on AWS, GCP, Azure, or on-premises, so push-hub depends only on open source components that are available everywhere. Cloud-specific services such as SQS or DynamoDB are not used in the core.

* **PostgreSQL is the only required dependency:** Managed PostgreSQL is available on every major cloud (RDS / Aurora, Cloud SQL, Azure Database for PostgreSQL).

* **12-factor:** Configuration through environment variables, logs to stdout, and no state inside containers.

* **Reference environment:** Testing starts on **AWS (EKS + RDS)**, but the core contains no AWS-specific code.

* **No outbound connections except APNs / FCM:** push-hub sends no telemetry and performs no online license checks (see [Section 7](#7-licensing-in-the-product)).

## 2. Components

### 2.1 Single Binary, Multiple Roles

push-hub is built as a single binary. Its role is selected at startup:

| Role | Responsibility |
|------|----------------|
| `api` | REST JSON API: device registration, send requests, events |
| `worker` | Picks up delivery jobs and sends them to APNs / FCM |
| `scheduler` | Enqueues scheduled deliveries when they are due |
| `all` | Runs all roles in one process (trial and small deployments) |

The same container image is used for every role. On Kubernetes, each role runs as its own Deployment.

### 2.2 Language

**Go is recommended:** it produces a self-contained binary, has strong support for HTTP/2 (required by APNs) and concurrency, and has a small memory footprint.

### 2.3 Queue

* The initial queue is built on PostgreSQL with `SELECT ... FOR UPDATE SKIP LOCKED`.
* Each job sends a batch of 500–1,000 tokens, which is enough for hundreds of millions of notifications per month.
* The queue is behind an interface so that Redis or NATS can be added later.

### 2.4 Secrets

* APNs keys (`.p8`) and FCM service account JSON are passed through environment variables or mounted files.
* Encryption with a KMS is optional and can be added later through a pluggable interface.

### 2.5 Observability

* **Admin port:** `/metrics`, `/healthz`, and `/readyz` are served on an admin port separate from the API port, so they are not exposed with the public API. Every role (`api`, `worker`, `scheduler`, `all`) uses the same admin port. The port number is TBD.
* **Metrics:** Prometheus-format endpoint at `/metrics`, scraped by the customer's own Prometheus (or a compatible agent).
* **Logs:** structured JSON logs written to stdout (12-factor). Collection is left to the customer's log collector (Datadog, CloudWatch, ELK, etc.).
* **Health checks:**

  | Endpoint | Probe | Returns OK when |
  |----------|-------|-----------------|
  | `/healthz` | Liveness | The process is running |
  | `/readyz` | Readiness | PostgreSQL is reachable and database migrations are complete |

  The same checks apply to every role.
* **Dashboard:** a sample Grafana dashboard definition (JSON) is included.
* **No push from push-hub:** push-hub never sends metrics or logs anywhere; the customer's tools pull or collect them.

## 3. Feature Mapping

| Feature | Approach | Notes |
|---------|----------|-------|
| Immediate delivery | The API enqueues a job; workers send it to APNs (HTTP/2) or FCM HTTP v1, with rate limiting per destination | Batch processing; retries with exponential backoff |
| Bounce handling | Record APNs `410` / `BadDeviceToken` and FCM `UNREGISTERED`, and deactivate those tokens automatically | Handled entirely on the server side |
| **Open tracking** | **APNs and FCM do not report opens.** The app must report them through `POST /v1/events` | Requires an SDK or a published API specification |
| Analytics storage | PostgreSQL tables partitioned by time, plus Prometheus metrics | ClickHouse is a future option |
| Recipient management and search | `users` linked to `devices`; attributes stored as JSONB with GIN indexes; segments built from tags | "Users" here means notification recipients |
| Scheduled delivery | Indexed `scheduled_at`; the scheduler picks up due jobs, with an advisory lock so that only one instance does so | Per-recipient time zone delivery is deferred |
| Rich push | Image URLs, action buttons, and `mutable-content` are passed through to APNs / FCM | iOS requires a Notification Service Extension. Images are hosted in the customer's own S3-compatible storage |

## 4. Roadmap Priority

Demand validation comes first. Features are built in this order:

1. **MVP:** immediate delivery, device registration, bounce handling
2. Scheduled delivery and delivery analytics
3. Open tracking, segment search, rich push

## 5. Distribution

| Artifact | Location | Role |
|----------|----------|------|
| **Container image** | ghcr.io / Docker Hub | **Primary.** Every deployment method uses it |
| **Helm chart** | ghcr.io (OCI) | **Primary.** Production on Kubernetes |
| **docker-compose.yml** | Repository / README | **Primary.** Trial and small deployments |
| Standalone binary | GitHub Releases | Secondary. For VMs where containers cannot be used |
| deb / rpm (Omnibus-style) | — | **Not provided** |

* Binaries and images are built in a single release process (e.g. with GoReleaser).
* **Production:** the Helm chart expects PostgreSQL to run outside the cluster, preferably as a managed service.
* **Air-gapped environments:** customers can copy images to their own registry (ECR / Artifact Registry / ACR / Harbor). A procedure using `docker save` tar archives is also provided.
* **Supply chain:** images are signed with cosign and shipped with an SBOM.
* **Version pinning:** images are tagged `vX.Y.Z` in addition to `latest`, and can be pinned by digest.
* There is a single image, distributed to everyone under the Apache License 2.0.

## 6. Admin UI (Future)

The initial release has no UI (see [PRD §4.1](PRD.md#41-core-architecture)). If a UI is built later:

* **Embedded in the binary:** a static SPA (e.g. React / Vite) is embedded with `go:embed` and served at `/ui`, as Grafana, Prometheus, and Gitea do. The API and UI versions never drift apart.
* **Public API only:** the official UI uses only the public API. There are no UI-only private endpoints, so users can build their own UI on the same API.
* **Can be disabled:** `PUSHHUB_UI_ENABLED=false` turns the UI off.
* **Source layout:** UI code lives in `web/` (or a separate repository) and is embedded at release time. A UI-only image may be published if needed.

Requirements for custom UIs, which must be designed **before** the first API release because they are hard to change later:

* An OpenAPI specification and generated SDKs (e.g. TypeScript).
* **Browser-safe authentication:** server-to-server API keys must never be used in browsers. Options are short-lived tokens issued through OIDC, or a BFF (backend for frontend). **TBD.**
* CORS configuration and permission scopes.

## 7. Licensing in the Product

push-hub is licensed under the Apache License 2.0 (see [LICENSE](../LICENSE)). The open source product contains no license checks of any kind.

If an open-core commercial edition is introduced later (see [PRD §3](PRD.md#3-business-model--licensing)), it follows these rules:

* **No online license checks.** Phoning home would contradict the data governance promise and would not work in air-gapped environments.
* **No blocking or nagging.** Commercial terms are enforced by contract, not by the software.
* **The open source core stays complete.** Features that may become paid (SSO, RBAC, audit logs, data retention settings) are structured so that they can live in a separate `ee/` directory under a separate license. Delivery itself is never moved out of the open source core.

### 7.1 Signed License Keys

License keys are only needed if paid features are introduced. They are verified offline with **Ed25519** public key signatures:

* The Licensor signs keys with a private key. The binary embeds only the public keys.
* Shared-secret schemes (HMAC) are not used, because anyone with the embedded secret could issue keys.
* Format: `base64url(payload).base64url(signature)`. PASETO v4.public or EdDSA JWTs are alternatives.

Example payload:

```json
{
  "v": 1,
  "kid": "2026-01",
  "license_id": "lic_...",
  "customer": "Example Health Inc.",
  "plan": "enterprise",
  "features": ["sso", "audit_log", "rbac"],
  "limits": { "messages_per_month": 100000000 },
  "issued_at": "2026-11-01T00:00:00Z",
  "expires_at": "2027-11-01T00:00:00Z"
}
```

Verification steps:

1. Split the key at `.` and base64url-decode both parts. Reject the key if either step fails.
2. Parse the payload and look up the public key by `kid`. Reject unknown `kid` values.
3. Verify the Ed25519 signature over the raw payload bytes.
4. Check `expires_at` against the current time.

| Topic | Decision |
|-------|----------|
| Expiry | **Delivery never stops.** Only paid features are disabled, after a grace period of about 30 days with warnings |
| Key rotation | Each key carries a `kid`; multiple public keys can be embedded |
| Revocation | Not possible in real time without network access. Expiry matches the contract term, and a new key is issued at each renewal |
| Private key storage | Offline machine, password manager, YubiKey, or KMS, with backups |
| Delivery to the product | `PUSHHUB_LICENSE_KEY` environment variable, or a Kubernetes Secret mounted as a file |

**Known limit:** because the source is available, the check can be removed. License keys serve as proof of proper use and as feature switches; compliance is ultimately enforced by contract.

### 7.2 Automated Key Issuance (Future)

After commercial sales begin, keys may be issued automatically after payment:

```
Stripe Checkout / Invoicing
  → Webhook (checkout.session.completed / invoice.paid)
  → License server (serverless)
      verify webhook signature → deduplicate by event ID → build payload
      → sign with KMS → record issuance → send by email
```

* Annual renewals are handled the same way: `invoice.paid` triggers a new key.
* Keys are pasted into the product manually by default. Online activation is opt-in and exchanges only the license key, never notification data.
* The capabilities of the services named here (Stripe, KMS providers, Keygen, etc.) have not been verified and must be checked before adoption.

## 8. Prior Art

> Based on web search results and LICENSE files on GitHub. The official documentation sites could not be accessed directly, so some details, especially for GitLab, rely on excerpts and may be inaccurate.

### 8.1 Langfuse

* **Deployment:** Docker Compose for trials (documented as having no high availability, scaling, or backup features). Helm, or Terraform for AWS / Azure / GCP, for production.
* **v3 components:** Web (UI and API), Worker, PostgreSQL, ClickHouse (analytics), Redis (queue), S3-compatible storage. Ingested data is written to S3 by Web, Redis queues only a reference, and Worker loads it into ClickHouse.
* **v2 used only Web and PostgreSQL.** Components were added as scale grew.
* The Helm chart deploys data stores by default, but can be switched to existing databases.

### 8.2 GitLab Self-Managed

* **The Linux package (Omnibus) is the main method**, bundling PostgreSQL, Redis, and Sidekiq. Docker, Helm, Operator, GitLab Environment Toolkit (Terraform + Ansible), and source installs are also available.
* **For production on Kubernetes, PostgreSQL, Redis, Gitaly, and object storage run outside the cluster** (Cloud Native Hybrid).
* Recommended configurations are published per scale (Reference Architectures, e.g. 1,000 users / 20 RPS, 5,000 users / 100 RPS).

### 8.3 What push-hub Adopts

* Compose for trials, Helm for production, with managed PostgreSQL outside the cluster.
* Separate API and worker roles (like Langfuse web / worker and GitLab Rails / Sidekiq).
* **Start with PostgreSQL only** (like Langfuse v2), with room to add ClickHouse and others later.
* Publish recommended configurations per scale, in notifications per month.
* **No Omnibus-style all-in-one package.** The maintenance cost is too high.

## 9. Open Questions

| # | Topic | Current status |
|---|-------|----------------|
| 1 | Browser-safe authentication for the API (OIDC vs. BFF) | TBD. Must be decided before the first API release |
| 2 | KMS support for Ed25519 signing | TBD. ECDSA P-256 is a fallback if needed |
| 3 | Adding ClickHouse for analytics | Future option, depending on scale |
| 4 | Recommended configurations per scale | TBD. To be measured on the reference environment |
| 5 | Open tracking: SDK vs. published API only | TBD |
| 6 | Admin port number, metric names and labels | TBD |
