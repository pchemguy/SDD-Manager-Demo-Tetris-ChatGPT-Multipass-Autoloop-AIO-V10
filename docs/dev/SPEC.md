# System specification

## Scope and ownership

The game serves desktop keyboard players described in [PROJECT](PROJECT.md). [ARCHITECTURE](ARCHITECTURE.md) and [DECOMPOSITION](DECOMPOSITION.md) define structural owners. The following requirements are normative; acceptance examples describe required outcomes, not completed tests. No accounts, multiplayer, remote leaderboard or touch interface is required.

## B01 — Board and piece geometry

The visible board has columns 0–9, rows 0–19, increasing right/down. A cell is empty or contains a settled tetromino kind. The active piece is separate and has exactly four distinct occupied cells. Legal ordinary placement requires all four cells inside the board and empty in the settled grid. Rejected translation/rotation preserves board, piece and score.

Kinds are I, O, T, S, Z, J, L. Spawn matrices, with `/` separating rows and `.` denoting empty padding, are I `..../IIII/..../....`; O `.OO./.OO./..../....`; T `.T./TTT/...`; S `.SS/SS./...`; Z `ZZ./.ZZ/...`; J `J../JJJ/...`; L `..L/LLL/...`. Rotate 3×3 and I 4×4 matrices about their centers in quarter turns; O occupied cells stay fixed. Spawn orientation is 0, anchor row 0, column floor((10 − matrix width)/2). Empty padding may extend outside the board when occupied cells remain legal.

Each next kind is an independent uniform draw from the seven kinds, in I/O/T/S/Z/J/L order using floor(7r), r in [0,1). Spawn failure enters game over. It does not clear stacked cells or retry another kind.

Acceptance: each kind contains four cells in each orientation; edge/floor/stack collisions reject atomically; a blocked spawn ends the game; deterministic random boundary values select the expected kinds.

## B02 — Actions and time

Left/right translate one cell. Arrow Up or X rotates clockwise; Z rotates counterclockwise. Arrow Down moves one row per soft-down action. R and the Restart button start a fresh game. Discrete rotate/restart actions ignore native key-repeat. Horizontal/soft-down held input uses immediate action, 150 ms initial delay and 50 ms repetition; opposing horizontal inputs use the most recently pressed direction. Actions at the same timestamp process in queued arrival order before elapsed-time advancement. Consumed game keys prevent page scrolling only while the board/game region has focus; HTML controls retain their standard keyboard operation.

Gravity interval at level L is max(100, floor(1000 × 0.8^L)) milliseconds. Start at level 0. Every accumulated full interval attempts one-row downward movement; preserve the fractional remainder. Failed gravity or soft-down attempt locks the piece immediately. Successful soft-down earns 1 point per moved row. Ignore gameplay movement after game over; restart remains available.

Elapsed time is monotonic, finite and nonnegative. Hidden/unfocused time is excluded; clear held keys and pending movement on suspension, reset the frame timestamp on resume. Do not replay inactive wall time. There is one controller loop; repeated restart does not multiply gravity or listeners.

Acceptance: exact interval boundaries advance consistently across frame partitions; failed down locks; left/right/rotation cannot overwrite cells; focused keys work while control editing does not move the piece; background/resume causes no jump.

## B03 — Lock, clear and progression

Lock only a fully legal placement. Merge four cells, identify all full rows, remove them simultaneously, compact remaining rows downward in their existing order and insert empty rows at the top. For k=1/2/3/4 cleared rows, award 100/300/500/800 × (pre-clear level + 1) points. No-clear lock awards zero line points. Update total lines, set level=floor(totalLines/10), and use its gravity interval for subsequent falling. Spawn the next piece only after the lock/clear/score transaction completes; begin its gravity remainder at zero. No timer can move the old piece after lock.

Acceptance: one through four row clears produce exact compaction and score; crossing a ten-line threshold uses old level for the award and new level afterward; cells outside cleared rows retain identity and order.

## B04 — Lifecycle and player feedback

Opening the page starts a fresh game with empty board, score/lines/level zero and a valid initial piece. Game over freezes movement/time and visibly identifies the outcome. Restart resets board, progression, active piece, elapsed remainder and queued/held inputs; no reload is needed. Random stream reset is not required, but a fresh game draws through the same defined source contract.

Show board, active piece, numeric score/level/lines, controls and a keyboard-operable restart button. Regular piece colors are distinct from the board and nonwhite; settled cells retain their piece identity/color. Instructions/status use readable HTML, not Canvas alone. Focus indicators remain visible. At a 1280×720 desktop viewport, board, primary controls and status are usable without overlapping; smaller viewports may scroll.

Acceptance: play-to-game-over/restart loop works repeatedly; screenshot inspection confirms cells and statuses are visible, numeric changes agree with core state and restart returns to a fresh board without duplicated input handling.

## B05 — Technical and failure contracts

The production build comprises static browser assets, requires no gameplay network service and exposes no credential. The core imports no browser globals and accepts injected clock/random sources. Snapshots expose no mutable aliases into engine state. Invalid injected time/random inputs fail before state mutation; ordinary impossible player actions are no-ops. Browser resource initialization failure presents an intelligible error rather than a blank apparently running board. Dispose removes owned scheduling/listeners.

Core fixtures cover geometry, multiple clears, progression, spawn failure and restart with deterministic inputs. Browser acceptance uses isolated Chromium headless shell and Playwright-managed Firefox per the root policy: repeated context/page lifecycle, actual app keyboard interaction, Canvas screenshot inspection and clean teardown. A successful build or browser launch alone does not establish gameplay acceptance. Setup failures are diagnosed independently per required browser; document-only work requires no browser installation.

## Acceptance route

| Requirement | Responsible component(s) | Required evidence |
| --- | --- | --- |
| B01 | Geometry, Piece source, Session engine | Boundary/rotation/draw fixtures and blocked-spawn case |
| B02 | Session engine, Game controller, Keyboard adapter | Fake-clock action tests plus focused/background browser interaction |
| B03 | Lock/progression operations | Simultaneous-clear, compaction and score/level fixtures |
| B04 | Controller, Board renderer, Status/control view | Actual game-over/restart scenario and inspected screenshots |
| B05 | Core and adapters | Static build, deterministic seams, failure/alias/teardown checks and both-browser evidence |

## Enhanced delivery contract scope

B01–B05 define the classic delivery contracts. The complete enhanced game uses E01 instead of B01's independent draw, E02 instead of B01's no-kick rotation, and E03 instead of B02's immediate failed-down locking. Those precise aspects are enhanced-delivery changes; classic phase exits remain limited to B01–B05. All other baseline guarantees continue to apply. The enhanced controls are available without a configuration panel.

## E01 — Bag and Hold

Shuffle I/O/T/S/Z/J/L with Fisher–Yates, visiting i=6 down to 1 and swapping i with floor(r×(i+1)) using one injected [0,1) draw per step. Consume each shuffled bag from index 0 before shuffling another. Every seven-kind bag contains each kind once; a boundary may repeat a kind across adjacent bags. Inspecting queued kind consumes nothing.

C or either Shift activates Hold. Empty slot stores current kind and consumes one source kind; populated slot swaps kinds without consuming source. Incoming piece starts at B01 spawn orientation/anchor with fresh gravity/grounded state. Once used, further Hold commands do nothing until that active piece locks. Returning from Hold counts as the same active lifetime for this restriction; do not reset Hold-used just because a swap spawned a piece. Blocked incoming spawn ends the game and preserves the resulting Hold slot. Restart empties Hold and resets availability and bag queue. Hold view displays stored kind and unavailable state.

Acceptance: exact bag membership and deterministic shuffle order; empty/nonempty swaps with correct source consumption; repeat suppression until lock; blocked swap/game-over and restart clear state.

## E02 — Rotation and landing projection

Quarter-turn rotation uses [ordered wall-kick tables](spec/ROTATION.md). The pure ordinary landing is the last legal placement reached by moving down one row repeatedly until the next step would collide or leave the board. It does not search through settled blockers. Ghost cells equal that projection, drawn behind the active piece and distinguishable from settled cells. A coincident ghost may be omitted to keep the active piece legible. Ghost is shown at every other valid projected landing in this delivery scope.

Acceptance: all kick transitions and rejection fixtures pass; ordinary projection stops above blockers; preview cells and hard-drop placement match for every tested orientation/edge/stack case.

## E03 — Grounded full-interval lock delay

Grounded means the live piece is at a valid landing and cannot descend farther under its movement rule. Detect ground contact immediately after spawn or a successful move/rotation/down/drop reaches such a landing. Capture the current gravity interval and start grounded elapsed time at zero at contact, independently of the gravity remainder. Lock when active grounded elapsed reaches that full captured interval, never earlier.

A failed downward attempt while grounded does not immediately lock. Successful translation/rotation that stays grounded retains elapsed time; leaving ground cancels contact and elapsed, and later contact starts a fresh full interval. Failed actions do not reset timers. Repeated hard drop to the same landing retains an existing deadline. Hard drop to a different landing establishes contact there. Hold/lock/restart clear piece timers. Hidden/unfocused time is excluded. A single large elapsed update must split at gravity/contact/lock boundaries; time before contact cannot count toward the grounded delay, and leftover active time can advance a newly spawned piece only after the lock event. Match results from equivalent small time partitions.

Acceptance: contact at t=0 with interval I does not lock at I−1 and locks at I; old gravity remainder cannot shorten I; floor kicks staying grounded cannot refresh it; leaving/regaining ground restarts it; repeated drop/failed-down/rotation spam cannot reset an unchanged contact; inactive time adds zero.

## E04 — Hard drop and enhanced player feedback

Space moves a live piece to E02 landing immediately, awards 2 points per downward row actually moved and enters/continues E03 timing. A zero-distance repeat earns zero and does not refresh contact. Drop does not clear lines before lock. Game-over commands do nothing. Hold and hard-drop keys are discrete, ignore native repeat and obey B02 focus rules.

Show Hold status and ghost with updated control instructions. Browser acceptance exercises Hold before/after lock, a kick at a wall, hard drop matching ghost, the full visible delay before lock, and restart. Core acceptance covers all E01–E04 boundaries and retained B03 scoring/clear transactions. No next-piece preview is required by these contracts.

| Contract | Component route | Acceptance route |
| --- | --- | --- |
| E01 | Piece source and Session engine; Hold view | Bags, source-consumption, exchange lifecycle and browser availability |
| E02 | Rotation policy, Geometry, Landing projection | Tables, deterministic candidate selection, ghost/drop identity |
| E03 | Session engine and controller clock | Exact deadlines, reset/no-reset, active-time partition and lifecycle |
| E04 | Session/controller/input/rendering | Actual drop/hold controls, scoring and inspected game output |

## G01 — Conditional ghost visibility

G01 governs the complete game's ghost visibility instead of E02's unconditional presentation. With rows 0–19 and the midline between rows 9 and 10, show the ghost exactly when min(projected occupied-cell row) ≥ 10. Matrix padding, active-piece height, anchor row and centroid do not determine eligibility. If any occupied projected cell is in rows 0–9, hide the entire ghost; a straddling projection is hidden. Recompute from the authoritative landing after every relevant board/piece/orientation transition. If eligibility becomes true, show it again immediately on the next render. E02's coincident-piece legibility treatment remains applicable.

The predicate is pure; hiding the ghost does not change collision, hard-drop destination, score, lock timing, piece-source state or input. No separate visibility setting is part of this contract.

Acceptance: fixtures with uppermost projected cell at 9 versus 10 produce hide versus show; a straddling vertical I is hidden; an anchor/padding cell above 10 with all occupied cells below still shows; upper/lower transitions restore visibility both ways. Browser scenarios inspect the Canvas output and verify identical drop landing whether the ghost is visible or hidden. Responsible components: Landing projection (cells), Board renderer (predicate and drawing).

## P01 — Bonus ledger and activation

Start/restart with zero bonuses and no active penetrator. Each lock clearing exactly four rows grants one bonus, capped at three, after ordinary line scoring. Other clears grant none; a penetrator's own four-line clear is eligible. P is a discrete activation hotkey with normal focus/repeat rules. With a live regular piece, fully legal current placement and at least one bonus, atomically decrement the count and mark that piece active. With no bonus, game over or an already active penetrator, do nothing and spend nothing. Repeated activation cannot consume another bonus.

Active penetration lasts until lock; restart also clears it. Hold is disabled throughout activation, even if the current Hold slot is empty. Rejected Hold changes no source/slot/timer/bonus state. On legal lock, clear the flag before subsequent spawn. Bonus count and held piece are independent session data; restarting clears both.

Acceptance: 0→activation no-op, 1→0 successful spend, repeated activation no double-spend, awards 0→1 and 3→3, no award for 1–3 row clear, active-piece four-line award, restart and game-over rejection. Indicator state must equal authoritative count after every transaction.

## P02 — Deepest valid penetrator landing

For a penetrator, ignore settled occupancy when traversing, but enforce board bounds on all occupied cells. A valid final placement has all four occupied cells in bounds and no settled overlap. Enumerate anchor rows from the current anchor downward through the last row whose occupied offsets fit; select the greatest row with a fully valid final placement, even if blocking rows occur above it. Current anchor is eligible if legal. Empty matrix padding is irrelevant. This search never clears, overwrites or removes existing cells.

Use exactly this destination for natural fall stopping, hard drop and ghost. At an overlapping intermediate placement, the current anchor is not a legal final candidate, but a lower legal candidate must exist. A lateral/rotation candidate is accepted only if its occupied cells are in bounds and at least one legal final placement exists at or below its anchor. Otherwise reject atomically. For rotation, try E02's kick order with this mode-aware traversal predicate. Ordinary pieces continue using ordinary collision-limited projection; penetration is never inherited by the source or Hold slot.

Acceptance: scan through one/multiple blocking rows to an empty cavity; choose the lowest of several legal cavities; stop at a valid placement above an unusable bottom; accept temporary overlap with a lower valid destination; reject candidate with no valid destination; preserve every pre-existing cell throughout traversal. Ghost and hard drop must agree exactly across these cases, including rotated/edge pieces. G01 evaluates this projected placement's occupied cells.

## P03 — Descent, grounding and lock safety

Natural gravity and soft down move one row toward P02's destination, allowing intermediate overlap; they stop at that destination. A penetrator is grounded only at a fully legal destination with no deeper valid placement under its current geometry. Intermediate collision is never grounding or a failed-down lock. Activation may reveal a deeper destination: recompute grounding and cancel an old grounded timer if contact is lost. At the deepest destination, use E03's full captured interval; hard drop also follows it. A failed downward command at that legal destination follows the applicable lock policy without extending the timer.

Before any lock, validate all four cells against settlement legality, regardless of mode or timer state. Never merge an invalid placement; retain a safe nonmutating failure rather than corrupt the board. Legal penetrator lock uses the ordinary B03 clear/score transaction, then clears ability and spawns a regular piece. Hard-drop points count anchor-row distance actually moved, including rows crossed through blockers.

Acceptance: no lock during overlap, full interval starts only at final legal contact, activation while previously grounded cancels contact when a deeper destination exists, repeat drop does not refresh an unchanged landing, lock preserves settled cells and active ability does not transfer to the next piece.

## P04 — Ability presentation and interaction evidence

Draw every occupied cell of the active penetrator white. Use a white outlined/translucent ghost that remains visually distinct from solid active/settled cells. Regular active and settled pieces use their normal nonwhite kind colors; after penetration ends at lock, the newly settled cells use their original kind color. The side panel shows one lightning symbol per available bonus (0–3), plus readable count/meaning. Update it immediately after award/spend. Hold control/status explicitly indicates that activation makes it unavailable.

Browser acceptance demonstrates earning a four-line bonus, activation/spend, white traversal through occupied cells, Hold rejection, dynamic ghost visibility with penetrating projection, matching hard drop, legal delayed lock and restart clearing state. Inspect Canvas screenshots and side-panel values in both required browsers. No test may award a completion claim from DOM text alone when white rendering/overlap is in scope.

| Contract | Structural owner | Acceptance route |
| --- | --- | --- |
| P01 | Session ledger/activation and view status | Atomic cap/award/spend/reject fixtures; live symbol count |
| P02 | Geometry and mode-aware projection | Deep cavities, rejected candidates, ordinary-mode regressions |
| P03 | Session engine, controller time | Traversal/ground/contact/valid-lock interaction fixtures |
| P04 | Renderer/input/status | White appearance, disabled Hold and actual both-browser ability sequence |

## C01 — Six session settings and defaults

Settings are independent validated values with the following choices. The full game uses these configurable policies; G01 defines the dynamic option, E03 defines interval locking and P01's active-penetrator Hold prohibition always applies.

| Setting | Allowed choices | Default | Effective boundary |
| --- | --- | --- | --- |
| ghost | hide, dynamic, show | dynamic | Immediately on next render |
| hardDrop | on, off | on | Next command |
| nextPreview | on, off | on | Immediately on next render |
| dropLock | follow, immediate | follow | Next piece spawn, including Hold replacement |
| lock | failed, interval | interval | Next piece spawn, including Hold replacement |
| hold | on, off | on | Next command; penetration still disables |

Render all six as labeled keyboard-operable side-panel controls with current selected values. Reject unknown/invalid values without changing any setting or game state. Update the chosen value atomically and keep unrelated choices unchanged. Keep controls synchronized with the model. Focus within controls must not trigger game actions or suppress native editing/navigation. At 1280×720 the board, settings and status remain readable without overlap; allow scrolling on smaller viewports.

## C02 — Live changes and lifetime

Serialize setting changes with gameplay commands. Presentation toggles and feature gates apply to subsequent actions/renders. General/drop lock policies are copied into the live piece at spawn, not read afresh on every tick. Changing either while a piece is falling/grounded changes the selected setting for the next spawn but preserves the current piece's policy and deadline. A successful Hold exchange is a spawn for policy capture while retaining E01's once-per-lock restriction. Communicate the next-piece application rule beside locking controls.

Disabling Hold preserves the stored kind and current Hold-used flag; mark it unavailable rather than spending/exchanging it. Re-enabling restores availability only when the piece has not used Hold and is not an active penetrator. Disabling hard drop makes Space a no-op with no drop points, placement or timer reset. Changing settings never resets score, board, bonus count or source queue. Game restart preserves selected settings but resets normal game state. Each full page initialization starts from the defaults; do not read or write cookies, URL parameters, localStorage, sessionStorage or another persistence source for these session-only contracts.

Acceptance: each control works independently; changing a grounded piece's lock policy does not change its current deadline; next ordinary or Hold-spawned piece uses the new policy; toggles work while penetrating; disabling/re-enabling Hold preserves its slot and once-per-piece restriction; restart retains settings, reload resets defaults; neither query strings nor cookies influence session-only initialization.

## C03 — Locking combinations

General `failed` locks a legally grounded piece on the first failed gravity or one-row soft-down attempt. Contact alone does not lock. General `interval` uses E03 exactly: failed down cannot shorten or reset its full contact interval. Both policies require legal settlement, including penetrators.

Hard-drop `follow` moves to the mode-appropriate landing and uses that live piece's captured general policy. The drop command itself is not a failed one-row downward attempt: under `failed`, even zero-distance drop waits for a subsequent failed gravity/soft-down attempt; under `interval`, it starts/retains contact timing. Hard-drop `immediate` locks a legal projected placement in the same command regardless of general policy, including zero distance. It uses B03/P01 transactions and clears the live timer. A disabled hard drop does nothing under either policy. Soft-drop behavior still follows general policy. Drop scoring is awarded once for rows actually moved, never again on a zero-distance repeat.

Acceptance: cover the 2×2 general/drop policy matrix with both regular and penetrating pieces, zero/nonzero distance, enabled/disabled drop, contact timing and legal settlement. An immediate penetrating drop must pass through blockers before locking at the deepest legal destination. Interleaving setting changes cannot give the current piece a mixture of old and new captured policies.

## C04 — Ghost options and next preview

`hide` suppresses all ghost drawing; `dynamic` applies G01's entire-projected-piece test; `show` makes every valid projection eligible regardless of midline. Existing coincident-piece legibility treatment applies in all visible modes. Visibility never changes landing. Active penetrator projection remains P02 and ordinary projection remains E02.

When nextPreview is on, show the next queued kind with normal nonwhite kind colors and a readable Next label. It is exactly the next source consumption if no populated-Hold swap intervenes. Preview is not the held piece: a populated Hold exchange leaves next unchanged, while empty Hold consumes one kind and advances the preview. Rendering/hiding/re-enabling the preview consumes no queue/random values. At startup, seed/inspect the bag consistently so first preview and next actual consumption agree.

Acceptance: all ghost modes above/below/straddling midline for ordinary and penetrating pieces, preview identity across draw/empty Hold/populated Hold/visibility toggles, no random-consumption changes from repeated renders, and actual controls/screenshots in both browsers. Controls remain accessible during game over and settings still govern a restart.

| Contract | Structural owner | Evidence route |
| --- | --- | --- |
| C01 | Settings model/view and coordinator | Defaults/allowlists, six independent controls and focus |
| C02 | Coordinator, engine spawn and view | Policy snapshots, feature gates, restart/reload lifetime |
| C03 | Engine/drop/landing | Policy matrix, exact timing, penetration and zero-distance cases |
| C04 | Renderer, source inspect and status view | Mode predicates, nonconsuming preview and browser output |
