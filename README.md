# Trailmarks

A small practice-test platform for Sewa, built from the 5th grade Math, Science,
and Social Studies Year-at-a-Glance plans. Same shape as the Class Companion
assignments Sewa already uses — short-answer questions, multiple attempts,
per-question scoring and feedback — but self-hosted and content you control.

No build step, no backend. It's four files: `index.html`, `styles.css`,
`app.js`, and `questions.js` (the question bank). Progress is saved to the
browser's local storage under the key `trailmarks_state_v1`, so it lives on
whatever device/browser Sewa answers on.

## Run it locally

Just open `index.html` in a browser — everything is static.

## Launch it on GitHub Pages

1. Create a new GitHub repo (e.g. `trailmarks`) and push these four files
   (plus this README) to the root of the `main` branch.

   ```bash
   git init
   git add .
   git commit -m "Trailmarks v1"
   git branch -M main
   git remote add origin https://github.com/<your-username>/trailmarks.git
   git push -u origin main
   ```

2. In the repo on GitHub: **Settings → Pages → Build and deployment → Source**,
   choose **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.

3. GitHub gives you a URL like
   `https://<your-username>.github.io/trailmarks/` within a minute or two.
   That's the link Sewa opens to take the practice sets.

## Adding or editing questions

Everything content-related lives in `questions.js`. Each subject has a list
of units, and each unit has a list of questions:

```js
{
  id: "m1q4",                 // unique — keep the subject/unit prefix
  prompt: "…the question text…",
  rubric: [
    {
      criteria: "…what earns the points…",
      points: 3,
      keywords: ["keyword1", "keyword2"],   // any one match = credit
      hint: "…shown if the criterion is missed, to guide a resubmit…"
    }
  ]
}
```

Scoring adds up rubric points where at least one keyword appears in the
answer (case-insensitive). It's a rough auto-grader meant for quick, private
practice — not a stand-in for a teacher's read of the work. `MAX_ATTEMPTS`
in `app.js` (currently 3) controls how many tries each question allows.

## What's loaded

**Every unit from all three Year-at-a-Glance plans — 33 units, 330 questions
(10 per unit):**

- **Math** — all 15 units: Place Value; Add/Subtract Whole Numbers &
  Decimals; Multiply Whole Numbers; Multiply Decimals; Divide Whole Numbers;
  Divide Decimals; Data; Add/Subtract Fractions; Multiply/Divide Fractions;
  Algebra & Coordinate Plane; Measurement Conversions; Shapes & Solids;
  Personal Financial Literacy; STAAR Review; 5th Grade Essentials.
- **Science** — all 11 rows: Lab Safety; Forces and Motion; Energy/Circuits/
  Light; Matter and Its Properties; Earth's Changing Surface; Semester 1
  Synthesis; Sun/Earth/Water Cycle; Ecosystems; Structure/Function/Survival;
  Targeted Cross-Strand Retrieval; Conservation and Human Impact.
- **Social Studies** — all 7 units: Exploration and Colonization; American
  Revolution; Government; Westward Expansion; Industrial Revolution; Civil
  War and Reconstruction; Modern America.

## How the question bank was built

The content isn't hand-typed directly into `questions.js` — it's assembled
from source files in `gen/`:

- `gen/helpers.py` — small `Q()` / `unit()` helpers that keep each question
  definition compact and validate that every unit has exactly 10 questions.
- `gen/math_data.py`, `gen/science_data.py`, `gen/social_data.py` — the
  actual question content, one Python list per subject.
- `gen/build.py` — imports all three, runs sanity checks (no duplicate IDs,
  every rubric item has points and keywords), and writes the final
  `questions.js`.

To add or edit content, edit the relevant `gen/*_data.py` file and rerun:

```bash
cd gen && python3 build.py
```

That regenerates `questions.js` in place. You only need to commit the
regenerated `questions.js` to GitHub — the `gen/` folder is optional to
include in the repo (handy to keep for future edits, but not required for
the site to run).

## Possible next steps

- Swap local storage for a small cloud sync (e.g. JSONBin.io) if you want
  Sewa's progress to follow across devices.
- Add a scheduled email digest of scores (e.g. via Zapier/Make.com) if you
  want a weekly summary without checking the site yourself.
