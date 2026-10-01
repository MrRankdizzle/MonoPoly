# Mustang MonoPoly — project rules

Mustang MonoPoly is a gamified 9th/10th-grade biology learning game (macromolecules:
carbs, proteins, lipids, nucleic acids), built as a parody of the board-game *concept*
only, skinned in the school's colors and logo.

## Hard constraints — do not violate these

- **One self-contained `index.html`.** No build step, no frameworks that need
  compiling, no bundler, no external JS/CSS files. The only other files this repo
  should ever contain are this `CLAUDE.md` and `PLANS.md` (the phase roadmap —
  update it as each phase is completed). Google Fonts `<link>` tags are the one
  exception to "no external resources" (already in use for Lexend).
- **Never start a dev server. Never use the Vercel CLI or any deploy CLI.** The
  user previews changes locally with `open index.html` and deploys only via
  `git push`. Don't suggest `npm run dev`, `vercel`, `python -m http.server`, etc.
  (A short-lived headless-Chrome check purely to catch JS errors before handing
  work back is fine — that's not a server and nothing gets deployed by it.)
- **Chromebook/phone-friendly.** No layout that requires a mouse, hover-only
  affordances, or a large viewport. Respect `prefers-reduced-motion` (the dice
  spin, token movement, water-drop animation, and confetti burst all already
  check `reduceMotion` — extend that pattern for any new animation).
- **Sound is off by default.** `SOUND_ON` loads from `localStorage`
  (`monopoly_bioquest_sound`) and defaults to `false`. All sound effects are
  synthesized with the WebAudio API (`tone()` / `playSfx()`) — no audio files.
- **Parody only.** Original art, original square names, original styling. No
  Monopoly logos, mascots, fonts, or card names ("Chance", "Community Chest",
  etc.). "Mustang MonoPoly" is the project's own name, not the trademarked
  Monopoly logo/mark — keep it that way. The actual school logo (`LC Logo.png`,
  not committed — it's embedded as a data URI in `index.html` via `LOGO_DATA_URI`,
  see below) is fair use of the school's own mark for the school's own tool.
- **School colors are chrome-only.** `--brand` (navy) / `--brand-blue` (sky
  accent), both sampled from the actual logo file — see "Branding" below. These
  color the app shell (header, nav pills, board frame/center panel, Report
  Card). The four molecule colors (`--carbs`/`--proteins`/`--lipids`/`--nucleic`)
  are unrelated and must stay distinct from the brand blues — that's *why*
  `--nucleic` is indigo/purple (`#7c5cd6`) instead of the blue it started as.
- **Progress persists in `localStorage`, keyed by player name**, wrapped in
  `try/catch` (see `loadStore()` / `saveStore()`). There's a "reset my progress"
  option in the in-game menu (`doReset()`) that clears only the current player's
  save. Never let a storage failure (private browsing, quota) throw — degrade to
  in-memory state silently.

## The screens

`UI.screen` is one of `'onboard' | 'home' | 'lab' | 'board' | 'report'`.
`renderApp()` is the single dispatcher — it fully rebuilds `#app` on every
screen/overlay/popup change (cheap enough at this DOM size).

- **Home** (`homeHTML()`) — two cards, Learn Lab / Play MonoPoly, plus the
  school logo. Reachable anytime via the header nav pills or by clicking the
  brand/logo.
- **Learn Lab** (`labScreenHTML()`) — tabs in `LAB_TAB_ORDER`: **Meet the Big
  Four** (`LAB.tab === 'bigfour'`, first tab and the landing tab for new/reset
  players) then the four bench tabs. A bench tab is the *restored original*
  teaching layout: monomer shelf, bench (tap-to-bond/tap-to-break), water
  counters, the reaction log, the per-tab `CHALLENGES` list (not the same thing
  as `MODULES`), and the cheat-sheet/hook. The Big Four tab has no bench
  (`bigFourHTML()`/`renderBigFour()`, PART 8b): a side-by-side comparison
  (`#b4-table`), elements + the oxygen-ratio trick (`#b4-elements`), the
  Element Detective drill (`#b4-detective`, `DETECTIVE_FORMULAS`), energy per
  gram + food lab (`#b4-energy`), and the body-composition chart (`#b4-body`).
  Its activities fire `afterAction({type:'detective'|'energy'|'body', ...})`
  into the same `labChallengeCheck` as the bench tabs. Every tab ends with our
  standard (`standardHTML()`, text in `STANDARD`, word for word — it replaced
  the old "I can" learning targets). This is the primary teaching surface —
  students are meant to start here, not on the board.
- **Play MonoPoly** (`boardScreenHTML()`) — no side panel. The board is a 6×6
  perimeter grid stretched into a wide rectangle spanning the full `.wrap`
  width (same as the header/Lab). `.wrap.board-wrap` is a 100svh flex column,
  so the board fills whatever height is left under the header — fully visible
  without scrolling at 1366×768 (Chromebook), capped at `min(66vw, 820px)` tall
  on big screens. Below 700px wide it becomes a tall `aspect-ratio:5/8` board
  and the page may scroll. Squares (`squareHTML()`) and the center are CSS
  size containers: square art/name lay out side-by-side when the square is
  wide (`@container (min-aspect-ratio:5/4)`) and stacked when tall. The center
  grid lives on `.bc-grid` (a container can't query itself) and holds the two
  decks (`#deckProp`/`#deckEnz`), two CSS-3D dice (`dieHTML()`, rolled by
  tapping the dice or ROLL; the sum moves the token), the ATP wallet + rank +
  deeds count, Mogul Showdown, the Learn Lab button, and a one-line ticker
  (`logActivity()` → `#tickerText`). Ownership shows on the board itself: an
  owned square gets a `.sq-deed` banner in `PLAYER.color`. The token
  (`#token`, `tokenSVG()`) hops square by square (`hopToken()`), sitting over
  each square's art. **The whole board is open from the start** — tap any
  square any time (`openSquare()`).
  **Cards:** every square's content opens as a deed-style card on a
  full-screen `.card-stage` over the board (`cardStageHTML()` →
  `cardContentHTML()` dispatches on `UI.overlay.mode`
  `'module'|'challenge'|'showdown'|'denature'|'info'`). Always open cards via
  `showCard(overlay, origin)`: `origin` is `'deck-prop'`/`'deck-enz'` (card
  flies off the deck, flips, zooms up), `'sq-<idx>'` (zooms from the square),
  a CSS selector, or `'center'`; `closeOverlay()` shrinks it back to the same
  origin. Card markup = `deedHead(band, kicker, title)` (color band from
  `BAND`, incl. the `← Board` button `#closeOverlayBtn`) + `.deed-body`, with
  `deedArt(band, artKey)`. The board behind is dimmed, and blurred only when
  `BLUR_ON` (Settings toggle; defaults on only for >4 cores and >4GB, since
  blur can lag on low-end Chromebooks).
  **Wrong answers** (module quiz, Denaturation, Showdown MC/riddle) show the
  explanation plus `nudgeHTML(review)` — a button to the exact Lab spot from
  `QUIZ_REVIEW`/`DENATURE_REVIEW`/`SHOWDOWN_REVIEW` (`{tab, focus, text}`;
  focus = `shelf|bench|challenges|hook|targets|facts:<cheat-sheet row>`).
  `goReview()` saves the open card in `UI.returnTo`, opens the Lab, scrolls to
  and highlights that spot; the Lab shows a "Back to my card" bar
  (`returnToCard()`, also triggered by the Play nav pill). Cards keep their
  place across the trip: `o.quiz.{idx,attempts,wrong}` for modules, `o.wrong`
  for Denaturation/Showdown, and Showdown only resets first-try state when
  `o.idx` changes (`o.mountedIdx`) — so leaving to review never resets the
  "first try" bonus. Menu/Settings/how-to-play/reset-confirm stay on the
  separate `UI.popup` modal system (`popupHTML()`, z-index above the card stage).
  **ATP:** `awardATP(n, srcEl)` updates `PLAYER.atp` immediately but the
  displayed wallets (`shownATP()` = atp − `UI.atpInFlight`) count up when the
  flying coins land; `celebrate()` is the confetti (property purchase, set
  bonus, enzyme win, Showdown finish). All of it, plus dice tumble, token
  hops, and card fly/zoom, is skipped under `reduceMotion`.
- **Report Card** (`reportCardHTML()`, `UI.screen === 'report'`, reached via the
  ☰ menu) — ATP total, progress by proficiency level (`levelReportHTML()`:
  "Level N: x of y" plus a level × group table, built from `levelItems()`),
  per-group ownership + Lab-challenge counts, and Mogul Showdown best/last
  stars + badge.

## Proficiency levels (standard 1.2)

Every Lab challenge, module quiz question, Enzyme Card, Showdown round, and
Denaturation question has `lv:1|2|3` with an inline `/* L#: why */` comment —
the user re-tags by editing those. L1 = identify polymers/monomers, builds,
naming, Element Detective; L2 = formation/separation (dehydration synthesis,
hydrolysis, water, digestion) + relative energy (Calories/g, C–H bonds);
L3 = functions. Every molecule must keep at least 2 Level 3 items. Completion
is tracked per item: `PLAYER.labChallenges`, `PLAYER.quizDone`,
`PLAYER.enzymeDone[id]` (Enzyme Cards need a stable `id`),
`PLAYER.showdownDone[index]`, `PLAYER.denatureDone[index]` — so only ever
*append* to `SHOWDOWN_ROUNDS`/`DENATURE_QUESTIONS`/a module's `quiz`, never
reorder. When a module gains a question after a student bought it, the owned
card shows the new check (bonus ATP, no repurchase), and `normalizePlayer()`
pads `quizDone`. Levels show as `lvlChip(lv)` on challenges, quiz checks,
Enzyme Cards, Denaturation, and in the Showdown kicker. Each quiz question also
needs a matching `QUIZ_REVIEW[id][i]` wrong-answer target (several point into
Big Four via `focus:'#b4-…'`).

## How the file is organized (script is one big IIFE, in numbered PARTs)

0. **Top of the script** — `BIG_FOUR_STATS` (every number on the Big Four tab:
   elements, Calories/g, % dry weight, foods; marked "approximate, verify
   against Mr. Rankin's notes"), `DETECTIVE_FORMULAS`, `STANDARD`.
1. **Macromolecule data + SVG tile builders** — reused verbatim from the original
   MonoPoly lab prototype (carbs/proteins/lipids/nucleic acid data, `carbTile()`,
   `aaTile()`, `nucTile()`, `lipidSVG()`, naming functions like `nameCarb()`).
2. **Lab engine** (`LAB` state + `addLinear`/`breakBond`/`addLipid`/`breakLipid`/etc.
   + `renderLab()`) — this is the build/break dehydration-synthesis/hydrolysis
   mechanic from the original prototype, tab-scoped (`carbs`/`proteins`/`lipids`/
   `nucleic`). Every action funnels through `afterAction(ev)`, which forwards an
   event descriptor (`{type:'syn'|'hyd'|'place'|'digestedTri'|'comp', len}`) to
   whatever `LAB.onGoalProgress` is currently pointed at — the Learn Lab screen
   points it at `labChallengeCheck`, a module overlay points it at
   `refreshModuleGoals`, an enzyme challenge points it at `checkChallengeWin`.
   The shelf+bench markup itself is `shelfBenchHTML()`; `labBenchHTML()` wraps
   it with the water ledger and a 2-column log/cheat-sheet row for overlays,
   while the Learn Lab screen assembles its own 3-column row (log/challenges/
   cheat-sheet) plus the standard section, matching the original layout.
3. **Game config** — `CFG` (all ATP amounts, tunable in one place), `LOGO_DATA_URI`
   (the embedded school logo, right after `const INK`), `SQUARES` (the 20-square
   board loop) + `SQ_GRID` (their positions on a 6×6 perimeter grid), `MODULES`
   (the 12 property lessons: hook + guided-build goals + quiz), the restored
   original `CHALLENGES` (the Learn Lab's open-ended per-tab challenge list —
   distinct from `MODULES`), `ENZYME_POOL`, `DENATURE_QUESTIONS`, `SHOWDOWN_ROUNDS`,
   flavor-text pools.
   **3b. Board art** — `ART` (one original inline-SVG illustration per property
   plus `go`/`denature`/`water`/`enzyme`/`spin`/`trophy`/`house`, all on a 64×64
   viewBox in one style: thick `INK` outlines, flat fills, one highlight; no
   emoji anywhere on the board or cards), `artSVG(key)`, `squareArtKey()`,
   `tokenSVG(color)`, `deckBackHTML()`, `BAND` (card band colors),
   `PLAYER_COLORS`, and the wrong-answer review maps (`QUIZ_REVIEW` etc.).
4. **Persistence + sound** (`STORE`/`PLAYER`, `SOUND_ON`, WebAudio `tone()`).
   `normalizePlayer()` is the migration path — every field added after a
   player's save was first created gets a default there, and fields from removed
   features get `delete`d; it never touches existing progress (atp/owned/pos/etc).
   `init()` calls `saveStore(STORE)` right after normalizing so a migration is
   written back immediately, not just held in memory until the next action.
   `PLAYER.color` (token + deed-banner color) is migrated in there too.
   `BLUR_ON` (`monopoly_bioquest_blur`) is a per-device preference like sound.
5. **App shell** — onboarding, board + center rendering, dice, token hops,
   landing dispatch, the card stage (`showCard`/`animateCardOpen`/
   `animateCardClose`), flying ATP coins, confetti.
6. **Module + enzyme-challenge card content** — both share `labBenchHTML()`.
   Untimed: an enzyme challenge just waits for the bench to satisfy
   `challenge.test()`, or the student taps "Skip this one" for a small
   consolation ATP (`resolveChallenge(false)`).
7. **Card open/close, special-square cards, Lab review round-trip, and the
   remaining `UI.popup` modals** — `closeOverlay()`, `goReview()`/
   `applyReviewFocus()`/`returnToCard()`, `infoPanelHTML()`,
   `denaturePanelHTML()`/`denatureAnswer()` (retries always allowed);
   `menuBodyHTML()`/`settingsBodyHTML()`/`confirmResetBodyHTML()`/
   `howtoBodyHTML()` stay on the true-modal `UI.popup` system.
8. **Shared header/nav, Home screen, Learn Lab screen, Big Four (8b), Report
   Card, Settings** — `headerHTML()`
   is used by all main screens; `labScreenHTML()`/`labTabsHTML()` render
   the restored Lab; `labChallengeCheck()`/`renderLabChal()` drive the Lab's
   challenge list and award ATP; the Settings popup (`settingsBodyHTML()`) holds
   the sound toggle, the blur toggle, "Reset my progress", and the game-piece
   color swatches (`data-popact="toggleSound"`/`"toggleBlur"`/`"confirmReset"`/
   `"color:<hex>"`).
9. **Mogul Showdown** (`SHOWDOWN_ROUNDS`, `openShowdown()` → `UI.overlay.mode ===
   'showdown'`) — 12 rounds (3 per macromolecule: one `kind:'build'` reusing the
   lab engine, one `kind:'mc'`, one `kind:'riddle'` — mc/riddle share one answer
   path, `choices`+`correct`, riddles just add a `clues` array), order shuffled
   per attempt in `openShowdown()`. No lives, untimed: a wrong MC/riddle answer
   disables that option, lets you retry, and shows a Lab review nudge;
   a build round just waits for the bench to satisfy `round.test()`. Stars are
   1-3 based only on rounds solved correct on the *first* attempt
   (`CFG.SHOWDOWN_STARS_2`/`_3` thresholds out of 12), paid out as
   `CFG.ATP_SHOWDOWN_BASE + CFG.ATP_SHOWDOWN_PER_STAR * stars`. Results persist
   to `PLAYER.showdown` (`bestStars`/`lastStars`/`badge`/`attempts`).
10. **Event wiring + init** — one delegated `click` listener; everything else is
    a plain function call, no framework.

## Branding

- `LOGO_DATA_URI` (Part 3, right after `const INK`) is the school logo, embedded
  as a base64 PNG data URI so the app stays one file. It was cropped to its
  visible bounds and downsized (long edge 480px) from the original `LC Logo.png`
  before encoding — if the logo ever changes, re-crop/resize before re-embedding,
  don't just base64 a full-resolution export (bloats the file for no visual gain).
  Used via `<img class="brand-logo">` (header) and `<img class="home-logo">`
  (home screen + onboarding).
- `--brand` / `--brand-blue` in `:root` were sampled directly from that logo
  file's actual pixels (navy `#233766`, sky accent `#65a5e3`), not a generic
  "Carolina blue" swatch — if the logo is ever swapped, re-sample rather than
  guessing, since the instruction that produced these was "match the logo."

## ATP economy (tunable via the `CFG` object near the top of PART 3)

These numbers were **not specified** in the original request (the spec cut off
mid-sentence on "Earn ATP...") — they're a reasonable default economy, not a
locked-in design. Adjust freely in `CFG`:

- Pass/land on GO: 30 / +12 landing-exactly bonus (halved from 60/25 when
  movement went to two dice, 2–12: a lap is ~3 rolls, so GO pays out about twice
  as often — ATP should come mostly from learning, not luck)
- Each guided-build goal completed: 10
- Each quiz question: 15 first-try, 5 on a later try (unlimited retries either way)
- Buying a property (finishing its module): +25 bonus on top of the above
- Owning all 3 properties in a color group: +50
- Water Break / ATP Synthase Spin corner: 20 each, unlimited revisits
- Denaturation Station: 15 (must answer correctly — retries are free and unlimited)
- Enzyme challenge (untimed): 30 on success, 5 consolation if skipped
- Each Learn Lab challenge (the original per-tab `CHALLENGES` list): 20, feeds
  the same wallet as the board
- Mogul Showdown: 200 base + 50/star (250/300/350 total for 1/2/3 stars)

**There are no timers anywhere in this app** — removed by explicit instruction.
Don't reintroduce a countdown for a new mechanic without checking with the user
first; the standing instruction was "Enzyme Cards and Showdown rounds are simply
untimed."

## Known trade-offs (accepted, not bugs)

- Water Break, the ATP Synthase Spin corner, and enzyme squares can be
  re-tapped indefinitely for small ATP amounts (no cooldown/cap). Left as-is
  because the amounts are small relative to a property module (~65–85 ATP) and
  a classroom setting self-limits repeat-tapping; revisit if it becomes a real
  problem.
- Quiz "first try" bonus resets if a student closes a module mid-quiz and
  reopens it later, but a question already answered correctly is never asked
  again (`PLAYER.quizDone`) — so ATP can't be farmed by repeatedly leaving and
  re-entering a module, only the "first try" framing is slightly approximate
  across sessions.
