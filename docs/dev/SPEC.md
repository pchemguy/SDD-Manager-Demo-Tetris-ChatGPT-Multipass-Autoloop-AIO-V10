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
