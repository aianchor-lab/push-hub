# ADR-0003: Make the First Release v1.0.0 (GA), with No Target Date

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

A minimal first version (immediate delivery only) would not replace a push SaaS, and an existing open source gateway already covers that case (see [ADR-0007](0007-v1-feature-scope.md)). The target users in healthcare and finance do not adopt alpha or beta software. Nobody follows the project before it is announced, so a public deadline has no audience.

## Decision

* The first release is **v1.0.0 (GA)**. No alpha or beta releases are published.
* There is no target date. The release happens when the v1.0 scope ([ADR-0007](0007-v1-feature-scope.md)) and the quality bar ([ADR-0012](0012-v1-quality-bar-and-test-strategy.md)) are met. The [Roadmap](../ROADMAP.md) lists phases and exit criteria instead of dates.
* v1.0.0 promises compatibility for three things only: the HTTP API under `/v1`, the configuration (environment variables), and database migrations. Go packages live under `internal/` and are not a public API.
* Breaking API changes after v1.0 are added as `/v2`, with `/v1` kept for a transition period.

## Alternatives Considered

* **A small v0.1 first:** too little to be useful, and no different from existing gateways.
* **Alpha and beta releases:** the target users do not adopt them, and they add release work.
* **A target date:** no external audience; scope is used to control the work instead.

## Consequences

* The API is fixed before any outside user has tried it. To compensate, push-hub is tested heavily against realistic failures before release ([ADR-0012](0012-v1-quality-bar-and-test-strategy.md)).
* Without a date, scope must be guarded explicitly to avoid drift.

## Evidence

* [ADR-0007](0007-v1-feature-scope.md) for the comparison with existing gateways
