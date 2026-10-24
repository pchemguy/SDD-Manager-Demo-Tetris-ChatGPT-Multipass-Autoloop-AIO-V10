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

## Placement-assistance ownership

Ordered rotation policy/tables live in src/core/rotation.ts with tests/unit/rotation.test.ts. Shared landing projection lives in src/core/landing.ts with tests/unit/landing.test.ts. Piece-source bag/inspect behavior belongs in src/core/random.ts; Hold and lock timers remain in src/core/engine.ts and types.ts rather than browser state. Their boundary fixtures extend tests/unit/random.test.ts and engine.test.ts.

Hold display and ghost drawing extend browser/view.ts and renderer.ts; action bindings extend browser/input.ts. Browser assist scenarios live in tests/e2e/enhancements.spec.ts. The normative kick child is docs/dev/spec/ROTATION.md. No second landing implementation belongs in a view or test helper; test fixtures assert the production projection result through defined seams.

## Penetrator ownership

Keep mode-aware traversal/settlement predicates in src/core/geometry.ts and deepest valid destination in src/core/landing.ts. Bonus count, active flag, movement/contact/lock transactions stay in src/core/engine.ts and types.ts; no second ability state store is needed. Focused fixtures extend unit geometry/landing/engine tests, with cavity/blocked-floor boards in tests/fixtures/.

P binding, white active/ghost rendering and bonus/Hold-disabled status extend existing input.ts, renderer.ts and view.ts. Actual ability scenarios belong in tests/e2e/penetrator.spec.ts. Board fixtures can construct bonus/stack state through core test seams; production exports no unguarded player-facing test hook.

## Session settings ownership

The typed settings/default/validation model belongs in src/app/settings.ts with tests/unit/settings.test.ts. Its lifecycle is coordinated by controller.ts; core engine/types own captured live-piece policies, and core tests exercise policy combinations without browser globals. Session setting updates do not import storage APIs.

Native controls and selected/pending policy feedback belong in src/browser/settings-view.ts; input.ts excludes their focus from gameplay bindings. Next display extends view.ts using random.ts inspect; ghost options extend renderer.ts. Six-control/lifetime/focus scenarios belong in tests/e2e/settings.spec.ts, with source-consumption and lock-matrix unit fixtures beside their core/model owners. No cookie/location source module is required for this session scope.

## Persistent configuration ownership

The canonical wire schema/codec/resolver belongs in src/app/settings-codec.ts with tests/unit/settings-codec.test.ts, consuming settings.ts defaults/allowlists. Browser cookie/location/history effects belong in src/browser/settings-storage.ts with tests/unit/settings-storage.test.ts using injected adapters. Bootstrap/controller performs resolution before model/game creation and persistence after validated changes.

Actual query/cookie/reload scenarios belong in tests/e2e/persistence.spec.ts with isolated contexts. Do not create tracked real-user cookie dumps, tokens, browser profiles or current-location logs. README documents the six public query keys and precedence when implemented/verified. Root RELEASE-NOTES.md owns final user-facing release notes; version metadata belongs in package.json and its lockfile once implementation establishes them. Final implementation evidence belongs in docs/dev/reports/IMPLEMENTATION-REPORT.md; earlier milestone/phase report paths remain unchanged.
