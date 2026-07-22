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

---

## 2026-07-21 — Related-Work Research and Contribution Analysis

### Purpose

To collect and organize the academic sources needed for the related-work section, and to answer questions concerning:

* the development of external-memory priority queues before the target paper;
* the difference between structures that support and do not support `DecreaseKey`;
* the theoretical lower bound for `DecreaseKey`;
* contemporary research published around 2019;
* relevant research published after the target paper;
* the position and significance of the target paper within this research history;
* the main technical and theoretical contributions claimed by the target paper.

### AI Assistance

ChatGPT was used to:

* identify original papers relevant to the related-work discussion;
* locate official publication pages, open-access versions, and downloadable PDFs;
* distinguish the ESA 2019 conference version from the full arXiv version of the target paper;
* suggest consistent local filenames for the collected papers;
* help organize `sources/source-map.md`;
* explain the role of each collected source in the review;
* answer questions about the historical relationship among earlier external-memory heaps, priority queues supporting `DecreaseKey`, lower-bound research, contemporary 2019 work, and later research;
* explain the difference between the target paper and the Jiang–Larsen priority queue;
* explain why the 2025 optimal-insertion priority queue and Lazy B-Trees should be treated as related later directions rather than direct replacements for the target paper;
* answer questions about the target paper’s main contributions, including the asymmetric complexity tradeoff, the x-treap, the Buffered Repository Tree, and the graph-algorithm applications;
* help distinguish claims made explicitly by the papers from broader interpretations of their significance.

### Sources Located or Discussed

The AI-assisted source search covered the following works:

1. Iacono, Jacob, and Tsakalidis — ESA 2019 conference version;
2. Iacono, Jacob, and Tsakalidis — full arXiv version;
3. Fadel et al. — *Heaps and Heapsort on Secondary Storage*;
4. Kumar and Schwabe — *Improved Algorithms and Data Structures for Solving Graph Problems in External Memory*;
5. Wei and Yi — *Equivalence between Priority Queues and Sorting in External Memory*;
6. Eenberg, Larsen, and Yu — *DecreaseKeys are Expensive for External Memory Priority Queues*;
7. Jiang and Larsen — *A Faster External Memory Priority Queue with DecreaseKeys*;
8. Brodal et al. — *External-Memory Priority Queues with Optimal Insertions*;
9. Rysgaard and Wild — *Lazy B-Trees*.

The papers and corresponding links were organized in:

`sources/source-map.md`

### Source Verification

The following information was checked using original publication pages, original PDFs, or publicly available author and arXiv versions:

* paper titles and author lists;
* publication years and venues;
* whether each work predates, is contemporary with, or follows the target paper;
* the operation sets supported by the different priority queues;
* whether a result is deterministic or randomized where relevant;
* the general form of the reported I/O bounds;
* the role of the 2017 `DecreaseKey` lower bound;
* the fact that the later 2025 papers study different operation sets and performance objectives;
* the target paper’s stated complexity tradeoff between `Update` and `ExtractMin`/`Delete`;
* the target paper’s use of both the priority queue and the Buffered Repository Tree in its graph-algorithm applications.

### Interpretive Assistance

AI was also used to discuss the following interpretation:

> The target paper should not be described as making every priority-queue operation simultaneously optimal. Its main contribution is to deliberately assign an optimal I/O bound to `Update`, while allowing `ExtractMin` and `Delete` to be more expensive.

This interpretation was checked against the target paper’s own description of its results.

AI also helped explain why this asymmetric design can be useful when the higher-level graph algorithm performs many more update-like operations than extraction operations.

This workload-based explanation is treated as an interpretation of the paper’s design significance, not as a direct quotation or independent theorem from the paper.

### Limitations and Responsibility

The related-work explanations produced with AI assistance are preliminary research aids.

Before a technical claim is retained in the final review, the relevant source must be checked for:

* the exact computational model;
* assumptions on $M$, $B$, and $N$;
* randomization assumptions;
* amortized, expected, or worst-case guarantees;
* the exact operation set;
* the precise theorem statement;
* restrictions on graph density or graph type.

The collected sources have not all been read in full at this stage. Their inclusion in `source-map.md` records their relevance and availability, not the completion of a full technical review.
