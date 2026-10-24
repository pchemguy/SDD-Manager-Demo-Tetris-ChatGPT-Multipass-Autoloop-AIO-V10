# Implementation tasks

This is the single owning implementation checklist derived from [PLAN](PLAN.md), [SPEC](SPEC.md) and [layout](layout.md). Each task includes its own tests and affected documentation. All work is planned and unchecked; document acceptance does not claim implementation. Respect phase activation, task result/evidence/checkbox commit atomicity, per-commit push/readback and the human-authorized implementation range. At each complete phase, verify and publish its explicit integration before the requested release/tag/notes and dependent activation.

## Phase 1 — Playable classic baseline

- [ ] Phase 1 — Playable classic baseline
    - [ ] Milestone 1.1 — Playable classic game
        - [ ] T-001 — Establish isolated build and verification toolchain
            Depends on: none. Scope: package/lockfile, TypeScript/Vite entry and unit/browser runners; dedicated browser binary/profile paths. Contract: B05.
            Evidence: pin compatible dependencies and runtime commands; production skeleton builds; both required browsers complete two isolated context/page/input/render/teardown cycles under whole-process deadlines. Diagnose provisioning/session failures under BROWSER_DEV_POLICY.md; do not declare game acceptance from the synthetic probe.
        - [ ] T-002 — Implement piece geometry and legal movement
            Depends on: T-001. Scope: src/core/geometry.ts, types.ts and tests/unit/geometry.test.ts. Contract: B01.
            Evidence: all seven spawn matrices and rotation directions, wall/floor/stack rejection and occupied-cell-only bounds pass deterministic fixtures; rejected candidates preserve state.
        - [ ] T-003 — Implement classic session progression
            Depends on: T-002. Scope: src/core/random.ts, engine.ts and focused random/engine tests. Contract: B01–B04.
            Evidence: independent piece draws, gravity partitions, failed-down locking, simultaneous 1–4 clears, score/level threshold, blocked spawn and restart fixtures pass with no mutable snapshot aliases.
        - [ ] T-004 — Integrate playable browser input and rendering
            Depends on: T-003. Scope: src/app/controller.ts, browser/input.ts, renderer.ts and app entry. Contract: B02, B04–B05.
            Evidence: actual app supports movement, rotation, soft down and restart; fake-clock/focus checks show no hidden-time jump or duplicate loop/listener after restart; Canvas rendering and keyboard delivery checked in both browsers.
        - [ ] T-005 — Complete readable status and baseline delivery evidence
            Depends on: T-001, T-002, T-003, T-004. Scope: browser/view.ts, style.css, end-to-end scenarios, README and version 0.1.0 build metadata. Contract: B01–B05.
            Evidence: play/clear/game-over/restart demonstrations, status/focus and inspected screenshots pass in Chromium and Firefox; static build and user setup/control docs verified.
            Milestone exit: review the whole capability and dependencies, execute required checks, repair blockers, and commit docs/dev/reports/phases/1/1.1.md plus HANDOFF with this task result and initial completion checkbox. TODO section records None or justified eligible follow-up.
    - [ ] Milestone 1.2 — Phase review
        - [ ] T-006 — Review, test and report phase 1
            Depends on: T-005 and all Phase 1 delivery milestone completion/closure. Scope: whole-phase code review, relevant regressions, blocker repairs, build/version readiness, report and HANDOFF; preserve prior working behavior. Contract: B01–B05.
            Evidence: phase exits and actual app/browser evidence reviewed; findings repaired or eligible noncritical TODOs explained; committed docs/dev/reports/phases/1/PHASE-REPORT.md and HANDOFF; integration/release remain subject to their separate gates.
