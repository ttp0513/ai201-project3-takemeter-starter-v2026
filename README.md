# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter is a text classifier designed to organize posts from the
`r/Genshin_Impact` community according to their primary purpose. It distinguishes
among lore discussion, build and team help, quest and exploration help, and
technical or account-related help by analyzing each post's title and body. The project
examines whether a small, manually reviewed dataset can support a reliable
community-specific classifier, particularly when posts share vocabulary across
categories. Its intended use is to describe patterns in community discussion,
not to moderate content or evaluate individual users.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `lore_discussion`

**Definition:** A post whose main purpose is to discuss, question, or propose
an interpretation of Genshin Impact's story, characters, factions, history,
or worldbuilding.

**Example 1:**
> The Traveler's Sibling is the "Goblet" to Asmoday.

**Example 2:**
> How different are the Tsaritsa and Abyss Order's goals?

### `build_and_team_help`

**Definition:** A post asking for or providing advice about character builds,
artifacts, weapons, team compositions, rotations, account strength, or whether
a character would improve an account or team.

**Example 1:**
> Can C6 Alyosha replace Odette in a Sandrone team?

**Example 2:**
> What artifact should I use with my Furina?

### `quest_and_exploration_help`

**Definition:** A post asking for practical instructions or assistance with
quests, puzzles, bosses, domains, achievements, navigation, or exploration
objectives in the game world.

**Example 1:**
> How do I unlock this chest?

**Example 2:**
> How do I decrease the water level in this cave?

### `technical_and_account_help`

**Definition:** A post asking for help with installation, launching, login,
account access, cross-save, controllers, crashes, performance, graphics,
network latency, or another device or service problem.

**Example 1:**
> My phone keeps freezing when teleporting.

**Example 2:**
> Game is lagging for no apparent reason.

### The hardest boundary

**Which two labels:**

`lore_discussion` and `quest_and_exploration_help`

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->

If the requested answer would explain the meaning of the story, a character,
or the world, I use `lore_discussion`. If the requested answer would provide
actionable steps for completing an objective or reaching a location, I use
`quest_and_exploration_help`, even when that objective is part of a story quest.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

The dataset contains 200 English-language posts from Reddit's
`r/Genshin_Impact` community. The posts were collected in several
browser-automation batches, combined into one file, and checked for duplicate
text before labeling.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

I labeled the first 20 posts without AI assistance to establish a cold-labeling
baseline. AI then supplied provisional labels for the remaining posts. Those
rows are explicitly marked `AI pre-labelled; pending human review` in the
dataset, and I will read and correct every provisional label before training.
When a post plausibly crossed a category boundary, I recorded the competing
labels and the decision rule in its note rather than treating the choice as
obvious.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `lore_discussion` | 63 | 31.5% |
| `build_and_team_help` | 60 | 30.0% |
| `quest_and_exploration_help` | 43 | 21.5% |
| `technical_and_account_help` | 34 | 17.0% |
| **Total** | **200** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* A player considered restarting an Adventure Rank 33 account
> because of poor banner luck and a weak roster.
>
> *Could have been:* `build_and_team_help` or
> `technical_and_account_help`.
>
> *I chose, because:* I chose `build_and_team_help`. Although restarting is an
> account-level decision, the underlying question is whether the player's
> current characters and pull results make the account viable. It does not
> concern login access, cross-save, installation, or another technical service
> problem.

**2.**
> *The post:* A player praised a Sumeru Archon Quest for its character
> development, treatment of Dottore, and conclusion, then asked what other
> players thought.
>
> *Could have been:* `lore_discussion` or
> `quest_and_exploration_help`.
>
> *I chose, because:* I chose `lore_discussion`. The post names a quest, but it
> evaluates the story and its characters rather than requesting instructions
> for completing an objective.

**3.**
> *The post:* A player completed the story quests for Razor, Diluc, and
> Xiangling but expected those characters to appear in the account roster.
>
> *Could have been:* `quest_and_exploration_help` or
> `technical_and_account_help`.
>
> *I chose, because:* I chose `quest_and_exploration_help`. The confusion is
> tied directly to the outcome of three named story quests and whether those
> quests grant their featured characters. Resolving the post requires
> explaining the quests' reward rules, rather than troubleshooting account
> access, synchronization, or a device-related failure.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Original settings:** <!-- epochs, learning rate, batch size, seed -->

- Epochs: 3
- Learning rate: `2e-5`
- Batch size: 16
- Maximum sequence length: 128 tokens
- Random seed: 42
- Hardware: CPU (`torch 2.14.1+cpu`)

**Anything I changed from the defaults, and why:**

I retained the notebook's default training settings for the first run. This
provides a reproducible reference point before any model or hyperparameter
changes and avoids tuning against a single 30-post test split.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->

The stratified split produced 139 training posts, 31 validation posts, and 30
test posts. The held-out test set contains 10 `lore_discussion`, 9
`build_and_team_help`, 6 `quest_and_exploration_help`, and 5
`technical_and_account_help` posts. The last two labels therefore have thin
test support: one prediction changes their recall or accuracy contribution by
approximately 17 or 20 percentage points, respectively. Their per-label scores
should be interpreted cautiously in the next unit.

**Combined improvement run:**

I preserved the original run in `results.json` and saved the exploratory rerun
in `results_improved.json`. The rerun kept DistilBERT, seed 42, the learning
rate, batch size, and the exact train, validation, and test partitions
unchanged. It increased training from three to five epochs, raised the maximum
sequence length from 128 to 256 tokens, and applied inverse-frequency class
weights calculated from the training split. The validation and test examples
were not reweighted or resampled.

| Measure | Original | Combined improvement | Change |
|---|---:|---:|---:|
| Accuracy | 0.467 | 0.833 | +0.367 |
| Macro F1 | 0.347 | 0.853 | +0.506 |
| `lore_discussion` F1 | 0.571 | 0.800 | +0.229 |
| `build_and_team_help` F1 | 0.529 | 0.778 | +0.248 |
| `quest_and_exploration_help` F1 | 0.286 | 0.833 | +0.548 |
| `technical_and_account_help` F1 | 0.000 | 1.000 | +1.000 |

The combined run is promising, but it is not a controlled one-change
experiment because three training decisions changed together. The result does
not establish which change produced the improvement. In addition, the perfect
technical/account F1 is based on only five test posts, so it should not be
interpreted as evidence of perfect generalization.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I asked AI to combine several batches of Reddit posts,
  detect duplicates, remove unrelated entries, and assign provisional labels
  using my four-label taxonomy.
- *What came back:* It produced a 200-post dataset with normalized duplicate
  checks, provisional labels, and notes identifying rows that required human
  review.
- *What I changed:* I revised the original taxonomy by replacing the difficult
  `pull_results` category with `quest_and_exploration_help`, kept pull and
  roster questions under `build_and_team_help`, and reconsidered ambiguous
  assignments rather than accepting every provisional label automatically.

**Moment 2**

- *What I asked for:* I asked AI to identify posts that crossed category
  boundaries and explain which two labels were plausible for each one.
- *What came back:* It highlighted cases involving account restarts, reactions
  to story quests, and expected character rewards after completing quests.
- *What I changed:* I used the written decision rules to make the final choice.
  In particular, the character-reward question is recorded as
  `quest_and_exploration_help` because resolving it requires explaining what
  those named quests award, not troubleshooting login, synchronization, or a
  device failure.

**Pre-labelling disclosure:**

AI supplied provisional labels for 180 posts after I labeled the first 20
without AI assistance. The AI-assisted rows remain explicitly marked
`AI pre-labelled; pending human review` in `labels.csv`. The Milestone 5 run
used the dataset in that documented state, so its metrics are a preliminary
reference rather than evidence that the labeling review is complete. I will
read and correct every provisional row before treating a later run as the
final model evaluation.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
