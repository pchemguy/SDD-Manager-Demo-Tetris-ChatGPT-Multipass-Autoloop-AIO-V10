# Browser Tetris

## Purpose and users

Build a playable single-player Tetris game for desktop browser users who want a short keyboard-controlled game without an account or server. A player can open the game, understand its controls, play until game over and immediately start another game.

## Intended outcome

Provide responsive, predictable falling-piece gameplay with a readable board, score and game-over feedback. Keep gameplay rules independently testable and make the game deliverable as static browser assets.

## Principal scope

- A 10-column by 20-row visible board and the seven standard tetromino shapes.
- One falling piece at a time, automatic downward gravity, left/right movement, player-controlled rotation and accelerated downward movement.
- Collision and board-boundary checks; a piece settles after a full gravity interval of grounded lock delay.
- Complete horizontal rows clear together, remaining rows fall, and the score reflects the number of lines cleared. Gravity accelerates as progress increases.
- Game over when the next piece cannot spawn in a valid position. Restart clears the board, score and progression and starts a fresh game.
- Visible score, progression, controls, game-over state and restart control. Gameplay keys do not scroll the page while the game has focus.

Pieces use shuffled seven-piece bags. Rotation tries a defined ordered set of wall kicks. Hold allows one exchange per piece lifetime; a ghost previews the landing, and hard drop moves immediately to it while following grounded lock delay. These controls support deliberate placement without changing settled cells before lock.

## Delivery constraints

Use TypeScript, a Canvas game board and semantic HTML status and controls, with no application backend. Keep simulation time and piece selection controllable for deterministic automated checks. Production assets run in the browser without a server-side runtime.

Use keyboard input as the primary interaction. Keep instructions and status readable, distinguish regular pieces using nonwhite colors, and give interactive HTML controls visible focus. Freeze gameplay time when the page is inactive so returning does not unexpectedly advance the game.

Verify core rules and representative gameplay in isolated Chromium headless shell and Playwright-managed Firefox environments, following the repository browser policy. Exact build/test package choices and input mappings remain for architecture and specification.

## Non-goals

Online multiplayer, accounts, backend leaderboards and mobile touch controls are outside this project's intended scope. No third-party Tetris branding or proprietary game assets are required.

## Success criteria

A user can start a game, move and rotate pieces, let gravity settle them, clear rows, observe score and speed progression, reach game over and restart without reloading. Rules remain consistent at walls, floor, stacked cells and spawn boundaries. Automated rule tests and browser interaction/rendering checks demonstrate that behavior; document preparation alone establishes no implementation claim.

## Ghost visibility

The ghost is visible when its complete projected piece lies in the lower half of the board. Hide it when any projected occupied cell is above the midline, including a projection that straddles the boundary. This changes visual assistance without changing the piece's landing or movement.

## Penetrator ability

A four-line clear earns one penetrator activation, with at most three stored. A hotkey spends one bonus to let the current piece pass through settled cells until it locks in a fully in-bounds, nonoverlapping position. Hold is unavailable while this ability is active. Hard drop and ghost use the deepest valid destination below blocking cells. Show the active penetrator entirely white, keep ordinary pieces nonwhite, and display one side-panel symbol per available bonus. Restart clears stored bonuses and active ability.
