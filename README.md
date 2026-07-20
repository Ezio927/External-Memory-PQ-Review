# External Memory Priority Queues with Decrease-Key

This repository records my reading, understanding, verification, and review-writing process for the paper:

**External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms**

* **Authors:** John Iacono, Riko Jacob, Konstantinos Tsakalidis
* **Conference:** ESA 2019
* **DOI:** 10.4230/LIPIcs.ESA.2019.60
* **Primary sources:** see [`sources/source-map.md`](sources/source-map.md)

---

## 1. Project Goal

The goal of this project is to produce a detailed review of the paper, with emphasis on:

* What problem the paper solves and why the problem is important;
* Previous related work and research published after this paper;
* The main techniques proposed by the paper;
* The intuition behind the proposed data structure;
* Detailed implementation of the data structure and its operations;
* Correctness arguments and I/O complexity analysis;
* Applications to external-memory graph algorithms;
* Strengths, limitations, and the significance of the results.

The final review is intended to be understandable and verifiable by a reader who has not previously read the paper.

---

## 2. Current Understanding

The paper studies priority queues in the external-memory model, where the main performance measure is the number of block transfers between internal memory and external storage.

A central difficulty is efficiently supporting `DecreaseKey`, or the more general `Update` operation, which is important in graph algorithms such as shortest-path algorithms.

The paper introduces a recursive data structure called an **x-treap**. My current preliminary understanding is that the x-treap combines:

* batched operations;
* hierarchical buffers;
* recursive substructures;
* delayed processing of updates;

so that many operations can share the cost of external-memory I/O.

The resulting priority queue is then applied to external-memory graph algorithms, including SSSP, DFS, and BFS.

This is an evolving understanding. Preliminary interpretations are recorded in reading notes and are revised after checking the original paper and related literature.

---

## 3. Current Progress

### Completed

* Repository initialized;
* Official paper and full-version sources collected;
* Initial project structure created;
* First-round reading of the problem background and external-memory model completed;
* Preliminary questions and uncertainties recorded;
* First reading branch merged into the default branch through a Pull Request.

### Current Stage

The project is currently moving from preliminary problem understanding toward:

1. precise definitions of the priority-queue operations;
2. previous work and the main contributions of the paper;
3. the intuition and structure of the x-treap;
4. construction of a unified review draft.

The review is intentionally developed through multiple reading, verification, and revision stages rather than being uploaded as a completed document at once.

---

## 4. Repository Structure

* [`notes/`](notes/)
  Chronological reading notes, preliminary interpretations, questions, and technical understanding developed during the reading process.

* [`review/`](review/)
  Outline and unified review draft. Material from the reading notes will gradually be verified, revised, and integrated here.

* [`sources/`](sources/)
  Primary paper files, source links, and later the reference map for related work.

* [`process/`](process/)
  Records of project progress, revisions, unresolved questions, and AI-assisted work.

* [`figures/`](figures/)
  Self-made explanatory figures used to understand and explain the data structures and algorithms.

---

## 5. Reading Notes

Current reading notes:

* [`notes/01-problem-and-background.md`](notes/01-problem-and-background.md)
  First-round understanding of the problem, external-memory model, motivation for `DecreaseKey`, preliminary intuition, and unresolved questions.

More notes will be added as the project progresses.

---

## 6. Review Development

The unified review will be developed in [`review/`](review/) through the following process:

**reading → preliminary notes → questions → source verification → revision → integration into the unified review**

The current structure of the review is recorded in:

* [`review/outline.md`](review/outline.md)

The outline and review draft will be expanded gradually as the relevant sections of the paper are understood and verified.

---

## 7. Process Records

The development process is recorded separately from the final review.

* [`process/progress-log.md`](process/progress-log.md)
  Chronological record of reading and writing progress.

* [`process/ai-usage-log.md`](process/ai-usage-log.md)
  Record of AI-assisted work, including the purpose of AI use and subsequent manual verification.

These files will be updated throughout the project to preserve the development history of the review.

---

## 8. AI Usage and Verification

AI tools may be used for tasks such as:

* locating papers and related literature;
* clarifying difficult definitions;
* discussing proof ideas;
* organizing reading plans;
* checking preliminary interpretations;
* assisting with Git and repository organization.

AI-generated explanations are **not treated as authoritative sources**.

Important factual claims, mathematical definitions, complexity bounds, and proofs will be checked against the original paper or other primary academic sources before being incorporated into the final review.

AI usage and the corresponding verification process are recorded in [`process/ai-usage-log.md`](process/ai-usage-log.md).

---

## 9. Status

**Work in progress.**

The repository is intended to preserve the complete development process of the paper review, including preliminary understanding, mistakes, unresolved questions, corrections, and later revisions.
