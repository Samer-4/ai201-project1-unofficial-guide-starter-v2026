# The Unofficial Guide

Samer Ahmed — Corpus: `campus_life`

---

# Unit 1

## What This Does

The Unofficial Guide is a retrieval-augmented question answering system built using the `campus_life` corpus. It answers questions about campus topics such as housing, dining, studying, courses, and student life by retrieving relevant posts from the corpus. The system uses a relevance gate to reject questions that the corpus does not cover and generates brief answers grounded in the retrieved documents with source attribution.

## Chunking Strategy

**Chunk size:** One complete document/post  
**Overlap:** None

The `campus_life` corpus contains short posts that generally focus on one self-contained topic. The 88 documents average 317 characters, with the shortest at 178 characters and the longest at 549 characters. Because these posts are already short, I kept each complete post as one chunk rather than splitting it at an arbitrary character limit. This preserves the surrounding context and avoids cutting a complete thought into separate chunks.

## Sample Chunks

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::split_documents`

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::split_documents`

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::split_documents`

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```

## Sample Answer

**Question:** How long are wait times at Kestrel Commons between 12:15 and 1:00?

**Answer:**

```
The wait times at Kestrel Commons are 20 to 25 minutes between 12:15 and 1:00.

Source: `dining_kestrel_commons.txt` (also mentioned in `dining_kestrel_commons_followup.txt`).
```

**My relevance cutoff:** 0.6

I tested five questions covered by the corpus and five clearly out-of-scope questions. The best distances for the in-corpus questions ranged from 0.2197 to 0.5396, while the out-of-scope questions ranged from 0.8246 to 0.9340. This left a clear gap between the two groups, so I kept the 0.6 relevance cutoff. Lower distances represent closer matches.

| Question | In corpus? | Best distance |
|---|---|---:|
| When do housing lottery numbers come out? | Yes | 0.3453 |
| How long are wait times at Kestrel Commons between 12:15 and 1:00? | Yes | 0.2197 |
| What is the best time to do laundry in Aldridge Hall? | Yes | 0.2975 |
| How late is the library open during the term? | Yes | 0.4182 |
| What do students recommend wearing during winter? | Yes | 0.5396 |
| What is the capital of Mongolia? | No | 0.8246 |
| How do I change the oil in a diesel engine? | No | 0.9340 |
| Who won the 1994 World Cup? | No | 0.8859 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| How do I write a for loop in Rust? | No | 0.8960 |

## How I Used AI

**1.** I used AI to help me reason through a chunking strategy for the `campus_life` corpus. After inspecting the documents and seeing that they were short and mostly self-contained, AI suggested keeping each post as one chunk rather than using the starter's fixed-size character splitting. I used that approach and then manually inspected five resulting chunks to make sure they contained enough context to stand on their own.

**2.** I used AI to help interpret the retrieval distances from my five in-corpus and five out-of-scope questions. After comparing the results, I kept the 0.6 cutoff because there was a clear gap between the two groups. I also used AI to help improve the grounding instruction so numerical facts are preserved exactly when retrieved information is paraphrased.

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Numerical facts are preserved | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Retrieved chunk contains enough context | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

### Baseline Evidence

The baseline evaluation was produced by `run_eval.py` using the questions in `questions.py`, with retrieval and generation handled by the existing RAG pipeline.

**Criterion 1 — Retrieved chunk contains the answer**

For the housing lottery question, the system retrieved `admin_housing_lottery.txt` and answered:

> Housing lottery numbers come out the second week of March.

The retrieved document states: "Numbers come out the second week of March and selection runs over four evenings."

**Criterion 2 — Every answer names a source**

For the Kestrel Commons question, the system answered:

> The wait times at Kestrel Commons are 20 to 25 minutes between 12:15 and 1:00.

> Source: `dining_kestrel_commons.txt` (also mentioned in `dining_kestrel_commons_followup.txt`).

**Criterion 3 — Gate stops out-of-corpus questions**

The five out-of-scope questions were all refused by the relevance gate:

> Gate refused 5 of 5 out-of-scope questions.

**Criterion 4 — Numerical facts are preserved**

For the library-hours question, the system answered:

> The library is open until 2am during the term.

The retrieved source `study_library_hours.txt` states:

> Open until 2am during term, until 10pm during reading week.

**Criterion 5 — Retrieved chunk contains enough context**

For the winter clothing question, the retrieved `winter_gear.txt` contained:

> The buildings are heated to the point of being too warm, so layers matter more than a heavy coat.

This chunk contains the recommendation and the reason for it without requiring a neighboring chunk.

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MET | All 5 test questions retrieved a chunk containing the information needed to answer the question, exceeding the target of 4 of 5. |
| 2 | Every answer names a source | MET | All 5 generated answers named at least one source document in all three runs. |
| 3 | Gate stops out-of-corpus questions | MET | The relevance gate refused all 5 out-of-scope questions, exceeding the target of 4 of 5. |
| 4 | Numerical facts are preserved | MET | Numerical facts used in the generated answers matched the retrieved source documents, including the 20–25 minute Kestrel wait time and the library's 2am closing time. |
| 5 | Retrieved chunk contains enough context | MET | All 5 retrieved answer-containing chunks provided enough context to understand the relevant information without requiring a neighboring chunk. |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis: "Question 3 asks about laundry costs. The answer is in
     one sentence that got split across two chunks, so neither chunk on its
     own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

No acceptance criteria were missed in the baseline evaluation. All five criteria met their original targets.

The automatic scorer did report failures for the housing lottery and laundry questions. After inspecting the outputs, these were not retrieval or generation failures. Both answers contained the expected information. The failures came from the exact-string scorer comparing a lowercased expected phrase against an answer that was not lowercased, making the comparison case-sensitive.

Because the system met all five original criteria, I would tighten Criterion 1 from 4 of 5 to 5 of 5. For this corpus, the test questions have specific answers that are directly present in the documents, so requiring successful retrieval for all five questions would be a stronger standard.

## The Improvement

**What I changed:**

I tightened the grounding instruction in `generate.py`. Previously, the prompt explicitly required only numerical facts to be preserved exactly. I expanded this instruction to require exact preservation of factual details including numbers, dates, names, times, and specific recommendations.

**Why I picked it:**

The baseline showed that retrieval, chunking, and the relevance gate were already meeting their targets, so changing those stages was not supported by the diagnosis. I instead targeted generation fidelity to reduce the chance that correct retrieved information would be altered during paraphrasing.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Numerical facts are preserved | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Retrieved chunk contains enough context | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

The system continued to meet all five acceptance criteria after the grounding prompt was tightened, so the change did not introduce any regressions. The generated answers continued to preserve the factual details from the retrieved documents.

The raw question-level evaluation also changed from 3 of 5 passing to 5 of 5 passing. However, this improvement should not be attributed to the grounding prompt. During diagnosis, I found and corrected a case-sensitivity bug in `scorer.py` that had incorrectly marked the housing lottery and laundry answers as failures even though they contained the expected information. After correcting the evaluator, all five questions passed in all three runs.

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

All five acceptance criteria were met after the improvement, so there are no known failures against the current test criteria.

One limitation is that the evaluation only uses five in-corpus questions and five out-of-scope questions. This is enough for the acceptance criteria defined for this project, but a larger and more varied test set could reveal retrieval or generation failures that these questions do not cover.

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->

Knowing what I know now, I would make Criterion 1 stricter. I originally required the retrieved chunks to contain the answer for at least 4 of 5 test questions. Since all five questions retrieved the correct information consistently, I would change the target to 5 of 5.

I would also make the automated scorer case-insensitive from the beginning. The original scorer lowercased the expected phrase but not the generated answer, which caused two correct answers to be reported as failures because of capitalization differences. This showed me that evaluation code itself needs to be tested, not just the RAG system.

## How I Used AI

**1.** I used AI to help interpret the baseline evaluation results and compare them against the five acceptance criteria. This helped separate actual RAG performance from the automatic scorer results. I manually verified the retrieved source documents and confirmed that the expected information was present.

**2.** I used AI to help diagnose why two correct answers were marked as failures. We inspected `questions.py` and `scorer.py` and found that the expected answer was lowercased while the generated answer was not, causing case-sensitive comparisons to fail. I corrected the scorer and verified the fix by running the evaluation again.

**3.** I used AI to reason about which single system improvement was appropriate for Milestone 4. Since retrieval, chunking, and the relevance gate already met their targets, I chose to tighten the grounding instruction in `generate.py` rather than changing a stage that was already working.