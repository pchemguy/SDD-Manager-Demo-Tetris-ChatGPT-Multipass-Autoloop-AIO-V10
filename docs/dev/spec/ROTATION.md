# Quarter-turn wall kicks

This contract refines E02 in [SPEC](../SPEC.md). Orientations advance 0 → R → 2 → L → 0 clockwise. Compute the target orientation using B01 matrices, then try the following anchor offsets in order. Coordinates are (dx,dy), with positive y downward. The first legal candidate wins atomically; if all fail, neither orientation nor anchor changes. No 180-degree action is provided. O rotates without translation and its occupied cells remain fixed.

| Transition | J/L/S/T/Z offsets | I offsets |
| --- | --- | --- |
| 0→R | (0,0),(-1,0),(-1,-1),(0,2),(-1,2) | (0,0),(-2,0),(1,0),(-2,1),(1,-2) |
| R→0 | (0,0),(1,0),(1,1),(0,-2),(1,-2) | (0,0),(2,0),(-1,0),(2,-1),(-1,2) |
| R→2 | (0,0),(1,0),(1,1),(0,-2),(1,-2) | (0,0),(-1,0),(2,0),(-1,-2),(2,1) |
| 2→R | (0,0),(-1,0),(-1,-1),(0,2),(-1,2) | (0,0),(1,0),(-2,0),(1,2),(-2,-1) |
| 2→L | (0,0),(1,0),(1,-1),(0,2),(1,2) | (0,0),(2,0),(-1,0),(2,-1),(-1,2) |
| L→2 | (0,0),(-1,0),(-1,1),(0,-2),(-1,-2) | (0,0),(-2,0),(1,0),(-2,1),(1,-2) |
| L→0 | (0,0),(-1,0),(-1,1),(0,-2),(-1,-2) | (0,0),(1,0),(-2,0),(1,2),(-2,-1) |
| 0→L | (0,0),(1,0),(1,-1),(0,2),(1,2) | (0,0),(-1,0),(2,0),(-1,-2),(2,1) |

Acceptance fixtures cover every transition, both piece groups, successful wall/floor kicks, first-legal priority, total rejection and unchanged O occupancy. Ordinary legality still forbids any occupied cell outside the board or overlapping settled cells. A kick that changes grounded status is reported to the session engine's lock-timing policy.
