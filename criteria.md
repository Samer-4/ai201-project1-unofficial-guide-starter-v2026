# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**

I chose 4 out of 5 because I want to allow a little room for error. An automatic
test might fail even when the retrieved information is still useful, so allowing
one miss gives me a chance to manually review it instead of making the target
too strict.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**

Every answer should have a source so I can verify where the information came
from and make sure the model is not hallucinating information that is unsupported
by the corpus.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

**Why this target:**

I chose 4 out of 5 because an out-of-scope question could still have some
similarity to information in the corpus. Allowing one miss gives the relevance
gate some leeway instead of making the cutoff so strict that it could reject
questions that are actually relevant.

---

## 4. Numerical facts are preserved

For at least 4 of my 5 test questions, any numerical facts used in the generated
answer should match the numerical facts in the source.

**Why this target:**

The system can paraphrase the retrieved information, but changing a number can
completely change the meaning even if the rest of the answer sounds correct. I
chose 4 out of 5 to allow one error that can be manually reviewed.

---

## 5. Retrieved chunks contain enough context

For at least 4 of my 5 test questions, the retrieved chunk containing the answer
should have enough context to understand the relevant information without needing
a neighboring chunk.

**Why this target:**

If a chunk is too small, important context could be missing. If it is too large,
the system may retrieve unnecessary information. I chose 4 out of 5 because I
want most chunks to be useful on their own while allowing one case that may need
additional surrounding context.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
