# Physical layout

## Ownership map

The following are intended implementation locations, not claims that source files already exist. Existing root instructions, browser/shell policies, disclosure and bundled plugin remain authoritative. Logical responsibilities come from [DECOMPOSITION](DECOMPOSITION.md).

| Owner / artifact | Source or output home | Verification / documentation home |
| --- | --- | --- |
| Geometry, piece definitions and collision | src/core/geometry.ts | tests/unit/geometry.test.ts |
| Random piece source | src/core/random.ts | tests/unit/random.test.ts |
| Session and lock/progression state | src/core/engine.ts; shared types in src/core/types.ts | tests/unit/engine.test.ts; deterministic fixtures in tests/fixtures/ |
| Coordinator and frame lifecycle | src/app/controller.ts | tests/unit/controller.test.ts |
| Keyboard/focus adapter | src/browser/input.ts | tests/unit/input.test.ts and tests/e2e/game.spec.ts |
| Canvas rendering | src/browser/renderer.ts | tests/e2e/game.spec.ts; ignored screenshots in test-results/ |
| HTML status, controls and startup | src/browser/view.ts, src/main.ts, index.html, src/style.css | tests/e2e/game.spec.ts; README.md player/setup instructions |
| Build and runners | package.json and lockfile, tsconfig.json, vite.config.ts, playwright.config.ts | npm scripts documented when actually established |
| Product/development contracts | docs/dev/ roots | Adjacent SPEC/PLAN/TASKS review reports; phase reports under docs/dev/reports/phases/ |
| Production build and browser evidence | dist/, test-results/, playwright-report/ | Generated and ignored; no browser binaries or runtime profiles in tracked source |

## Dependency and path rules

Core files import only other core code and injected interfaces. Controller coordinates core and browser adapters; renderers never write core state. Unit tests can import pure components directly. End-to-end tests use the actual app entry and production build/preview path. Keep fixtures local and deterministic; do not bake test-only globals into production.

Package manager/runtime versions and commands must be pinned and documented during authorized toolchain setup, with a committed lockfile. Browser binaries and profiles use a dedicated absolute location outside tracked source as required by BROWSER_DEV_POLICY.md. Root *.tkn exclusion remains in force. Do not modify unrelated toolchains or /pyenv.

## Reports and generated assets

Milestone reports belong at docs/dev/reports/phases/<phase>/<milestone>.md and phase reports at docs/dev/reports/phases/<phase>/PHASE-REPORT.md. TASKS names future report paths; they are not created until their assigned implementation boundaries. Build artifacts, coverage, logs and screenshots are reproducible evidence rather than source. Keep only deliberately selected stable fixtures tracked. Manual conversation provenance is governed by docs/ai-logs/AGENTS.md and excluded from routine product validation.
