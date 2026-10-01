# Mustang MonoPoly — roadmap

One phase at a time. Each phase is built, previewed with `open index.html`, and
only committed once Mr. Rankin approves it. Update this file as phases finish.

| Phase | What | Status |
|---|---|---|
| A2 | Full-width board, property art, 3D dice, card system, Lab review nudges | ✅ Done |
| B | Our standard, proficiency levels, "Meet the Big Four" | ✅ Done |
| C | Nucleic acids upgrades: double helix, ATP/ADP station | ✅ Done |
| D1 | Property tiers, rent, new question types + questions, save migration, Report Card | ✅ Done |
| D2 | Streak meter, lifelines, confidence wagers | ⏳ Next |

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

## Phase B — our standard, proficiency levels, and "Meet the Big Four" ✅

Notes from the build:
- 76 items tagged (`lv` + inline comment). Totals: 43 at Level 1, 14 at
  Level 2, 19 at Level 3.
- New Level 3 questions: Glucose Grove (why runners eat carbs), Starch
  Boulevard (starch/glycogen storage), Glycerol Gardens (bears and fat),
  Nucleotide Knoll (RNA's job), Helix Heights (DNA's job).
- A few challenges were reworded to frame them around function (Phe–Lys–Gly–Met
  "would it fold and do the same job?", phospholipid "building block of every
  cell membrane").
- The proteins Enzyme Card "Protease patrol! Bond two amino acids" is now
  "Ribosome rush!" (proteases break bonds rather than build them), and
  "Helicase hustle" is now "Pairing practice".
- `BIG_FOUR_STATS` % dry weight values are rough (proteins ~43, lipids ~38,
  minerals/other ~15, carbs ~2, nucleic ~2). Please check them.


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

## Phase C — nucleic acids upgrades (Learn Lab nucleic acids tab) ✅

Notes from the build:
- Zipping pairs strands position by position, with the bottom strand read
  3′→5′, which matches the existing "Show matching strand" convention. A true
  reverse complement (built 5′→3′) is also accepted and flipped into place.
- Students pick strands with a "Pick for helix" button on each strand, then
  tap "Zip into a double helix". Mismatches show a live preview marking the
  base each position needs.
- Added: 3 Lab challenges (zip L3, spend L2, recharge L2); 3 module questions
  (Nucleotide Knoll "which molecule do cells spend" L3, Backbone Bay "ATP
  hydrolysis" L2, Helix Heights "why zipping releases no water" L2); 2
  Showdown rounds (L2, L3). The Showdown is now 14 rounds; star thresholds
  are 7 and 12 first-try correct.
- The nucleic cheat sheet gained "Energy transfer" (ATP) and "Base pairs" rows.
- Level totals are now 43 at Level 1, 19 at Level 2, 22 at Level 3.


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

## Phase D — gamified questions (split into D1 and D2)

### D1 — tiers, rent, question types, new questions, migration, Report Card ✅

Build notes:
- Question bank built exactly as approved below. Level totals are now 44 at
  Level 1, 26 at Level 2, 26 at Level 3, and every property has all three.
- Houses and plants need a later visit: at least one roll since the last
  upgrade (or since buying). Tapping a property before then shows "Come back
  after your next roll".
- Rent is collected only when the dice land you there, not when you tap.
- Migrated saves start at 0 rolls, so their first upgrade comes after one
  roll.

**Decisions so far**
- Rent: you collect rent when you land on your own property (5 ATP for the deed,
  10 with a house, 20 with a Polymer Plant).
- Each property's tiers draw from its own questions: **Buy** = its Level 1
  questions (after the hook + guided build, as now), **House** = its Level 2
  questions, **Polymer Plant** = its Level 3 questions. Existing L2/L3 questions
  move into the house/plant tiers.
- Save migration: a student who already answered a property's L2 (or L3)
  questions gets that house (or plant) automatically.
- "What am I?" clues reveal one at a time, and solving with fewer clues pays
  more.
- Sort-it works by tap (tap a chip, tap its group), and drag also works.

**Tier map** (✱ = new question, see the question bank below)

| Property | Buy (L1) | House (L2) | Polymer Plant (L3) |
|---|---|---|---|
| Glucose Grove | 2 existing | ✱ D1-1 | 1 existing |
| Sucrose Street | 1 existing | 1 existing | ✱ D1-2 |
| Starch Boulevard | 1 existing | 1 existing | 1 existing |
| Amino Avenue | 2 existing | ✱ D1-3 | ✱ D1-4 |
| Peptide Place | 1 existing | ✱ D1-5 | 1 existing |
| Folding Falls | ✱ D1-6 | ✱ D1-7 | 2 existing |
| Glycerol Gardens | 1 existing | 1 existing | 1 existing |
| Saturation Square | 2 existing | ✱ D1-8 | ✱ D1-9 |
| Membrane Marina | 1 existing | ✱ D1-10 | 1 existing |
| Nucleotide Knoll | 2 existing | ✱ D1-11 | 2 existing |
| Backbone Bay | 1 existing | 2 existing | ✱ D1-12 |
| Helix Heights | 2 existing | 2 existing | 1 existing |

**Question bank: new questions** (edit freely; correct answers marked ✔)

**D1-1 · Glucose Grove · House · L2 · Sort-it**
Sort each change: does it release water or use water?
- Glucose + glucose → maltose → *Releases water (dehydration synthesis)*
- Your liver links glucose into a glycogen chain → *Releases water*
- Saliva breaks starch into maltose → *Uses water (hydrolysis)*
- Digesting sucrose into glucose + fructose → *Uses water*
Explanation: Building a bond releases one water (dehydration synthesis).
Breaking a bond uses one water (hydrolysis).

**D1-2 · Sucrose Street · Polymer Plant · L3 · Myth or Fact**
"Plants like sugar cane use sucrose to carry energy from their leaves to the
rest of the plant." ✔ Fact
Explanation: Leaves make glucose, link it with fructose into sucrose, and ship
the sucrose through their sap to roots, fruit, and growing tips. It's an energy
delivery molecule.

**D1-3 · Amino Avenue · House · L2 · Multiple choice**
Building a chain of 4 amino acids releases how many water molecules?
✔ 3 · 4 · 1 · 0
Explanation: One water per peptide bond. 4 amino acids are joined by 3 bonds,
so 3 waters.

**D1-4 · Amino Avenue · Polymer Plant · L3 · Sort-it**
Sort each protein by its job.
- Lactase → *Enzyme*
- Amylase → *Enzyme*
- Hemoglobin → *Transport*
- Keratin (hair and nails) → *Structure*
- Collagen (skin and tendons) → *Structure*
- Antibodies → *Defense*
Explanation: Proteins do most of the cell's work. Enzymes speed up reactions,
transport proteins carry things (hemoglobin carries oxygen), structural
proteins build tissues, and antibodies fight infection.

**D1-5 · Peptide Place · House · L2 · Odd One Out**
Which one is NOT hydrolysis?
- Digesting the protein in a steak into amino acids
- Breaking a peptide bond with water
- ✔ Linking two amino acids into a dipeptide
- Stomach enzymes snipping a protein chain apart
Explanation: Linking amino acids is dehydration synthesis (it releases water).
The other three break bonds with water, which is hydrolysis.

**D1-6 · Folding Falls · Buy · L1 · What am I?** (clues reveal one at a time)
1. Hemoglobin, keratin, and lactase are all examples of me.
2. I fold into a specific 3D shape.
3. I'm a long chain of amino acids.
✔ A protein · A polysaccharide · A triglyceride · DNA
Explanation: Proteins are chains of amino acids that fold into a shape, and the
shape decides the job.

**D1-7 · Folding Falls · House · L2 · Multiple choice**
Gram for gram, how much energy do proteins store compared with fats?
✔ Less than half (about 4 vs. about 9 Calories/g) · About the same · More than
fats · None, proteins never provide energy
Explanation: Fats are packed with energy-rich C–H bonds and store about
9 Calories/g. Proteins and carbs store about 4. Your body uses protein for fuel
mostly as a backup.

**D1-8 · Saturation Square · House · L2 · Myth or Fact**
"A gram of fat stores more than twice the energy of a gram of sugar." ✔ Fact
Explanation: About 9 Calories/g for fat vs. about 4 for sugar. Fat's long tails
are full of C–H bonds and have very little oxygen.

**D1-9 · Saturation Square · Polymer Plant · L3 · Odd One Out**
Which is NOT a job of lipids?
- Long-term energy storage
- Insulating the body (like a whale's blubber)
- Forming cell membranes
- ✔ Carrying genetic instructions
Explanation: Lipids store energy, insulate, and build membranes (and some
hormones). Genetic instructions are the job of nucleic acids.

**D1-10 · Membrane Marina · House · L2 · Multiple choice**
Building one phospholipid (glycerol + 2 fatty acids + 1 phosphate) releases how
many water molecules?
✔ 3 · 1 · 2 · 0
Explanation: Each part bonded to glycerol releases one water by dehydration
synthesis. Three parts attached means 3 waters.

**D1-11 · Nucleotide Knoll · House · L2 · Sort-it**
Sort each reaction: dehydration synthesis or hydrolysis?
- Adding a nucleotide to a DNA strand → *Dehydration synthesis*
- Recharging ADP back into ATP → *Dehydration synthesis*
- Spending ATP for energy → *Hydrolysis*
- Cutting a DNA strand's backbone → *Hydrolysis*
Explanation: Building a bond releases water (dehydration synthesis). Breaking
one uses water (hydrolysis). ATP is a nucleotide, so the same rules apply.

**D1-12 · Backbone Bay · Polymer Plant · L3 · Myth or Fact**
"If the bases in a gene were rearranged into a different order, it would still
store the same instructions." ✔ Myth
Explanation: The order of the bases *is* the message, like letters in a word.
The sugar-phosphate backbone holds them in order so the instructions stay
intact.

**Showdown riddles: third clue for one-at-a-time reveal** (hardest clue first)
- Carbs (answer: lactose): 1. Some people lack the enzyme that breaks me apart.
  2. I'm made of glucose and galactose. 3. ✱ You'll find me in milk.
- Proteins (answer: a polypeptide): 1. Change my order and you change my shape
  and my job. 2. My building blocks are joined by peptide bonds. 3. ✱ I'm a
  chain of amino acids.
- Lipids (answer: a phospholipid): 1. Two layers of me make up your cell's
  outer boundary. 2. ✱ I have a phosphate group on my glycerol. 3. I have a
  water-loving head and two water-fearing tails.
- Nucleic acids (answer: a nucleotide): 1. Billions of copies of me link up in
  a specific order. 2. That order spells out your genetic code. 3. ✱ I'm made
  of a phosphate, a sugar, and a nitrogen base.

**Report Card (D1):** houses and Polymer Plants owned, per color group.

**Proposed D1 payouts** (all tunable in `CFG`)
- House bonus +20, Polymer Plant bonus +30, on top of the normal question ATP.
- What am I?: normal question ATP, plus 5 for each clue left unrevealed.
- Sort-it: counts as first-try correct only if every chip is placed right on
  the first check. Wrong chips bounce back with a hint.

### D2 — streak meter, lifelines, confidence wagers (after D1)

- **Streak meter:** consecutive first-try correct answers build a multiplier
  (x2, x3), with a visible flame that resets gently on a miss.
- **Lifelines,** each usable once per lap: "Enzyme Cut" removes two wrong
  answers, and "Peek at the Lab" shows the relevant cheat-sheet row for 5
  seconds.
- **Optional confidence wager** before answering (Low, Medium, or High, with a
  small cap) to reward students who know they know it. Losses stay small; this
  should feel encouraging.
- Open D2 decisions: wager size (and whether a wrong answer loses ATP), and
  whether the streak, lifelines, and wager apply in the Mogul Showdown or only
  to board questions.
