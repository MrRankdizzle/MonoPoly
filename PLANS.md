# Mustang MonoPoly — roadmap

One phase at a time. Each phase is built, previewed with `open index.html`, and
only committed once Mr. Rankin approves it. Update this file as phases finish.

| Phase | What | Status |
|---|---|---|
| A2 | Full-width board, property art, 3D dice, card system, Lab review nudges | ✅ Done |
| B | Our standard, proficiency levels, "Meet the Big Four" | ⏳ Next |
| C | Nucleic acids upgrades: double helix, ATP/ADP station | Planned |
| D | Gamified questions | Planned |

---

## Phase A2 — board redesign ✅

Replaced the Phase A side panel. Full-width rectangular board with a large
center (decks, two 3D dice, ATP wallet + rank, Learn Lab button, activity
ticker); ownership shown as deed banners on the squares; original inline-SVG
art for every square; the token hops square by square; deed-style cards fly
out of the Property/Enzyme decks over a dimmed (optionally blurred) board;
wrong answers nudge to the exact Lab spot and return to the same card; flying
ATP coins + confetti. GO payout halved (30 / +12) to balance the two dice.

---

## Phase B — our standard, proficiency levels, and "Meet the Big Four"

**Standard** (replace any remaining "I can" targets with this, word for word):

> **1.2 | Biological Molecules:** "I can compare and contrast the four main
> types of biomolecules that comprise the body: carbohydrates, lipids, nucleic
> acids and proteins." [HS-PS1-1]
>
> **Level 1:** I can identify different biological molecules (polymers) and
> their respective building blocks (monomers).
>
> **Level 2:** Additionally, I can describe the chemical formation and
> separation of biological molecules, and use this to explain the relative
> amounts of energy stored in different biomolecules.
>
> **Level 3:** Additionally, I can describe the functions of the different
> biological molecules.

**Level tagging.** Tag every Lab challenge, module quiz, Enzyme Card, and
Showdown question:

- **Level 1:** identifying polymers and their monomers, straightforward builds,
  Element Detective, naming.
- **Level 2:** formation and separation (dehydration synthesis, hydrolysis,
  water released or used, digestion) and relative energy (Calories per gram,
  why lipids store more, C–H bonds).
- **Level 3:** functions (short- and long-term energy storage, membranes,
  enzymes, structure, transport, genetic information). Saturated vs.
  unsaturated, phospholipids, and order-matters proteins count when framed
  around what the molecule does.
- Every molecule needs at least 2 Level 3 items. Add function-focused ones
  where needed (e.g., "Why do bears store fat instead of glycogen for winter?").
- Leave the tag for each item as an inline comment so it can be re-tagged.
- Report Card shows progress by level (e.g., "Level 1: 8 of 10").

**"Meet the Big Four"** — a new first Learn Lab tab, and the default landing
tab for new players:

- A side-by-side comparison of all four groups.
- Elemental composition: carbohydrates CHO, lipids CHO (phospholipids add P),
  proteins CHON (some S), nucleic acids CHONP. Teach the trick: carbs are about
  1 C : 2 H : 1 O, while lipids have far less oxygen and many more C–H bonds.
- Energy per gram: carbohydrates ~4 Calories/g, proteins ~4, lipids ~9.
  Nucleic acids aren't used as fuel. Connect to Level 2: more C–H bonds means
  more stored energy.
- Percent of the body's dry weight for each group.
- All of these numbers live in one `BIG_FOUR_STATS` constant near the top,
  marked "approximate, verify against Mr. Rankin's notes."
- Interactive activities: an "Element Detective" classify-the-formula drill
  (including telling carbs from lipids by oxygen ratio), an energy-per-gram
  comparison with real foods, and a simple body-composition chart.
- Its challenges earn ATP like the other tabs, tagged mostly Level 1 and 2.
- Once it exists, point the relevant wrong-answer review buttons at it.

---

## Phase C — nucleic acids upgrades (Learn Lab nucleic acids tab)

**A. Double helix**

- Students select two DNA strands of equal length and tap "Zip into a double
  helix."
- If every base pairs (A–T, G–C), the strands zip with an animation, showing
  hydrogen bonds as dashed lines (2 for A–T, 3 for G–C), then twist into a
  simple helix view. Label the 5′ and 3′ ends and note the strands run in
  opposite directions.
- Mismatches get highlighted, with the correct partner base shown.
- An "Unzip" option separates them.
- Key teaching point, called out in the equation log: pairing and unzipping use
  hydrogen bonds, NOT dehydration synthesis or hydrolysis, so no water is
  released or used and the water counters don't change.
- RNA can't zip; explain that RNA is usually single-stranded.

**B. ATP and ADP station**

- ATP = adenine + ribose + 3 phosphates; ADP has 2.
- "Spend energy": ATP + water → ADP + phosphate (hydrolysis), with an energy
  burst.
- "Recharge": ADP + phosphate → ATP + water (dehydration synthesis). It
  requires energy, which comes from breaking down food in cellular respiration.
- Both reactions update the water counters and the equation log.
- Tapping the ATP wallet anywhere shows a short explainer that ATP is the
  cell's energy currency and that spending it means hydrolysis to ADP. Connect
  it to the ATP Synthase Spin square if that fits.
- Add energy transfer (ATP) to the nucleic acids cheat sheet.

**C. New items (tagged by level)**

- Lab challenges: "Zip two strands into a double helix" (L3), "Spend an ATP"
  (L2), "Recharge ADP back to ATP" (L2).
- At least one ATP/ADP question and one base-pairing question in the nucleic
  acids module quizzes and the Showdown, e.g., "Why doesn't zipping two DNA
  strands release water?" (L2) and "Which molecule does your cell spend for
  quick energy?" (L3).

---

## Phase D — gamified questions (after Phase B's level tags exist)

- **Property upgrades tied to proficiency levels:** a Level 1 question buys a
  property; returning later and answering a Level 2 question adds a house; a
  Level 3 question upgrades it to a Polymer Plant (hotel). Show houses and
  plants on the board squares (on the color band). Owning the property also
  charges "rent" in ATP when landed on again.
- **More question types:** multiple choice; quick builds on the card using the
  lab engine; sort-it (drag molecules or monomers into the correct group);
  Myth or Fact; Odd One Out; and "What am I?" riddles where clues reveal one at
  a time, with fewer clues used earning more ATP.
- **Streak meter:** consecutive first-try correct answers build a multiplier
  (x2, x3), with a visible flame that resets gently on a miss.
- **Lifelines,** each usable once per lap: "Enzyme Cut" removes two wrong
  answers, and "Peek at the Lab" shows the relevant cheat-sheet row for 5
  seconds.
- **Optional confidence wager** before answering (Low, Medium, or High, with a
  small cap) to reward students who know they know it. Losses stay small; this
  should feel encouraging.
- **Report Card stats:** houses and Polymer Plants owned per level.
