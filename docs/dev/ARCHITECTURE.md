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

## Placement assistance and piece supply

The core owns shuffled bag state, Hold slot/availability, rotation kick evaluation, projected landing and grounded lock timing. Presentation reads this state through the same snapshot, including a Hold display and ghost; it does not calculate a second landing. A pure landing operation computes ordinary collision-limited descent for both ghost and hard drop, preventing preview/action divergence.

The session engine governs piece exchange and grounded lifecycle. Piece-source state advances only when a new queued kind is consumed; Hold swaps do not reseed it. Timer state belongs to the live piece and is discarded on lock or exchange. Geometry remains a pure bounds/occupancy service; rotation policy supplies ordered kick offsets. No browser timer independently locks a piece.

## Penetration and legal settlement

Separate traversal validity from settlement validity in the core. Ordinary traversal requires empty cells; penetrator traversal permits occupied cells but still requires bounds and a reachable legal settlement at or below the candidate. Settlement always requires four in-bounds empty cells. A mode-aware pure projection supplies the same destination to natural descent, hard drop and ghost.

The engine owns the bonus ledger, activation transaction and piece-lifetime ability flag. It recalculates grounding after an ability/movement change and never locks an overlapping intermediate placement. The view maps active ability to white rendering and bonus count to symbols, without changing piece kind or stored board data. The ability ends on lock; settled cells retain the original kind's normal nonwhite color. This keeps white a visible indication of the live ability rather than permanent board state.

## Session settings boundary

An application-owned settings value contains only validated choices and starts from resolved validated values at page initialization. HTML form controls emit changes through the coordinator; rendering and command availability consume the current value. The engine snapshots general/drop lock policies when each active piece spawns, including Hold replacement, so later form changes cannot retroactively alter a live deadline.

Settings contain no board/Hold/bonus state and survive engine restart within a page. The core receives typed policy values rather than reading DOM controls. Next preview inspects the authoritative source queue without consuming it. Browser presentation exposes labeled native controls, disabled states and current/next-piece policy feedback. There is no storage, cookie or location dependency in this settings boundary.

## Persistence adapters and startup ordering

A browser settings-source adapter reads the current URL and cookie string, applies a pure per-field codec/resolver and supplies validated initial settings before the first game is created. Valid explicit URL selections are saved to their cookies during initialization. Application settings remain independent of DOM/storage APIs; the core receives only resolved values and per-piece snapshots.

On user selection, the coordinator commits a valid model update, then asks the adapter to write the corresponding cookie and replace the current URL query value. Cookie and history effects are independent and nonfatal to gameplay. The adapter preserves unrelated parameters/fragment and reports unsaved status instead of falsely claiming success. No gameplay board, score, Hold slot or bonus state is persisted.
