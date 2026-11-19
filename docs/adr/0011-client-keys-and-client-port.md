# ADR-0011: Public Client Keys on a Separate Client Port, Without Official SDKs

* **Status:** Accepted
* **Date:** 2026-11-18

## Context

Mobile apps register devices and report open events. Server API keys cannot be embedded in apps, because anyone can extract them. Running inside a customer's VPC does not mean push-hub is never reachable from the internet; customers can expose endpoints through their own load balancer or ingress. Push platforms solve this with a public, limited client credential.

## Decision

* **Two kinds of keys:**
  * **Server API keys** (secret): full API access within their scopes ([ADR-0008](0008-admin-authentication-and-authorization.md)).
  * **Client keys** (public, one or more per app): may only register and update a device and report events. They cannot send, read, or change anything else.
* **Separate client port:** client endpoints are served on their own port. Customers can expose only that port, and keep the server API and the admin UI internal.
* **Identity verification (optional):** the customer's server signs a JWT that binds a user ID, and the app passes it with client requests. Modes: disabled, optional, required.
* **Rate limits** on the client port, per IP address and per client key.
* **Relaying through the customer's server** with a server API key remains possible for customers who do not expose anything.
* **No official SDKs in v1.0.** The client API has two operations. push-hub publishes the OpenAPI specification and sample code for Swift, Kotlin, and a relay server.
* push-hub adds the `message_id` to each notification's custom data, so the app can report opens without extra lookups.

## Alternatives Considered

* **Relay through the customer's server only:** safe, but every customer must build a relay, and push platforms do not require one.
* **Official mobile SDKs (iOS, Android, Flutter, React Native):** four codebases to maintain.

## Consequences

* The README architecture diagram (devices reporting opens to push-hub) stays valid; the authentication description changes.
* The client port needs abuse protection and its own security review.

## Evidence

Checked on 2026-11-18:

| Service | Credential in the app | Limits | Impersonation protection |
|---------|-----------------------|--------|--------------------------|
| OneSignal | App ID: "safe to use in client-side SDK initialization. It is not a secret." | REST API keys must stay off clients | Identity Verification: ES256 JWT signed by the customer's server |
| Braze | SDK API key: "Designed to be public" | "It cannot export user data, send messages on your behalf, or modify campaigns." | SDK Authentication: RS256 JWT signed by the customer's server |
| Airship | App key and app secret: "intended to be embedded in distributed applications" | Master secret must not be embedded | Not covered by the page read |
| PostHog | Project token on public POST-only endpoints | Private endpoints need a personal API key | Not described |

Sources: [OneSignal Keys & IDs](https://documentation.onesignal.com/docs/keys-and-ids), [OneSignal Identity Verification](https://documentation.onesignal.com/docs/identity-verification), [Braze API basics](https://www.braze.com/docs/api/basics/), [Braze SDK Authentication](https://www.braze.com/docs/developer_guide/authentication/), [Airship API security](https://www.airship.com/docs/developer/rest-api/ua/api-security/), [PostHog API](https://posthog.com/docs/api). Whether these SDKs report opens directly or through the customer's server was not covered by the pages read.
