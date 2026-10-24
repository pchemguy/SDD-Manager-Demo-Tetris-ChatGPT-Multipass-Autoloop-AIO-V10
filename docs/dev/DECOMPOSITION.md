# Component decomposition

## Component map

The blocks in [ARCHITECTURE](ARCHITECTURE.md) contain these logical components. Interfaces describe responsibilities; SPEC owns precise behavioral contracts when available.

| Component | Provides / requires | Ownership and limits |
| --- | --- | --- |
| Geometry | Piece definitions, rotated cell offsets, board-bounds/collision predicate; accepts board and candidate | Stateless. Does not choose pieces, advance time or render |
| Piece source | Next tetromino kind from injected random stream | Owns random-consumption state; never changes the board |
| Session engine | Create/restart state; apply move/rotate/soft-down actions; advance active elapsed milliseconds; produce read-only snapshot | Owns board, active piece, gravity accumulator, score, cleared-line total, level and game-over state |
| Lock/progression operations | Validate final placement, merge cells, compact complete rows, calculate score/level and spawn outcome | Internal engine collaboration using Geometry and Piece source; one transaction, no independently running subsystem |
| Game controller | Start/dispose one frame loop; order commands and elapsed time; suspend/resume with focus/visibility | Requires engine, clock and source adapters. Owns no duplicate board |
| Keyboard adapter | Translate physical keys; clear held input on focus loss; prevent page scrolling for consumed game keys | Emits actions only when game focus permits; excludes text/control editing and ignores OS repeat for discrete actions |
| Board renderer | Draw settled cells and live piece from snapshot; resize drawing surface for display scale | Owns Canvas resources only; drawing errors cannot mutate the engine |
| Status/control view | Score/progression, instructions, start/game-over feedback and restart control | Semantic HTML, visible keyboard focus; sends restart through controller |

## State and collaboration

SessionState contains a 20×10 settled-cell grid, optional active piece (kind, orientation and integer anchor), score, total cleared lines, level, gravity remainder and running/game-over flag. A piece is represented by four occupied offsets; empty matrix padding is not a board cell. Live placement legality is evaluated on occupied offsets only.

The controller obtains elapsed active time and queued actions, processes them in deterministic order, then renders the resulting snapshot. The engine owns action rejection and lifecycle invariants. A lock validates placement before modifying the board, clears all complete rows simultaneously, applies progression and requests the next kind. Spawn failure ends the game without writing a colliding active piece into settled cells.

Restart replaces engine state and clears pending input/time in the controller; renderer and control nodes are reused. Disposal removes listeners and cancels owned scheduling. Browser failures are reported through visible startup/error state where appropriate rather than fabricating gameplay success.

## Interface and test boundaries

Core input consists of integer action identifiers, nonnegative finite elapsed milliseconds and a random source returning values in [0,1). Invalid time or injected random values produce a defined error without partial transition. Core output is a serializable snapshot with copied board/cell data. Tests can create explicit board fixtures and deterministic piece streams through internal construction seams; no test-only browser globals are part of the player API.

Geometry tests isolate walls/floor/rotation. Engine tests assert board conservation except line removal, simultaneous clears, scoring/progression, spawn failure and restart. Controller tests use fake time and focus events. End-to-end browser tests check actual input delivery, visible output and repeated restart/teardown. Physical owners are defined by layout rather than this logical model.

## Enhanced core responsibilities

- Piece source owns a FIFO of shuffled bags, each containing every kind once; deterministic shuffle input is injectable. It supplies consume and inspect operations without exposing mutable queue storage.
- Rotation policy consumes current kind/orientation/anchor and candidate legality, then returns the first legal kicked quarter-turn or unchanged placement. Ordered tables are authoritative in SPEC's rotation contract.
- Landing projection takes board and active placement and returns the last legal row reached by repeated ordinary downward movement; it does not write state. Both ghost and hard-drop commands consume this result.
- Session engine extends its state with held kind or null, Hold-used flag, grounded-contact timestamp/elapsed and the gravity interval captured at contact. Hold reinitializes orientation/anchor and piece timers, while a real lock resets Hold availability.
- View snapshot includes held kind, Hold availability and projected ghost cells. Controller maps C/Shift to Hold and Space to hard drop, with discrete-action repeat suppression.

Hold, drop and timer interactions are session rules, not independent services. Unit tests isolate bag permutations, kick order, projection and lock deadline; integration checks prove the displayed ghost agrees with the actual hard-drop result and Hold cannot bypass once-per-lock restriction.

## Ability and projection collaboration

- Session engine owns bonus count 0–3 and active penetrator flag. Its lock/clear transaction grants a bonus only for a four-line clear. Activation atomically spends one bonus and marks the current piece; Hold is rejected while marked.
- Geometry exposes bounds and ordinary empty-placement predicates separately. Landing projection scans legal settlement candidates from the current anchor to the lowest in-bounds anchor and returns the deepest valid one for penetrators; ordinary projection retains E02 behavior.
- Engine movement uses the active mode's traversal predicate, accepting overlap only when a legal destination remains at or below the candidate. A target change cancels or preserves grounded timing according to actual grounded state. Occupied intermediate cells are never written back or destroyed.
- Renderer consumes active ability and projected cells, drawing the live penetrator and its ghost in white with distinguishable treatment. The status/control view consumes bonus count and Hold-disabled state; neither owns a parallel ledger.

Deterministic fixtures cover cavities beneath blockers, invalid bottom rows, rejected geometry with no valid future settlement, activation from a grounded regular piece, Hold rejection without mutation, capped awards and legal lock. Browser scenarios exercise bonus earn/spend indication and actual white movement through a stack before legal settlement.
