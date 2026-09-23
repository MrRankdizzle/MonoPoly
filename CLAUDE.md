# MonoPoly — project rules

MonoPoly is a gamified 9th/10th-grade biology learning game (macromolecules: carbs,
proteins, lipids, nucleic acids), built as a parody of the board-game *concept* only.

## Hard constraints — do not violate these

- **One self-contained `index.html`.** No build step, no frameworks that need
  compiling, no bundler, no external JS/CSS files. The only other file this repo
  should ever contain is this `CLAUDE.md`. Google Fonts `<link>` tags are the one
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
  etc.). The "MonoPoly" title is the project's own pun name, not the trademarked
  logo/mark — keep it that way (wordmark only, no imitation of the real logo).
- **Progress persists in `localStorage`, keyed by player name**, wrapped in
  `try/catch` (see `loadStore()` / `saveStore()`). There's a "reset my progress"
  option in the in-game menu (`doReset()`) that clears only the current player's
  save. Never let a storage failure (private browsing, quota) throw — degrade to
  in-memory state silently.

## The three screens

`UI.screen` is one of `'onboard' | 'home' | 'lab' | 'board'`. `renderApp()` is the
single dispatcher — it fully rebuilds `#app` on every screen/overlay/popup change
(cheap enough at this DOM size; the one thing that must NOT trigger a full rebuild
is a running countdown timer, which pokes its own bar/text directly — see
`tickChallenge()`).

- **Home** (`homeHTML()`) — two cards, Learn Lab / Play MonoPoly. Reachable
  anytime via the header nav pills or by clicking the "MonoPoly" title.
- **Learn Lab** (`labScreenHTML()`) — the *restored original* teaching layout:
  tabs, monomer shelf, bench (tap-to-bond/tap-to-break), water counters, the
  reaction log, the original per-tab `CHALLENGES` list (not the same thing as
  `MODULES`, see below), the cheat-sheet/hook, and the "I can..." learning
  targets. This is the primary teaching surface — students are meant to start
  here, not on the board.
- **Play MonoPoly** (`boardScreenHTML()`) — the board game layer built on top
  of the same lab engine.

## How the file is organized (script is one big IIFE, in numbered PARTs)

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
   cheat-sheet) plus the learning-targets section, matching the original layout.
3. **Game config** — `CFG` (all ATP amounts + thresholds, tunable in one place),
   `TEACHER_PASSCODE`, `SQUARES` (the 20-square board loop) + `SQ_GRID` (their
   positions on a 6×6 perimeter grid), `MODULES` (the 12 property lessons: hook
   + guided-build goals + quiz), the restored original `CHALLENGES` (the Learn
   Lab's open-ended per-tab challenge list — distinct from `MODULES`), `ENZYME_POOL`,
   `DENATURE_QUESTIONS`, flavor-text pools.
4. **Persistence + sound** (`STORE`/`PLAYER`, `SOUND_ON`, WebAudio `tone()`).
   `normalizePlayer()` is the migration path — every field added after a
   player's save was first created gets a default there; it never touches
   existing progress (atp/owned/pos/etc).
5. **App shell** — onboarding screen, board grid rendering, dice roll + token
   movement + landing dispatch.
6. **Module + enzyme-challenge overlay** — both share `labBenchHTML()`. The
   enzyme challenge's countdown duration is timer-setting-aware (see below).
7. **Popups** — denaturation question (forced answer, no free close-X, retries
   always allowed), water break, corner bonus, menu, reset confirm, how-to-play.
8. **Shared header/nav, Home screen, Learn Lab screen, Settings** — `headerHTML()`
   is used by all three main screens; `labScreenHTML()`/`labTabsHTML()` render
   the restored Lab; `labChallengeCheck()`/`renderLabChal()` drive the Lab's
   challenge list and award ATP; the Settings popup (`settingsBodyHTML()`) holds
   the question-timer choice (Off/Relaxed/Standard), read by `effectiveEnzymeMs()`.
9. **Event wiring + init** — one delegated `click` listener; everything else is
   a plain function call, no framework.

## ATP economy (tunable via the `CFG` object near the top of PART 3)

These numbers were **not specified** in the original request (the spec cut off
mid-sentence on "Earn ATP...") — they're a reasonable default economy, not a
locked-in design. Adjust freely in `CFG`:

- Pass/land on GO: 60 / +25 landing-exactly bonus
- Each guided-build goal completed: 10
- Each quiz question: 15 first-try, 5 on a later try (unlimited retries either way)
- Buying a property (finishing its module): +25 bonus on top of the above
- Owning all 3 properties in a color group: +50
- Water Break / ATP Synthase Spin corner: 20 each, unlimited revisits
- Denaturation Station: 15 (must answer correctly — retries are free and unlimited)
- Enzyme timed challenge: 30 on success, 5 consolation on timeout/give-up
- Each Learn Lab challenge (the original per-tab `CHALLENGES` list): 20, feeds
  the same wallet as the board

Question timers (`PLAYER.timerSetting`, Settings popup): **Off** by default —
enzyme challenges become untimed and always pay the full win amount; **Relaxed**
doubles `CFG.ENZYME_MS`; **Standard** uses it as-is. `effectiveEnzymeMs()` is the
single place this is resolved — route any new timed mechanic through it (or a
sibling using the same pattern) rather than reading `CFG.ENZYME_MS` directly.

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
