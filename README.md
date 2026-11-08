# push-hub

A self-hosted push notification delivery engine for enterprises. Deploy it inside your own VPC or on-premises, and send APNs / FCM notifications without handing your data to a SaaS provider.

> **Status: Work in progress — demand validation.**
> push-hub is in the demand validation phase. There is no production code yet, and the scope described here may change. push-hub is **source-available** under the [Business Source License 1.1](LICENSE), not Open Source.

## The Problem

Push notification SaaS is easy to start with, but becomes hard to justify as you grow:

1. **Data sovereignty and compliance:** customer behavior logs, notification bodies, and device tokens are stored on an external provider's servers, often in another country.
2. **The "SaaS Tax":** pay-per-use pricing grows with delivery volume, and becomes a major cost at millions to hundreds of millions of notifications per month.
3. **Vendor lock-in:** your delivery pipeline, device data, and analytics depend on a single provider.

## Who Is This For

* **B2B enterprises and security-focused startups** in healthcare, finance, SaaS, government, and similar sectors that cannot send customer data or push tokens to external SaaS providers.
* **Development and DevOps teams** that deliver millions to hundreds of millions of push notifications per month and want predictable costs.

## Data Governance by Design

push-hub runs entirely inside your infrastructure:

* **Your data stays in your database.** Device tokens, recipient attributes, notification content, delivery logs, and analytics are stored in your own PostgreSQL.
* **The only outbound traffic goes to APNs and FCM.** push-hub sends no telemetry, usage statistics, or crash reports anywhere.
* **No license server.** push-hub never phones home to check a license, so it also works in air-gapped environments.
* **Runs on any cloud or on-premises.** The only required dependency is PostgreSQL, which is available as a managed service on AWS, GCP, and Azure. No cloud-specific services are used.
* **Standard observability.** Metrics, logs, and health checks plug into the tools you already run (Prometheus, Grafana, Datadog, CloudWatch, ELK).

> **What push-hub cannot change:** to deliver a notification, its content and the device token must be sent to Apple (APNs) or Google (FCM). This is true for every push provider, including SaaS. push-hub removes the additional third party in between, but it does not remove Apple or Google from the path. Keep sensitive data out of notification bodies where possible.

## Architecture

```mermaid
flowchart LR
    subgraph vpc["Your VPC / on-premises"]
        app["Your application server"]
        subgraph ph["push-hub (single binary)"]
            api["api"]
            scheduler["scheduler"]
            worker["worker"]
        end
        db[("PostgreSQL<br/>tokens · queue · logs · analytics")]
        obs["Prometheus / Grafana<br/>log collector"]
    end
    subgraph vendors["Apple / Google"]
        apns["APNs"]
        fcm["FCM"]
    end
    devices["End-user devices"]

    app -- "REST JSON API" --> api
    api --> db
    scheduler --> db
    db --> worker
    worker -- "APNs provider API (HTTP/2)" --> apns
    worker -- "FCM HTTP v1 API" --> fcm
    apns --> devices
    fcm --> devices
    devices -. "open events (optional)" .-> api
    obs -. "scrapes /metrics, collects stdout logs" .-> ph
```

* **Delivery flow:** your application server calls the REST API, `api` enqueues a job in PostgreSQL, and `worker` picks it up and sends it to APNs / FCM. For scheduled deliveries, `scheduler` enqueues the job when it is due.
* **Storage:** device tokens, recipient attributes, notification content, delivery logs, and analytics all stay in your own PostgreSQL.
* **The only traffic leaving your network is `worker` → APNs / FCM.** There is no telemetry and no license check.
* **Open tracking (optional):** APNs and FCM do not report opens, so your app reports them to `api` through `POST /v1/events`.
* **Monitoring:** your own Prometheus and log collector pull metrics and logs from push-hub.

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for the architecture direction.

## Quick Start

> **Preview:** no image has been published yet. This shows the planned startup experience and does not work today.

push-hub and PostgreSQL start with a single `docker-compose.yml`:

```yaml
services:
  postgres:
    image: postgres:17
    environment:
      POSTGRES_USER: pushhub
      POSTGRES_PASSWORD: pushhub
      POSTGRES_DB: pushhub
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U pushhub"]
      interval: 5s
      timeout: 5s
      retries: 10

  push-hub:
    image: ghcr.io/aianchor-lab/push-hub:latest
    command: ["all"] # api + worker + scheduler in one process
    ports:
      - "8080:8080"
    environment:
      PUSHHUB_DATABASE_URL: postgres://pushhub:pushhub@postgres:5432/pushhub?sslmode=disable
      PUSHHUB_API_KEY: change-me
      # APNs (token-based authentication with a .p8 key)
      PUSHHUB_APNS_KEY_FILE: /run/secrets/apns.p8
      PUSHHUB_APNS_KEY_ID: ABC123DEFG
      PUSHHUB_APNS_TEAM_ID: DEF123GHIJ
      PUSHHUB_APNS_TOPIC: com.example.app
      # FCM (service account JSON)
      PUSHHUB_FCM_CREDENTIALS_FILE: /run/secrets/fcm.json
    volumes:
      - ./secrets:/run/secrets:ro
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  pgdata:
```

```sh
docker compose up -d
curl http://localhost:8080/healthz
# {"status":"ok"}
```

Docker Compose is intended for trials and small deployments. For production, use the Helm chart with a managed PostgreSQL outside the cluster (see [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md#5-distribution)).

## API

> **Draft:** this is a mock specification published for feedback. It may change before the first release.

* Base path: `/v1`. Requests and responses are JSON.
* Authentication: `Authorization: Bearer <PUSHHUB_API_KEY>`. API keys are for server-to-server use only; browser-safe authentication is still TBD.

### Register a device — `POST /v1/devices`

Registers a device token, or updates it if it already exists.

```http
POST /v1/devices
Authorization: Bearer change-me
Content-Type: application/json

{
  "platform": "ios",
  "token": "a1b2c3d4e5f6...",
  "user_id": "user_123",
  "attributes": { "plan": "pro", "locale": "en-US" }
}
```

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": "dev_01JC8Z5Q7X3M2N4P6R8T0V2W4Y",
  "platform": "ios",
  "user_id": "user_123",
  "active": true,
  "created_at": "2026-11-05T12:00:00Z"
}
```

`platform` is `ios` or `android`. Tokens rejected by APNs / FCM are deactivated automatically (`"active": false`).

### Send a notification — `POST /v1/messages`

Delivery is asynchronous: the API enqueues the message and returns immediately, and workers send it to APNs / FCM.

```http
POST /v1/messages
Authorization: Bearer change-me
Content-Type: application/json

{
  "to": { "user_ids": ["user_123", "user_456"] },
  "notification": {
    "title": "Your order has shipped",
    "body": "Tap to track your package.",
    "image_url": "https://assets.example.com/shipping.png"
  },
  "data": { "order_id": "ord_789" },
  "scheduled_at": null
}
```

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

{
  "id": "msg_01JC8Z6A1B2C3D4E5F6G7H8J9K",
  "status": "queued",
  "recipients": 3,
  "scheduled_at": null,
  "created_at": "2026-11-05T12:00:01Z"
}
```

`recipients` is the number of active devices targeted. Set `scheduled_at` (ISO 8601) to deliver later; the status is then `scheduled`.

### Report an open event — `POST /v1/events` (optional)

APNs and FCM do not report opens, so your app reports them here.

```http
POST /v1/events
Authorization: Bearer change-me
Content-Type: application/json

{
  "type": "opened",
  "message_id": "msg_01JC8Z6A1B2C3D4E5F6G7H8J9K",
  "device_id": "dev_01JC8Z5Q7X3M2N4P6R8T0V2W4Y",
  "occurred_at": "2026-11-05T12:03:00Z"
}
```

```http
HTTP/1.1 204 No Content
```

### Errors

All errors use the same format:

```json
{
  "error": {
    "code": "invalid_request",
    "message": "platform must be one of: ios, android",
    "request_id": "req_01JC8Z7M9N0P1Q2R3S4T5U6V7W"
  }
}
```

| Status | `code` | Meaning |
|--------|--------|---------|
| 400 | `invalid_request` | Malformed JSON or invalid parameters |
| 401 | `unauthorized` | Missing or invalid API key |
| 403 | `forbidden` | The API key does not have permission for this operation |
| 404 | `not_found` | The referenced message or device does not exist |
| 429 | `rate_limited` | Too many requests; retry after the `Retry-After` header |
| 500 | `internal_error` | Unexpected server error |

## Observability

> **Planned:** monitoring support is not implemented yet. Details may change before the first release.

push-hub needs no proprietary agent. It exposes metrics, logs, and health checks in standard formats so they plug into the monitoring stack you already run (Prometheus / Grafana, Datadog, CloudWatch, ELK).

| Feature | Endpoint / output | Notes |
|---------|-------------------|-------|
| Metrics | `GET /metrics` | Prometheus text format. Examples: messages sent and failed (per platform), queue depth, APNs / FCM latency |
| Logs | stdout, JSON lines | One structured event per line. Includes `request_id`, so log lines can be matched to API error responses |
| Liveness | `GET /healthz` | The process is running. Returns `{"status":"ok"}` |
| Readiness | `GET /readyz` | Returns 200 only when push-hub can serve traffic (e.g. the database is reachable). For Kubernetes readiness probes |
| Dashboard | Grafana JSON | A sample Grafana dashboard definition will be included |

Example log line:

```json
{"ts":"2026-11-05T12:00:02Z","level":"info","msg":"notification delivered","message_id":"msg_01JC8Z6A1B2C3D4E5F6G7H8J9K","platform":"ios","request_id":"req_01JC8Z7M9N0P1Q2R3S4T5U6V7W"}
```

Metric names and log fields are not final. If you need specific metrics or integrations, let us know in an [issue](https://github.com/aianchor-lab/push-hub/issues) or the [survey](#support-the-project).

## Support the Project

push-hub is in the demand validation phase: whether we build it depends on whether enough teams actually need it. You can help in two ways.

* **⭐ Star this repository.** Stars are one of the signals we use to decide whether to start production development. Starring also lets you follow progress.
* **📝 Answer the [3-minute survey](https://docs.google.com/forms/d/e/1FAIpQLSfy40dCmbFtYYkesl8lL9IoW6KyR2qLuC6Wm4MJOnWRVuYOQQ/viewform?usp=dialog).** Tell us how you send push notifications today and what is holding you back.

We especially want to hear from teams that:

* cannot send customer data or device tokens to a push SaaS for compliance or contractual reasons,
* are paying a growing "SaaS Tax" on push delivery volume, or
* need to run push delivery on-premises or in an air-gapped environment.

Survey answers are used only to evaluate demand for push-hub. We will not contact you unless you leave contact details and ask us to.

## License

push-hub is **source-available** software licensed under the [Business Source License 1.1](LICENSE) (BSL 1.1).

| Your use | License required |
|----------|------------------|
| Non-production use (development, testing, evaluation, PoC) | Free |
| Production use by a Small Business (fewer than 100 employees and contractors, and less than US$1M in annual revenue, including affiliates) | Free |
| Non-Commercial Use | Free |
| Any other production use | Commercial license |
| Offering push-hub to third parties as a hosted or managed service | Not permitted under the BSL; requires a separate agreement |

Each tagged release automatically converts to the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0) three years after its release date.

See [LICENSE](LICENSE) for the exact terms. For commercial licensing, please contact **aianchor.lab@gmail.com**.

## Contributing

Contributions require agreement to the [Contributor License Agreement](CLA.md). See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

> **Note:** we are not accepting external pull requests until a legally reviewed version of the CLA has been published.

## Security

Please do not report security vulnerabilities through public issues. See [SECURITY.md](SECURITY.md).
