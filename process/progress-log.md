# Progress Log

This file records the chronological progress of the paper review project.

The purpose of this log is to preserve the development process of the review, including source collection, preliminary reading, repository organization, writing, verification, and revision.

---

## 2026-07-17 — Project Initialization

### Work Completed

* Created the GitHub repository for the paper review project.

* Selected the paper:

  **External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms**

* Located and collected the official ESA 2019 conference version.

* Located and collected the full arXiv version of the paper.

* Added the primary paper files and source links to the repository.

* Created the initial repository structure, including:

  * `notes/`
  * `review/`
  * `sources/`
  * `process/`
  * `figures/`

* Created the initial `README.md`.

### Corresponding Commit

`chore: initialize review repository and register paper sources`

### Current Goal

The immediate goal at this stage was to establish an online repository with accessible source materials and a visible version history before beginning detailed technical analysis.

---

## 2026-07-17 — First-Round Reading: Problem and Background

### Reading Scope

The first round of reading focused mainly on:

* the abstract;
* the introduction;
* the motivation of the paper;
* the external-memory model;
* the priority-queue operations discussed in the paper.

### Work Completed

Created:

`notes/01-problem-and-background.md`

The note records my preliminary understanding of:

* the problem studied by the paper;
* the external-memory model;
* the difference between internal-memory computation and external-memory I/O;
* the motivation for studying `DecreaseKey`;
* the importance of priority queues to large-scale graph algorithms;
* the preliminary intuition behind batching and buffering;
* questions that remained unclear after the first reading.

The note intentionally preserves several unresolved questions rather than presenting the first interpretation as final.

Examples of questions recorded at this stage include:

* What is the precise relationship between `Update` and `DecreaseKey`?
* How are multiple versions of the same key handled?
* What are the respective roles of the front and rear buffers?
* How does `Resolve` process duplicate keys?
* Why is the data structure willing to accept a more expensive `ExtractMin` operation?
* How is the amortized I/O complexity derived?

### Corresponding Commit

`notes: record first reading on the problem and I/O model`

### Current Understanding

At this stage, my preliminary understanding is that the paper attempts to improve the I/O efficiency of priority-queue updates in external memory.

The main proposed structure, the x-treap, appears to avoid immediately placing every updated element into its final position. Instead, operations are accumulated and processed in batches through a hierarchy of buffers and recursive substructures.

This interpretation is preliminary and will be checked against the formal definitions and proofs in later reading stages.

---

## 2026-07-17 — Repository History and Branch Workflow

### Work Completed

* Created a separate branch for the first reading stage:

  `reading/problem-background`

* Kept the first-round reading note as a separate substantive commit.

* Corrected the incorrectly named `sourses/` directory to `sources/`.

* Created Pull Request #1 to merge the first reading stage into the default branch.

* Merged the branch into `main` while preserving the individual commits.

### Result

The default branch now contains:

* the initial repository setup;
* the primary paper sources;
* the first-round reading note;
* the repository path correction;
* the Pull Request merge history.

This establishes a visible development path from repository initialization to the first substantive reading output.

---

## 2026-07-20 — README Revision

### Work Completed

Revised `README.md` to better reflect the actual structure and workflow of this single-person project.

The updated README now explains:

* the paper being reviewed;
* the overall goal of the project;
* the current preliminary understanding of the paper;
* the current progress of the review;
* the purpose of each repository directory;
* the location of reading notes;
* the intended development path from reading notes to the unified review;
* the purpose of the progress and AI-usage records;
* the policy for verifying AI-assisted information.

The previous README contained several descriptions designed for a multi-person group workflow. These descriptions were removed or revised because this project is being completed individually.

### Corresponding Commit

`docs: update README for single-person review workflow`

---

## Current Project Status

The initial repository and source collection stages are complete.

The first round of problem-background reading has also been completed and merged into the default branch.

The project is now transitioning from preliminary reading notes to systematic technical analysis and unified review writing.

The next major stages are expected to include:

1. verification of the precise priority-queue operation definitions;
2. study of previous work and the paper's main contributions;
3. explanation of the main intuition behind the x-treap;
4. detailed analysis of the x-treap structure and its invariants;
5. analysis of `Resolve`, `Flush-Up`, `Flush-Down`, batched insertion, and batched extraction;
6. reconstruction and verification of the correctness arguments;
7. reconstruction of the amortized I/O complexity analysis;
8. analysis of the graph-algorithm applications;
9. comparison with previous and later related research;
10. integration of verified material into a unified paper review.

The review will continue to be developed incrementally through separate reading, verification, drafting, and revision stages.

---

## 2026-07-21 — Related-Work Source Collection

### Goal

The goal of this stage was to collect the primary academic sources required to explain:

* the development of external-memory priority queues;
* the difficulty of supporting `DecreaseKey`;
* the research position of the target paper;
* contemporary and later related work.

### Work Completed

Collected and organized original or publicly available versions of papers covering:

* external-memory heaps without `DecreaseKey`;
* early external-memory data structures supporting graph operations and `DecreaseKey`;
* the relationship between external-memory priority queues and sorting;
* lower bounds for priority queues supporting `DecreaseKey`;
* contemporary 2019 improvements;
* later work on optimal insertion;
* later work on lazy external-memory data structures.

The two versions of the target paper were renamed consistently:

* `Iacono-Jacob-Tsakalidis-2019-External-Memory-PQ-ESA.pdf`
* `Iacono-Jacob-Tsakalidis-2019-External-Memory-PQ-Full-Version.pdf`

The remaining related-work papers were also stored under descriptive filenames in `sources/`.

### Source Map Revision

Expanded:

`sources/source-map.md`

For each paper, the source map now records:

* full title;
* authors;
* publication venue and year;
* local filename;
* official publication or metadata page;
* downloadable PDF or arXiv page where available;
* the reason the paper is relevant to the review;
* the review sections that the source may support;
* differences between the source and the target paper.

The source map is intended to function as a research guide rather than only a bibliography.

### Corresponding Commit

`sources: organize related-work papers and update source map`

---

## 2026-07-21 — Related-Work Analysis

### Reading and Analysis Scope

This stage examined the broad research development surrounding the target paper:

1. efficient external-memory priority queues without `DecreaseKey`;
2. earlier structures that support `DecreaseKey` at a higher I/O cost;
3. theoretical lower bounds demonstrating that `DecreaseKey` introduces additional difficulty;
4. contemporary work published around 2019;
5. later research exploring other forms of asymmetric operation complexity.

### Work Completed

Added Section 5, **Related Work**, to:

`review/review.md`

The section currently discusses:

* Fadel et al. and the development of external-memory heaps;
* Kumar and Schwabe’s early external-memory graph data structures;
* Wei and Yi’s relationship between priority queues and sorting;
* the 2017 lower bound for `DecreaseKey`;
* Jiang and Larsen’s contemporary randomized priority queue;
* the 2025 priority queue with optimal insertions;
* Lazy B-Trees as a later related direction;
* the overall historical transition from symmetric operation goals toward asymmetric complexity tradeoffs.

### Main Understanding Formed

The current interpretation is that external-memory priority-queue research developed along two initially separate directions:

* structures with sorting-type I/O efficiency but without efficient `DecreaseKey`;
* structures supporting `DecreaseKey` but with weaker bounds.

The 2017 lower-bound result showed that the difficulty associated with `DecreaseKey` has a theoretical basis.

The target paper can therefore be understood as choosing a new allocation of unavoidable costs rather than eliminating them completely.

### Corresponding Commit

`docs: add related work and research timeline`

---

## 2026-07-21 — Analysis of the Target Paper’s Main Contributions

### Work Completed

Added Section 6, **Main Contributions**, to:

`review/review.md`

The section currently identifies four main contributions:

1. a new asymmetric external-memory priority-queue tradeoff;
2. the x-treap data structure;
3. a new Buffered Repository Tree;
4. improved external-memory SSSP, DFS, and BFS results for the graph conditions specified by the paper.

### Main Interpretation

The most important conceptual contribution is currently summarized as follows:

> The paper does not attempt to make every operation simultaneously optimal. Instead, it makes `Update` I/O-optimal and transfers more of the unavoidable cost to `ExtractMin` and `Delete`.

This interpretation helps connect the data-structure result to the intended graph-algorithm workload, where update-related operations may occur substantially more often than extraction operations.

### Scope Distinctions Recorded

The review distinguishes the target paper from later or contemporary work by checking:

* supported operation sets;
* deterministic versus randomized guarantees;
* the type of asymmetry being optimized;
* whether `DecreaseKey` is included;
* whether a later result is a direct improvement or only a related direction.

In particular:

* Jiang–Larsen is treated as a contemporary alternative approach;
* the 2025 optimal-insertion result is not treated as a replacement because it studies a different operation set;
* Lazy B-Trees is treated as a later related direction with different structural goals and tradeoffs.

### Corresponding Commit

`docs: analyze main contributions and complexity tradeoffs`

---

## Current Project Status

The project has now completed the following major stages:

1. repository initialization;
2. collection of the target paper;
3. first-round problem-background reading;
4. creation of the unified review structure;
5. drafting and verification of the problem, motivation, model, and `DecreaseKey` difficulty sections;
6. collection and organization of primary related-work sources;
7. construction of a related-work timeline;
8. preliminary analysis of the target paper’s main contributions.

### Current Review Coverage

The unified review currently contains:

* the problem addressed by the paper;
* the importance of the problem;
* the external-memory model;
* the difficulty of `DecreaseKey`;
* earlier, contemporary, and later related work;
* the main contributions and operation-complexity tradeoff.

### Next Stage

The next stage will focus on the technical core of the paper:

* the intuition behind the x-treap;
* why the structure uses multiple buffer levels;
* the roles of front and rear buffers;
* recursive subtreaps;
* the invariants maintained by the structure.

Before writing detailed operation and proof sections, the x-treap will first be reconstructed conceptually from the full paper version.

The intended order is:

1. technical reading notes;
2. a simplified structural example;
3. identification of invariants;
4. source verification;
5. integration into the unified review.
