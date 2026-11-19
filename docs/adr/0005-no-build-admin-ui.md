# ADR-0005: Build the Admin UI Without a Build Step

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

The admin UI should be easy to read and change, by people and by AI coding agents ([ADR-0006](0006-ui-customization-and-ai-skill.md)). A React / Vite application needs a Node toolchain and a build for every change. push-hub also runs in air-gapped environments and promises no outbound connections except APNs and FCM, so the UI cannot load libraries from a CDN.

## Decision

* The UI is plain HTML, CSS, and JavaScript modules, loaded with native **import maps**. There is no bundler.
* Third-party libraries are **vendored** as files in the repository (for example `ui/vendor/`) and embedded in the binary with `go:embed`.
* `package.json` is used by maintainers only, as the version manifest for vendored files. A `make vendor` target copies the files from `node_modules`. Dependabot or Renovate can update them. Users and customizers do not need Node.
* Only libraries that ship a **single-file ES module with no external imports** are allowed. The dependency count is kept to zero to two.
* Initial candidates: Alpine.js for interactivity; hand-written SVG for charts, with uPlot added only if SVG is not enough; the built-in `Intl` API for dates.

## Alternatives Considered

* **React / Vite:** easier for complex screens, but every change needs a build.
* **Loading libraries from a CDN:** breaks air-gapped use and the no-outbound-connection promise.
* **Chart.js, Lit, ECharts:** their ES modules import other packages, which import maps would have to resolve one by one.

## Consequences

* Complex screens, such as the segment builder and charts, take more hand-written code.
* uPlot does not ship a minified ES module, so either the unminified file (145 KB) is vendored or maintainers minify it.
* AI agents must not add dependencies on their own (see [ADR-0006](0006-ui-customization-and-ai-skill.md)).

## Evidence

Checked on 2026-11-17 by downloading each package with `npm pack` and searching the shipped ES module for imports of other packages:

| Package | npm dependencies | External imports in the ES module | Size | License |
|---------|------------------|-----------------------------------|------|---------|
| alpinejs 3.17.5 | `@vue/reactivity` | None (bundled into `dist/module.esm.min.js`) | 56 KB | MIT |
| uplot 1.6.32 | None | None (`dist/uPlot.esm.js`) | 145 KB | MIT |
| htmx.org 2.0.11 | None | None (`dist/htmx.esm.js`) | 171 KB | 0BSD |
| chart.js 4.6.1 | `@kurkle/color` | `import '@kurkle/color'` | 413 KB | MIT |
| lit 3.3.3 | 3 packages | `lit-html`, `lit-element`, `@lit/reactive-element` | — | BSD-3-Clause |
| echarts 6.1.0 | `zrender`, `tslib` | Not checked | — | Apache-2.0 |

Import map support, from [MDN browser-compat-data](https://github.com/mdn/browser-compat-data/blob/main/html/elements/script.json): Chrome and Edge 89, Firefox 108, Safari (including iOS) 16.4.
