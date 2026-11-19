# ADR-0009: Write Audit Events to the Structured Log

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

Regulated users need a record of who did what and when: sends, setting changes, key issuance, and sign-ins. They usually collect logs in their own SIEM (for example Splunk or Datadog), which already provides search, retention, export, and write-once storage.

## Decision

* push-hub writes audit events as structured JSON lines to stdout, marked with `"type":"audit"`, in the open source edition.
* Each event records the actor (user or API key), the app, the action, the target, the time, and the request ID.
* An audit log viewer in the admin UI is not part of v1.0. If users ask for one, it is added to the open source edition.

## Alternatives Considered

* **No audit events in the open source edition:** regulated users could not pass their reviews.
* **A storage and viewer layer as a paid feature:** a SIEM already provides it, so it would add little value.

## Consequences

* No audit storage to build or operate in v1.0.
* Users without a log collector see audit events only in their container logs.

## Evidence

* No external sources.
