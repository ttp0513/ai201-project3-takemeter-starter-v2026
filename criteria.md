# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy is at least 0.65 for each of the three seeds.

<!-- Your criterion. It must name a number. -->



**Why this target:**

This is a four-class problem, so uninformed guessing would average about 0.25
accuracy on a balanced test set. A minimum of 0.65 would show that the model
has learned distinctions that are useful for organizing community posts rather
than relying primarily on class frequency.


---

## 2. Macro F1 is at least 0.60 for each of the three seeds.

<!-- Your criterion. -->



**Why this target:**

Accuracy alone could conceal weak performance on the less common
`pull_results` and `technical_and_account_help` labels. Macro F1 gives all four
labels equal weight, and 0.60 is demanding enough to require useful precision
and recall without assuming that a dataset of roughly 200 posts will support
near-perfect classification.


---

## 3. Every label has an F1 score of at least 0.45 for each seed.

<!-- Your criterion. -->



**Why this target:**

The final dataset is expected to contain approximately 40 to 50 examples per
label, leaving only about six to eight examples from each label in a 15 percent
test split. Per-label F1 will therefore be sensitive to a small number of
errors, but a score below 0.45 would still indicate that the classifier has not
learned that category reliably enough to be useful.


---

## 4. Every label has at least 6 examples in the held-out test set for each seed.

<!-- Your criterion. -->



**Why this target:**

This requirement checks whether the collected data remain sufficiently
balanced after the stratified 70/15/15 split. With fewer than six test examples,
one prediction would change a label's apparent performance too sharply for a
stable interpretation across seeds.


---

## 5. The most-confident third is at least 0.15 more accurate than the least-confident third for each seed.

<!-- Your criterion. -->



**Why this target:**

TakeMeter should not only produce labels; its confidence should provide useful
information about when a prediction deserves review. A gap of at least 0.15
would show that higher confidence corresponds to meaningfully better accuracy,
rather than functioning as an uninformative score.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
