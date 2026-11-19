# ADR-0006: Limit Built-in UI Customization to Branding, and Ship an AI Skill for Custom Screens

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

Companies often want small changes to an admin UI: their logo, their colors, or a screen for an internal workflow. AI coding agents make custom screens cheap to build. If users edit the built-in UI directly, their changes conflict with every upgrade, and support has to deal with many different UIs.

## Decision

Customization has three levels:

| Level | How | Supported |
|-------|-----|-----------|
| Branding | Configuration for logo, title, and theme (CSS custom properties) | Yes. Kept compatible across upgrades |
| Custom screens | Separate screens built on the public API and the OpenAPI specification, with help from a bundled AI skill | The API is supported; the custom screens are the user's responsibility |
| Editing the built-in UI | Possible, since the UI has no build step | No. Treated as a fork |

* The **AI skill** follows the open Agent Skills format (`SKILL.md`) together with `AGENTS.md`, so it is not tied to one AI tool. It covers the API, the design tokens, examples, and the rules below.
* **Authorization is enforced on the server only.** A custom or AI-generated screen cannot grant itself more access than its session has.
* A slot or plugin mechanism inside the built-in UI is not part of v1.0. It is considered only if users ask for it.

## Alternatives Considered

* **Branding only, no AI skill:** safe, but offers nothing beyond what other products offer.
* **Let users override built-in UI files (for example through an override directory):** flexible, but breaks on upgrades and makes support harder.

## Consequences

* Everyone runs the same built-in UI, so upgrades and bug reports stay simple.
* The public API must be complete enough to build any screen the built-in UI has.
* Companies that cannot use AI tools, for example in air-gapped environments, get a complete product from the built-in UI alone.

## Evidence

* Agent Skills format: published as an open specification by Anthropic on 2025-12-18, with support reported in OpenAI Codex, Cursor, and GitHub Copilot ([Firecrawl](https://www.firecrawl.dev/blog/agent-skills), [MicrosoftDocs/Agent-Skills](https://github.com/MicrosoftDocs/Agent-Skills/)). Secondary sources; to be checked against each tool's documentation when the skill is built.
