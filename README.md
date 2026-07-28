# External Memory Priority Queues with Decrease-Key

This repository records my reading, understanding, verification, and review-writing process for the paper:

**External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms**

* **Authors:** John Iacono, Riko Jacob, Konstantinos Tsakalidis
* **Conference:** ESA 2019
* **DOI:** 10.4230/LIPIcs.ESA.2019.60
* **Primary sources and related literature:** see [`sources/source-map.md`](sources/source-map.md)
* **Final review:** [`review/review.md`](review/review.md)

---

## 1. Project Goal

The goal of this project is to produce a detailed and verifiable review of the paper, with emphasis on:

* what problem the paper solves and why the problem is important;
* previous related work and research published after this paper;
* the main techniques proposed by the paper;
* the intuition behind the x-treap and the asymmetric operation tradeoff;
* detailed implementation of the data structure and its operations;
* correctness arguments and amortized I/O complexity analysis;
* the Buffered Repository Tree and its role in the applications;
* applications to external-memory SSSP, DFS, and BFS;
* strengths, limitations, and the broader significance of the results.

The final review is intended to be understandable by a reader who has not previously read the paper while still containing enough technical detail for the main claims and derivations to be checked against the original sources.

---

## 2. Final Understanding

The paper studies priority queues in the external-memory model, where the main performance measure is the number of block transfers between internal memory and external storage.

A central difficulty is supporting `DecreaseKey` efficiently. Unlike a simple insertion, a decrease-key operation depends on the previous state of an existing key, which makes straightforward implementations prone to expensive random I/O.

The paper does not attempt to make every priority-queue operation equally cheap. Instead, it develops an **asymmetric operation tradeoff**:

> make the frequently occurring `Update` operation I/O-efficient, while allowing `ExtractMin` and `Delete` to perform more of the deferred restructuring work.

The main data structure used to obtain this tradeoff is the **x-treap**.

Its main ideas include:

* recursive buffers and subtreaps;
* batching of updates;
* front/rear priority separation;
* delayed handling of duplicate key versions;
* `Resolve`, `Flush-Up`, and `Flush-Down` operations;
* amortized analysis using potential functions.

A key conceptual distinction is between physical storage and logical state. Multiple physical copies of the same key may temporarily remain in the structure, while the minimum-priority copy acts as the representative element. Old copies can later become ghosts and are filtered without requiring every update to immediately locate and delete all previous versions.

The paper also develops a new **Buffered Repository Tree (BRT)** using x-boxes. The priority queue and BRT share a similar design principle: frequent modification operations are handled through buffering and batching, while more expensive work is deferred to less frequent query operations.

This becomes useful in the graph applications because the relevant workloads are strongly asymmetric. The algorithms perform approximately:

```text
O(E) edge-related updates / insertions
O(V) vertex-related extractions / queries
```

For sufficiently dense graphs, where (E) is much larger than (V), this operation distribution matches the data structures' tradeoff and leads to sorting-style I/O bounds for directed SSSP, DFS, and BFS.

The main lesson I take from the paper is therefore broader than the x-treap itself:

> **A data structure does not necessarily need to optimize every operation equally. The best operation tradeoff can depend on the workload of the algorithm using it.**

---

## 3. Project Progress

### Completed

The project has gone through several stages:

1. collection of the official paper, full version, and related literature;
2. first-round reading of the problem and external-memory background;
3. analysis of previous work and the `DecreaseKey` lower-bound context;
4. study of the x-treap intuition and recursive structure;
5. detailed analysis of `Resolve`, `Flush-Up`, `Flush-Down`, `Batched-Insert`, and `Batched-ExtractMin`;
6. correctness analysis based on representatives, invariants, and ghost filtering;
7. amortized I/O complexity analysis;
8. study of the Buffered Repository Tree;
9. derivation of the SSSP, DFS, and BFS application bounds;
10. critical evaluation and comparison with related work;
11. final conclusion and integration of the review;
12. final source verification, citation checking, and technical corrections.

The unified review is now available at:

* [`review/review.md`](review/review.md)

The Git history and process records preserve the intermediate reading and revision stages rather than presenting the review as a document written in a single step.

---

## 4. Repository Structure

* [`review/`](review/)
  Contains the review outline and the final unified paper review.

  * [`review/outline.md`](review/outline.md)
  * [`review/review.md`](review/review.md)

* [`notes/`](notes/)
  Contains early reading notes, preliminary interpretations, questions, and intermediate technical understanding.

* [`sources/`](sources/)
  Contains the primary papers and the source map used to organize the target paper, related work, and later literature.

  * [`sources/source-map.md`](sources/source-map.md)

* [`process/`](process/)
  Records the development of the project, including reading progress, technical questions, revisions, and AI-assisted work.

  * [`process/progress-log.md`](process/progress-log.md)
  * [`process/ai-usage-log.md`](process/ai-usage-log.md)

* [`figures/`](figures/)
  Reserved for self-made explanatory figures used during technical analysis and review development.

---

## 5. Review Development

The review was developed through an iterative process rather than being written directly as a finished document:

```text
reading
   ↓
preliminary notes
   ↓
technical questions
   ↓
source checking
   ↓
derivation and verification
   ↓
review drafting
   ↓
revision
   ↓
final audit and citation check
```

Early interpretations were kept separate from the unified review so that mistakes, uncertainties, and later corrections remained visible in the project history.

The final review covers:

* problem definition and motivation;
* the external-memory model;
* why `DecreaseKey` is difficult;
* previous and later related work;
* main contributions;
* x-treap intuition and structure;
* x-treap operations;
* correctness;
* amortized I/O complexity;
* Buffered Repository Trees;
* SSSP, DFS, and BFS applications;
* critical evaluation;
* conclusion and references.

---

## 6. Source Verification

The target paper is treated as the primary source for its stated results.

The longer full version is used when more detailed definitions, data-structure operations, proofs, and amortized analyses are required.

Related literature was checked separately for:

* earlier external-memory priority queues;
* the `DecreaseKey` lower bound;
* contemporary priority queues supporting `DecreaseKey`;
* the original x-box work;
* the original Buffered Repository Tree and graph traversal framework;
* later external-memory priority-queue results.

During the final revision, several statements were corrected or qualified after checking the corresponding sources. In particular:

* the cache-oblivious parameterized construction was distinguished from its cache-aware specialization;
* the semantics of `Update` were stated more precisely;
* later claims involving *Lazy B-Trees* were updated to account for the authors' published erratum;
* the original graph-traversal framework was distinguished from the aggregate operation counts used in the target paper's complexity analysis.

The references used in the final review are listed directly in [`review/review.md`](review/review.md).

---

## 7. Process Records

The project process is documented separately from the final review.

* [`process/progress-log.md`](process/progress-log.md)
  Chronological record of reading, technical analysis, writing, and revision progress.

* [`process/ai-usage-log.md`](process/ai-usage-log.md)
  Record of AI-assisted work, including technical clarification, source checking, verification, and writing organization.

These records are intended to make the development path of the review visible rather than preserving only the final result.

---

## 8. AI Usage and Verification

AI tools were used as interactive aids during parts of the project, including:

* clarifying difficult definitions and data-structure operations;
* discussing proof and amortized-analysis ideas;
* checking interpretations against source material;
* locating and comparing related literature;
* identifying inconsistencies during final review;
* assisting with writing organization and repository workflow.

AI-generated explanations were not treated as authoritative academic sources.

Important definitions, complexity bounds, historical claims, and related-work comparisons were checked against the target paper, its full version, or the corresponding primary literature before being incorporated into the final review.

The use of AI and the related verification steps are recorded in:

* [`process/ai-usage-log.md`](process/ai-usage-log.md)

---

## 9. Status

**Main review completed.**

The repository now contains the final unified review together with its source map, reading notes, process history, and AI-usage record.

The project intentionally preserves intermediate understanding, questions, corrections, and revisions so that the development of the final review remains visible alongside the finished result.
