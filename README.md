# The Unofficial Guide

Kenneth.Hasiholan — Corpus: `campus_life`

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

This project is a retrieval-based question-answering system built on the campus_life corpus. Users can ask specific questions about campus topics like housing, courses, dining, registration, and library policies. The system pulls the document chunks that look most relevant and checks whether they actually contain enough to answer. If they do, it writes an answer using only those chunks. If they don't, it refuses instead of guessing.

## Chunking Strategy

**Chunk size: ** 500 characters
**Overlap: ** 1 sentence when a split occurs

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

The campus_life corpus is 88 short documents, averaging around 317 characters. Most are short enough that I leave them whole. Anything longer than the target size gets split on sentence boundaries rather than at a fixed character count, so a chunk never ends mid-sentence. When that happens, the last sentence carries into the next chunk as overlap to keep some context around the split.

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

======================================================================
Chunk 1 | source: admin_add_drop_deadline.txt#0 | produced by: chunker.py::split_documents
======================================================================
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.

======================================================================
Chunk 2 | source: course_biol_160_exams.txt#0 | produced by: chunker.py::split_documents
======================================================================
BIOL 160 Cell Biology — assessment

Four unit tests and a cumulative final. Not curved.

The unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.

======================================================================
Chunk 3 | source: course_math_220_exams.txt#0 | produced by: chunker.py::split_documents
======================================================================
MATH 220 Linear Algebra — assessment

Two midterms and a cumulative final. Curved to a b- median.

The problem sets are the course; the lectures make sense afterwards rather than during.

======================================================================
Chunk 4 | source: dining_the_ridgeway_cafe.txt#0 | produced by: chunker.py::split_documents
======================================================================
The Ridgeway Café

Second-year here. Wait times: 10 to 15 minutes at 12:30, none after 2:00. The thing worth going for is the only place on campus with real espresso. The thing to know is that seating is tight; about 40 seats for a building of 900.

Hours are 7:00am to 4:00pm weekdays only. Costs declining balance only, no meal swipes.

======================================================================
Chunk 5 | source: housing_morrow_house.txt#0 | produced by: chunker.py::split_documents
======================================================================
Morrow House — what it's actually like

Just finished a year in this building. Built 1954, partially renovated 2008. Rooms are singles and doubles, hall bathrooms.

The good: cheapest housing tier by about $900 a year, and the singles are real singles.

The bad: known damp problem on the ground floor; two rooms were taken offline in 2024.

Laundry costs $1.50 wash, $1.25 dry, coin or card. On noise: loud until about 1am on weekends, no enforced quiet hours.

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:**
How long does it usually take for a checked-out library book on hold to arrive?
**Answer:**

It usually takes two to three days for a checked-out book on hold to arrive.

Source: admin_library_holds.txt

**My relevance cutoff:** 0.6

I kept the cutoff at 0.6 because my five in-corpus questions had best distances between 0.1289 and 0.5301, while my five out-of-scope questions had best distances between 0.8246 and 0.9340. There is a clear gap between 0.5301 and 0.8246, and 0.6 falls inside that gap.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question                                                                        | In corpus? | Best distance |
| ------------------------------------------------------------------------------- | ---------- | ------------: |
| How long does it usually take for a checked-out library book on hold to arrive? | Yes        |        0.1289 |
| Is the housing lottery completely random?                                       | Yes        |        0.2514 |
| For Econ 101, how many hours a week should I expect outside class?              | Yes        |        0.2755 |
| What is the grade for passing a course?                                         | Yes        |        0.4001 |
| What are the requirements for advising registration?                            | Yes        |        0.5301 |
| What is the capital of Mongolia?                                                | No         |        0.8246 |
| What is the recommended dosage of ibuprofen for a headache?                     | No         |        0.8442 |
| Who won the 1994 World Cup?                                                     | No         |        0.8859 |
| How do I write a for loop in Rust?                                              | No         |        0.8960 |
| How do I change the oil in a diesel engine?                                     | No         |        0.9340 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.Understanding and changing the chunking strategy**

I first used AI to help me understand the starter chunker.py before changing any code. I wanted to understand what fallback_split, split_documents, chunk size, and overlap were actually doing instead of replacing the function without knowing why.

After looking at my baseline, I saw that campus_life had 88 documents averaging about 317 characters, and the starter produced exactly 88 chunks because almost every document was shorter than the 800-character limit. AI suggested a few possible strategies, including a more general paragraph-and-sentence-based chunker. I considered making the chunker more universal, but decided that would be more complicated than this milestone needed.

I chose a simpler sentence-aware approach: keep short documents whole, use a target of 500 characters for longer documents, split only at sentence boundaries, and carry one sentence into the next chunk as overlap. After re-indexing, the system produced 90 chunks with a longest chunk of 461 characters. I also printed the chunks and checked that the shorter chunks still contained complete thoughts instead of broken sentence fragments.

**2.Checking retrieval and choosing the relevance cutoff **

I also used AI while working through the retrieval results in Milestone 4. I first ran my own retrieval commands and noticed that the correct document appeared as the top result for the questions I checked, while some lower-ranked results were only loosely related. I used AI to help me understand why this happened and decided to keep top-k at 5 because the correct source was already appearing at rank 1 and increasing the number would mostly add more unrelated chunks.

I then ran all five of my in-corpus questions and all five OUT_OF_SCOPE questions and recorded the best distance for each one. My in-corpus distances ranged from 0.1289 to 0.5301, while the out-of-scope distances ranged from 0.8246 to 0.9340. I used AI to help compare those two groups, but I used the actual numbers from my own runs to make the decision. Because there was a clear gap between 0.5301 and 0.8246, I decided to keep the existing relevance cutoff at 0.6 rather than changing it just to make a change.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

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

| Criterion                                         | Target | Run 1 | Run 2 | Run 3 | Verdict |
| ------------------------------------------------- | ------ | ----- | ----- | ----- | ------- |
| 1. Retrieved chunks contain the answer            | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 2. Every answer names a source                    | 5 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 3. Gate stops out-of-corpus questions             | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 4. Chunks can be understood on their own          | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 5. Final answers contain the expected information | 5 of 5 | 2/5   | 2/5   | 2/5   | MISSED  |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

results/run_2026-09-23_2123_before.md

### Evidence from the before run

**Criterion 1 — Retrieved chunks contain the answer**

Produced by: `store.py::search`, chunks from `chunker.py::split_documents`

The retrieved source for each of the first five questions contained the answer:

- Housing lottery — `admin_housing_lottery.txt`
- ECON 101 workload — `course_econ_101_workload.txt`
- Library holds — `admin_library_holds.txt`
- Printing quota — `admin_printing_quota.txt`
- Advising registration — `advising_registration.txt`

Manual result: 5 of 5 retrieved chunks contained the answer.

**Criterion 2 — Every answer names a source**

File: `results/run_2026-09-23_2123_before.md`
Produced by: `run_eval.py::main`

Question: How much printing quota do students receive?

Students receive $30 of printing per semester.

Source: `admin_printing_quota.txt`

**Criterion 3 — Relevance gate stops out-of-corpus questions**

Produced by: `run_eval.py::check_out_of_scope`

What is the capital of Mongolia? — refused
How do I change the oil in a diesel engine? — refused
Who won the 1994 World Cup? — refused
What is the recommended dosage of ibuprofen for a headache? — refused
How do I write a for loop in Rust? — refused

Gate refused 5 of 5.

**Criterion 4 — Chunks can be understood on their own**

Produced by: `chunker.py::split_documents`

Chunk 1 — admin_add_drop_deadline.txt#0

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript.

Manual result: 5 of 5 sampled chunks could be understood without needing surrounding text.

**Criterion 5 — Final answers contain the expected information**

Produced by: `run_eval.py::main` using `scorer.py::judge`

Run 1: 2/5
Run 2: 2/5
Run 3: 2/5

Note: `questions.py` currently contains a sixth question. The five-question acceptance criteria above use Questions 1–5.

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| #   | Criterion | Verdict | How I decided |
| --- | --------- | ------- | ------------- |
| 1   |           |         |               |
| 2   |           |         |               |
| 3   |           |         |               |
| 4   |           |         |               |
| 5   |           |         |               |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion                              | Target | Run 1 | Run 2 | Run 3 | Verdict |
| -------------------------------------- | ------ | ----- | ----- | ----- | ------- |
| 1. Retrieved chunk contains the answer | 4 of 5 |       |       |       |         |
| 2. Every answer names a source         | 5 of 5 |       |       |       |         |
| 3. Gate stops out-of-corpus questions  | 4 of 5 |       |       |       |         |
| 4.                                     |        |       |       |       |         |
| 5.                                     |        |       |       |       |         |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
