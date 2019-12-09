Minhalma
=========
By Graham

A single-file Sternhalma (a.k.a. Star Halma a.k.a. "Chinese Checkers") game. Adapted from a fairly underwhelming two-player version I wrote for a class project some years ago.

The game is principally played by clicking first on a unit and then on the cell you wish to move to. As is customary. The game pauses after every individual player wins. Play is resumed by pressing space, or by clicking anywhere on the board or page background. This continues until the penultimate player "wins," at which point the game is over. A new game can then be started by typing `N` or by clicking the page. Board size and player status (human, computer, or disabled) can be set in the configuration modal, accessible by clicking the plus sign in the upper right. Players can also be toggled between human and computer control by clicking the colored rectangles at the top of the page during a game. If a human player is converted to AI during its turn, the computer immediately takes the turn.

Additional in-game options can be set via the keyboard, and — in a limited number of cases — via touch gesture documented in the configuration modal. Further customization regarding number of units, AI settings, &c., can be affected through URL parameters.

## Keyboard shortcuts

- `<space>`: Pause or unpause game. Required when a player has won, or the game history has been traversed between AI moves.
- `N`: This will abruptly end the previous game and start a new one. Too bad.
- `R`: Shows ranking of players who have already won the current game.
- `S`: Return to default status message, which is fairly useless but tells you how many players are still playing.
- `[+|h|?]`: Shows configuration modal and brief summary of important controls.
- `U`: Undo. Only allowed on human move. Returns game to state before same player's last move, with the move counter, &c. reset to that state.
- `<ArrowLeft>`: Go back one move in game history.
- `<ArrowRight>`: Go forward one move in game history. If this brings the game to the latest position, play resumes or, again, is placed in a paused state.
- `<PgDown|ArrowDown>`: Go to beginning of game history. Really this is only here to provide symmetry with `<PgUp>`.
- `<PgUp|ArrowUp>`: Return to latest position in game history.
- `C`: Download current game as JSON file. The columns represent player direction (e.g. number counter-clockwise from bottom), source cubic (see below), target cubic, and a flag indicating whether the player won on that move.
- `H`: Download full history of games played in the present session (i.e. since page was last reloaded).
- `,`: Rotate board counter-clockwise.
- `.`: Rotate board clockwise.
- `[1-9]`: Create new config with board size as given number and pushes to URL history. Settings do not take effect until next game is started. (This is presently the only URL parameter option available from the keyboard.)

## Parameters

The board size, &c. can be modified by appending parameters to the page URL in your browser. Note that this will clear the current game and history.

- `size`: Set board size in hexes between center and inner radius, or the equivalently the number of "rows" per triangle. The default is 3, for performance reasons, while standard "Chinese Checkers" is 4.
- `units`: Set number of units. Defaults to triangular fill `size * (size + 1) / 2` (e.g. 2 -> 3, 3 -> 6, 4 -> 10). Can be overfilled, but do not set to more than half the outer row width with all six players enabled, or units will be overwritten.
- `players`: String indicating player types. Omitted trailing characters will leave those players disabled, while any characters > 6 will be ignored.
    - 0: Disabled
    - 1: Computer
    - 2: Human
- `depth`: AI parameter for max search depth. Default is 6, for one full rotation with all players.
- `skip`: AI parameter representing score differential threshold at which a move will be discarded without further exploration. A lower number substantially increases speed on large boards. Numbers above ~5 are effectively meaningless. (Default is 1.)
- `bonus`: AI parameter representing move score incentive multiplier for farthest back unit(s). (Default is 2.)

Some examples:

- `?size=2&units=6&players=2001`: A two-player game, human v. AI, where each side has six units.
- `?size=1&units=2`: A six-player game on a ridiculously small board where each player has two pieces. By going first you will lose.

To start a differently-configured game in the same session, create a `Config` instance in the browser console, passing the parameter you wish to set directly &mdash; e.g.:

`new Config({size: 4})`

This will be saved to `Config.instance` and used for the next new game. If the configured settings differ from the current ones, a new location will be pushed on to the page history (i.e., your browser URL bar will change.) This mechanism is also used when board size, &c. are set from the configuration modal.

## Some additional information

The cells use a somewhat-overbuilt coordinate system based on the "cubic" model described by Amit Patel in his excellent [Hexagonal Grids](https://www.redblobgames.com/grids/hexagons/) article. The gist is that each coordinate is described by three values that I usually label `u`, `v`, and `w`, which sum to 0 and can be thought of as defining a plane intersecting a cube in such a way as to express a hexagonal cross section. (See also the Wikipedia article on [permutohedra](https://en.wikipedia.org/wiki/Permutohedron) for a visualization of this.) These coordinates are particularly helpful in a game like this where true sixfold symmetry in e.g. calculating move cost &c. is desirable.

I have implemented a slight variation on the "[Masters Rule](https://www.mastersofgames.com/rules/chinese-checkers-rules.htm)" to avoid spoiling (i.e., adversarial unit blocking one's goal triangle). In the original version, a player is allowed to treat any target cell in their triangle as open, and simply swap a unit there into their unit's last position. In the present version I have applied this only to immediate-neighbor moves, without hops. This was principally to simplify the move-generating algorithm, but to be honest I didn't actually try it the other way, and it might work fine. Who even knows. I am also not entirely sure how the rule is supposed to work when e.g. there are units blocking multiple cells in a hop path.

## Distribution

This project was made possible in partnership with the Global Hexagonal Awareness Project, and is distributed under the Hexagonal Awareness License (see source).