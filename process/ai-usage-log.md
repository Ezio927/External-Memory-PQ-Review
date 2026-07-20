# AI Usage Log

This file records AI assistance used during the development of the paper review project.

AI-generated information is not treated as an authoritative academic source. Important factual claims, technical definitions, complexity results, and proofs must be checked against the original paper or other primary academic sources before being included in the final review.

---

## 2026-07-17 — Locating the Primary Paper

### Purpose

To locate reliable and downloadable versions of the selected paper:

**External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms**

### AI Assistance

ChatGPT was used to help locate:

* the official Dagstuhl publication page;
* the official ESA 2019 conference-version PDF;
* the full arXiv version of the paper.

### Information Used

The following sources were added to the project:

* the official ESA 2019 publication page;
* the official conference-version PDF;
* the arXiv page;
* the full-version PDF.

These sources were recorded in:

`sources/source-map.md`

### Verification

The paper title, authors, conference information, DOI, and relationship between the conference version and the full version were checked against the official publication information.

The original paper remains the primary source for all later technical analysis.

---

## 2026-07-17 — Planning the Git and Repository Workflow

### Purpose

To design a repository workflow that preserves the gradual development process required by the course.

The course requires an online version history that allows the instructor or teaching assistants to observe the development of the review rather than receiving only a completed document near the deadline.

### AI Assistance

ChatGPT was used to discuss and plan:

* the initial repository directory structure;
* the separation between reading notes and the unified review;
* the use of separate Git branches for different reading stages;
* the use of incremental commits;
* the use of Pull Requests before integrating substantive reading work into the default branch;
* the preservation of individual commits instead of squashing the complete development process into a single commit;
* the correction of an early Git author-configuration problem;
* the resolution of an unrelated-history problem caused by rewriting the initial commit metadata.

### Decisions Adopted

The repository currently separates:

* `notes/` for chronological reading notes;
* `review/` for the developing unified review;
* `sources/` for paper files and references;
* `process/` for progress and AI-usage records;
* `figures/` for explanatory figures.

The first substantive reading stage was developed in:

`reading/problem-background`

and was later merged into `main` through Pull Request #1.

### Verification and Responsibility

The Git commands suggested by AI were executed and checked locally using Git status and log commands.

The resulting commit graph and default branch were checked after the merge to confirm that the first reading notes and their development history were preserved.

---

## 2026-07-20 — README Writing and Revision

### Purpose

To revise the project README so that a reader opening the repository can quickly understand:

* what paper is being reviewed;
* what the project is trying to accomplish;
* what has already been completed;
* where the reading notes and sources are located;
* how the final review will be developed;
* how the development process and AI use are recorded.

### AI Assistance

ChatGPT assisted in drafting a revised version of `README.md`.

The revised structure included:

* Project Goal;
* Current Understanding;
* Current Progress;
* Repository Structure;
* Reading Notes;
* Review Development;
* Process Records;
* AI Usage and Verification;
* Project Status.

### Changes Adopted

The README was revised from an earlier group-oriented description into a single-person project description.

References to multi-member task allocation and group reading notes were removed.

Direct links were added or planned for the main reading notes, review directory, source map, progress log, and AI-usage log.

### Verification and Responsibility

The final README structure was reviewed against the actual repository layout.

Descriptions that did not correspond to existing files or the actual single-person workflow were removed or corrected.

The README is intended only as a navigation and status page. Technical claims appearing in the final paper review will still require verification against the original academic sources.

---

## 2026-07-20 — Initial Review Drafting and Source Verification

### Purpose

To assist with drafting and revising the first four sections of the paper review:

1. the problem addressed by the paper;
2. the importance of the problem;
3. the external-memory model and basic definitions;
4. the difficulty of supporting `DecreaseKey`.

### AI Assistance

ChatGPT was used to:

* organize the structure of the first four review sections;
* explain the relationship among `Insert`, `DecreaseKey`, and `Update`;
* provide intuitive explanations of the external-memory model;
* explain the motivation for batching and buffering;
* discuss why delayed updates can create multiple logical versions of the same key;
* explain the tradeoff between fast `Update` and slower `ExtractMin`/`Delete`;
* draft preliminary Markdown text and mathematical expressions;
* review the draft against the original ESA 2019 paper and identify statements requiring correction.

### Source Verification

After the initial draft was produced, the following parts of the ESA 2019 paper were checked again:

* Abstract;
* Section 1, Introduction;
* Section 1.1, Previous Work;
* Section 1.2, Our Contributions.

The following points were verified directly from the original paper:

1. `Delete(k)` removes all elements with key `k`.
2. `Update((k,p))` combines `Insert` and `DecreaseKey`.
3. The external-memory model is parameterized by main-memory size $M$ and block size $B$.
4. Main-memory computation is considered free in the I/O model.
5. The definitions of `Scan(x)` and `Sort(x)` were checked.
6. The stated amortized I/O bound for `Update` was checked.
7. The higher bounds for `ExtractMin` and `Delete` were checked.
8. The paper explicitly describes its result as trading suboptimal `ExtractMin` and `Delete` for optimal `Update`.
9. The dense-graph conditions required for the I/O-optimal SSSP, DFS, and BFS results were checked.
10. The `DecreaseKey` lower-bound result cited by the paper predates the target paper; an earlier draft incorrectly described it as later research, and this was corrected.

### Corrections Made After Verification

The review draft was revised in several places:

* the exact complexity tradeoff between `Update` and `ExtractMin`/`Delete` was made explicit;
* the graph-algorithm optimality statement was restricted to the dense-graph conditions stated in the paper;
* a simplified expression involving $E$ updates and $V$ extractions was explicitly marked as intuition rather than a formal complexity derivation;
* the relationship between the amortized `Update` cost and `Sort(N)` was stated more carefully;
* the chronological description of the `DecreaseKey` lower-bound work was corrected;
* the lower-bound discussion was revised to distinguish theoretical limitations from the authors' design choices.

### Responsibility

The AI-generated draft is treated as a preliminary writing aid rather than an academic source.

Definitions, theorem statements, asymptotic bounds, and claims about previous work are checked against the original paper before being retained in the review.

Further sections involving the x-treap structure and correctness proofs will require separate verification because they are not yet covered by this entry.
