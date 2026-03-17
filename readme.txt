================================================================================
  CELEBRATING CHRISTINE — Interactive Crossword Puzzle
  A personalized birthday gift crossword puzzle web app
================================================================================

OVERVIEW
--------
A single-page, browser-based crossword puzzle built as a 40th birthday gift
for Christine, created by Peter Kirschmann. The puzzle loads from a standard
iPuz format file and provides a full-featured solving experience including
keyboard navigation, answer checking/revealing, a timer, and a birthday
celebration sequence when the puzzle is solved.

Hosted as a static site via GitHub Pages with a custom domain (CNAME file).
GitHub Repository: https://github.com/pkirschmann/celebratingchristine


FILE STRUCTURE
--------------
celebratingchristine-main/
│
├── index.html          — Main HTML shell; all UI structure lives here
├── puzzle.ipuz         — The crossword puzzle data (grid + clues + solution)
├── crossword-logo.png  — Crossword grid icon used in the intro overlay
├── FKCake.gif          — Animated cake GIF shown in the birthday completion screen
├── favicon.ico         — Browser tab icon
├── CNAME               — Custom domain config for GitHub Pages
│
├── css/
│   └── style.css       — All styles; uses CSS custom properties for theming
│
└── js/
    └── app.js          — All application logic (no dependencies, vanilla JS)


HOW IT WORKS
------------
1. On page load, an intro overlay (index.html #intro-overlay) obscures the
   puzzle with a blurred backdrop. The timer does NOT start yet.

2. The user clicks "Play" → the intro dismisses and the timer starts.

3. app.js fetches puzzle.ipuz, parses it, builds the grid DOM, and populates
   the clue lists. The puzzle title is read from the iPuz file's "title" field.

4. The user solves the puzzle. On completing a word, the cursor automatically
   jumps to the first empty cell of the next incomplete clue.

5. When every cell matches the solution, the birthday celebration fires:
   confetti animation + "Happy Birthday" melody via the Web Audio API +
   the FKCake.gif overlay. Clicking anywhere dismisses it.


KEY FEATURES & IMPLEMENTATION NOTES
-------------------------------------
Intro Overlay (#intro-overlay)
  - Shown on page load; uses backdrop-filter: blur(6px) to obscure the puzzle
  - Dismissed by clicking "Play"; timer starts at that moment
  - Styled to match NYT Crossword intro popup aesthetic

Timer (state.timer in app.js)
  - Starts only after Play is pressed (bindIntro → startTimer)
  - Pauses automatically when the browser tab is hidden (visibilitychange event)
  - Stops on puzzle completion

Puzzle Data (puzzle.ipuz)
  - Standard iPuz JSON format: dimensions, puzzle grid, solution grid, clues
  - Clue numbers are re-derived from the grid layout in buildDerivedData(),
    NOT taken from the iPuz numeric values — this keeps numbering consistent
  - Clues stored in state.clueMap: { across: {num: {number, text, cells[]}},
                                     down:   {num: {number, text, cells[]}} }

Cursor & Navigation
  - state.cursor = { row, col, direction: 'across'|'down' }
  - Clicking a cell twice toggles direction (across ↔ down)
  - Arrow keys, Tab (next clue), Shift+Tab (prev clue), Enter (next clue)
  - advanceCursor(): after writing a letter, moves to the next empty cell in
    the word; if the word is complete, jumps to first empty cell of the next
    incomplete clue (cycles through all clues in across-then-down order)
  - Arrow key boundary navigation (handleArrowKey in app.js):
      ← at the start of an across word → last open cell of previous clue
      ↑ at the top of a down word      → last open cell of previous clue
      → at the end of an across word   → first open cell of next clue
      ↓ at the end of a down word      → first open cell of next clue
    Clue order wraps around (← at clue 1-Across → last Down clue).
    "Open" means empty/unfilled; falls back to the boundary cell if all filled.

Input Modes
  - Ink mode (default): normal black letters
  - Pencil mode: grey italic letters, toggled via toolbar ✏️ button
  - Mobile: a hidden off-screen <input> captures touch keyboard input

Check / Reveal
  - Works at letter, word, or full puzzle scope
  - Incorrect cells get a red dot indicator (CSS ::after pseudo-element)
  - Revealed cells get a purple triangle corner (CSS ::before pseudo-element)
  - cellStatus per cell: null | 'correct' | 'incorrect' | 'revealed'

Birthday Celebration (launchBirthday in app.js)
  - Triggered by checkCompletion() when all cells match the solution
  - Creates a full-screen overlay with 60 CSS-animated confetti pieces
  - Plays "Happy Birthday" using Web Audio API oscillators (no audio files)
  - Overlay click dismisses it

Responsive Design
  - Desktop: grid (60%) + clue panel (40%) side by side
  - Mobile (≤768px): stacked layout, smaller cell size (28px vs 40px)
  - CSS custom property --cell-size drives all cell dimensions


MAKING CHANGES
--------------
To update the puzzle content:
  - Edit puzzle.ipuz (change grid, solution, clues, or title field)
  - The title shown in the toolbar is read from ipuz.title

To update the intro screen:
  - Text/dates: edit index.html (#intro-overlay section)
  - Styles: edit css/style.css (#intro-overlay, #intro-box, etc.)
  - Logo: replace crossword-logo.png (72×72px works well)

To change the completion celebration message:
  - Edit the launchBirthday() function in js/app.js
  - The .bday-msg and .bday-sub divs contain the text
  - Replace FKCake.gif to change the celebration image

To change colors/theming:
  - All design tokens are CSS custom properties at the top of style.css (:root)
  - Key vars: --color-selected (yellow), --color-active-word (blue),
    --color-incorrect (pink), --color-revealed (purple)

To deploy changes:
  - Commit and push to the main branch of the GitHub repository
  - GitHub Pages serves the site automatically from the root of main


TECH STACK
----------
- Vanilla HTML / CSS / JavaScript (no frameworks, no build step)
- Google Fonts: Libre Franklin (300, 400, 600, 700 weights)
- Web Audio API for birthday music
- CSS Grid for the crossword grid layout
- CSS backdrop-filter for the intro blur effect


PUZZLE METADATA
---------------
Title:    Celebrating Christine
Date:     Tuesday, March 17, 2026
Author:   Peter Kirschmann

================================================================================
