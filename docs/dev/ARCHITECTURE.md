# Architecture

## Context and boundaries

[PROJECT](PROJECT.md) defines a static, single-player keyboard game. TypeScript implements a browser-independent rules core, an application coordinator and browser presentation/adapters. No backend, account service or gameplay network request is involved.

## Major blocks

| Block | Responsibility | Dependencies and owned data |
| --- | --- | --- |
| Game core | Validate pieces, transform the active piece, advance time, settle and clear rows, compute progression and manage game lifecycle | Owns settled board and live game state. Uses injected random values; imports no DOM, Canvas, cookie or URL APIs |
| Application coordinator | Translate ordered player actions and elapsed active time into core transitions; provide render snapshots | Owns animation lifecycle, queued actions and focus/visibility suspension. Calls core and supplies clock/random adapters |
| Browser presentation | Draw board/piece, expose readable status, controls and restart | Reads snapshots; emits commands through coordinator. Owns Canvas sizing and DOM nodes, never authoritative gameplay state |
| Platform adapters | Supply monotonic time and random values, map keyboard events | Depend on browser APIs. Injected at the application boundary, replaceable with deterministic fakes |

Dependency direction is presentation/adapters → coordinator → core. The core returns values rather than reaching outward to redraw or start timers. One coordinator serializes all state transitions; rendering reads a stable completed snapshot.

## Time, state and lifecycle

The settled board and falling piece are separate. Occupied cells enter the settled board only through a successful lock transaction. Clearing rows, score/progression and next spawn form one completed transition; consumers never observe a half-cleared board. Restart produces a fresh state and clears queued inputs and elapsed-time accumulators.

Use a single requestAnimationFrame lifecycle. Inject monotonic elapsed time into the core, independent of frame frequency. Suspending while hidden or unfocused clears held input and excludes inactive wall time; resuming starts with a fresh frame timestamp. This avoids accumulated surprise gravity. Teardown cancels the frame and removes owned event listeners; restart does not create another loop.

## Structural choices and verification seams

Canvas provides a small predictable board-drawing surface; semantic HTML carries score, instructions, status and restart focus. This avoids coupling core rules to rendering details while retaining accessible controls. A UI framework is unnecessary for this bounded interface.

Immutable external snapshots prevent view code from mutating the board. Internal data representation may use copied arrays or controlled mutation behind the core API; externally visible transitions remain atomic. Reject invalid player actions without partial mutation. Injected clocks and random streams make boundary behavior reproducible.

Pure core tests cover geometry, gravity, clear/scoring and lifecycle. Browser tests exercise focus, keyboard delivery, Canvas output, status and restart through the actual built application in the required isolated browser matrix. Design seams do not substitute for browser evidence.
