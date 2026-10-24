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
