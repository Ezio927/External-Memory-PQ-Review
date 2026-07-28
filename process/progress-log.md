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

---

## 2026-07-22 — x-treap Structure Analysis

### Goal

The goal of this stage was to move from the high-level contribution of the paper to the concrete data structure that realizes the claimed priority-queue tradeoff.

The main questions were:

* what an x-treap physically contains;
* how recursive subtreaps are organized;
* how key and priority information are represented;
* which properties must remain true after operations;
* how delayed updates can preserve the logical priority-queue state.

### Branch and Repository History

Work continued on:

`review/x-treap-structure`

During this stage, it was discovered that the previous related-work branch had not yet been merged into `main`.

The repository history was corrected by first merging:

`review/related-work-and-contributions`

into `main`, and then synchronizing the x-treap branch with the updated `main`.

This ensured that Sections 5 and 6 and the corresponding source/process records were present before the x-treap material was added.

### Section 7 — x-treap Intuition

Added an explanation of the main ideas behind the x-treap, including:

* recursive buffering;
* the relation to the x-box intuition;
* the use of key and priority as two different organizational dimensions;
* the different roles of front and rear buffers;
* the typical movement of updates through the structure;
* delayed removal of duplicate key versions;
* representative elements and ghost elements.

The main understanding developed in this stage is that x-treap does not immediately locate and modify an old element after every update.

Instead, updates may remain physically distributed through the structure while later batch operations restore the required logical relationships.

---

## 2026-07-22 — x-treap Concrete Structure and Invariants

### Work Completed

Added the concrete x-treap organization, including:

* top, middle, and bottom buffers;
* front/rear subdivision;
* upper- and lower-level recursive subtreaps;
* the $\sqrt{x}$ recursive scale;
* buffer capacity relationships;
* key-range partitioning;
* the base case.

The five structural invariants were also recorded and interpreted.

### Main Understanding

The invariants can be divided conceptually into two roles.

Key-related properties support efficient batch processing:

* buffers remain sorted by key;
* recursive subtreaps partition continuous key ranges.

Priority-related properties support extraction of minimum elements:

* front elements precede rear elements in priority;
* elements exposed in higher front buffers have smaller priority than elements stored below them.

The remaining structural condition keeps the recursive levels consistent when lower buffers contain data.

### Verification and Corrections

A full pass over `review.md` was performed after the first x-treap structure draft.

Corrections included:

* mathematical-expression formatting;
* heading hierarchy;
* the formal representation of $D.\mathrm{rep}$;
* the explanation of ghosts;
* the base-case parameter $\lambda$;
* several distinctions between intuitive descriptions and formal structural properties.

---

## 2026-07-22 — x-treap Operations

### Goal

After establishing the static structure, the next stage focused on how the x-treap restores and maintains its invariants during updates and extractions.

### `Resolve`

Studied `Resolve` as the basic local cleanup operation.

The main functions identified were:

* merge front and rear by key;
* remove duplicate versions of a key while retaining the minimum priority;
* restore the priority boundary between front and rear.

This explains how delayed `DecreaseKey` operations can eventually be reconciled using sequential scans rather than individual random searches.

### `Flush-Up`

Studied how minimum-priority candidates are restored when an upper front buffer becomes too small.

The analysis followed:

* recursive preparation of lower subtreaps;
* exposure of their smallest-priority candidates;
* comparison of candidates across multiple key ranges;
* movement of a batch of small-priority elements upward;
* restoration of the recursive structure when necessary.

This operation provides the main connection between the recursive layout and later `ExtractMin` operations.

### `Flush-Down`

Studied how the structure releases capacity after many updates have accumulated.

The analysis included:

* removing bottom rear elements first;
* selecting large-priority elements from bottom front when necessary;
* the $1/6$, $1/3$, and $2/3$ capacity thresholds;
* returning the removed elements as a key-sorted batch.

This operation explains how large-priority elements continue moving downward without requiring individual random I/O operations.

### `Initialize` and `Split`

Studied the operations used to maintain the recursive key partition.

`Initialize` constructs a recursive subtreap from a batch of elements.

`Split` divides an overfull subtreap according to key range so that a new recursive subtreap can be created.

Together they allow the recursive level to expand as more key ranges become populated.

### Batched Interfaces

Completed the first-pass analysis of the two main x-treap interfaces.

`Batched-Insert` combines:

* key-sorted merge;
* `Resolve`;
* recursive insertion;
* `Split`;
* `Initialize`;
* `Flush-Down`.

Its main effect is to allow update information to propagate downward in batches.

`Batched-ExtractMin` uses the priority invariants and `Flush-Up` to ensure that a batch of minimum-priority representative elements is available in the top front buffer before extraction.

### Current Understanding

The operations now form a coherent data flow:

```text id="xk5fue"
Update
  ↓
Batched-Insert
  ↓
Resolve / Split / Initialize / Flush-Down
  ↓
updates propagate downward


small-priority elements
  ↑
Flush-Up
  ↑
Batched-ExtractMin
  ↑
ExtractMin
```

At this stage, the structure and principal operations of the x-treap have been covered.

### Next Stage

The next stage is correctness analysis.

The planned questions are:

1. why duplicate physical copies do not change the logical represented element;
2. why the structural invariants are restored after the main operations;
3. why the elements returned through the top front are valid minimum representative elements.

After correctness, the project will move to the amortized I/O complexity analysis.

---

## 2026-07-23 — Correctness and Amortized Complexity Analysis

### Goal

After completing the structure and operations of the x-treap, the next stage focused on understanding why the data structure is both logically correct and able to achieve the claimed amortized I/O bounds.

The main questions were:

* how multiple physical copies of the same key can coexist without changing the logical priority-queue state;
* how representative elements are defined and maintained;
* why ghost elements do not cause an extracted or deleted key to be returned again;
* which x-treap invariants are directly responsible for `ExtractMin` correctness;
* why `Flush-Up` exposes the correct minimum-priority representatives;
* why the complicated recursive movement of elements still has a bounded amortized I/O cost.

### Correctness Analysis

Completed the correctness analysis of the x-treap and the complete priority queue.

The argument was organized around three levels:

1. **Representative semantics**
   Multiple physical versions of a key may coexist, but only the version with minimum priority represents the logical element.

2. **Structural invariants**
   `Resolve` and the other auxiliary operations restore the key-order and priority-order invariants required by the x-treap.

3. **Minimum extraction**
   Invariants 2 and 3 establish the priority ordering needed by `Flush-Up`. Once the minimum representatives have been exposed in the top front buffer, `Batched-ExtractMin` can return them correctly.

The analysis also connected the single x-treap to the complete priority queue.

The role of the hash table $X$ was clarified: keys already removed by `ExtractMin` or `Delete` are recorded so that old physical copies that later reappear can be recognized as ghosts and discarded.

### Amortized Complexity

The next step was to study why repeated flushing and recursive movement do not cause the I/O cost to grow uncontrollably.

The main idea developed was that batching gives a basic per-element cost of approximately:

$$
O(1/B)
$$

when a scan or merge involving $\Theta(C)$ elements costs $O(C/B)$ I/Os.

However, simply counting downward and upward moves is not sufficient because elements may move multiple times. The paper therefore uses a potential-function analysis.

The potential was interpreted as prepaid I/O work associated with:

* buffers approaching underflow or overflow;
* elements that still have several recursive levels to traverse;
* future `Flush-Up` operations;
* propagation between the different x-treaps of the complete priority queue;
* future processing of ghost elements.

This made it possible to connect the internal asymmetric movement of the x-treap to its final asymmetric operation bounds.

### Main Result Understood

For the cache-aware structure, setting $\lambda=O(M)$ gives:

$$
O\left(
\frac1B
\log_{M/B}\frac NB
\right)
$$

amortized I/Os for `Update`.

For `ExtractMin` and `Delete`, the bound is:

$$
O\left(
\left\lceil
\frac{M^\varepsilon}{B}
\log_{M/B}\frac NB
\right\rceil
\log_{M/B}\frac NB
\right).
$$

The main conceptual conclusion from this stage was that the final formulas reflect the actual data flow of the structure:

* updates mainly propagate downward through cheap batching;
* minimum extraction requires the more expensive upward restoration of candidates.

---

## 2026-07-24 — Buffered Repository Tree and Graph Applications

### Goal

After understanding the priority queue itself, the next stage investigated how the data-structure result is used in the graph algorithms claimed by the paper.

An important observation was that improving the priority queue alone is not sufficient. Existing external-memory graph algorithms also rely on a Buffered Repository Tree (BRT), which can become a second bottleneck.

### Buffered Repository Tree

Studied the BRT and its role in graph algorithms.

The BRT stores a multiset and supports:

* `Insert(e)`;
* `Extract(k)`, which returns and removes every stored element with key $k$.

A key distinction was established between the two recursive structures used in the paper:

* the new priority queue is based on the **x-treap**;
* the improved BRT is based on the earlier **x-box**.

The BRT analysis covered:

* batched insertion;
* search by key;
* output-sensitive $K/B$ cost;
* doubly increasing x-box sizes;
* the use of fractional-cascading pointers;
* the improved Insert and Extract bounds.

This showed that the BRT follows a similar asymmetric principle: insertions are delayed and batched, while queries perform more of the required search work.

### SSSP Application

The directed SSSP application was then analyzed through the operation counts of the existing external-memory algorithm.

The algorithm uses:

* $E$ priority-queue `Update` operations;
* $V$ priority-queue `ExtractMin` operations;
* $E$ BRT `Insert` operations;
* $V$ BRT `Extract` operations.

This operation distribution explains why the new asymmetric structures are useful.

For dense graphs:

$$
E\gg V,
$$

so the operations executed once per edge are significantly more frequent than the expensive operations executed once per vertex.

The graph analysis therefore gave a concrete interpretation of the priority-queue design:

> reducing the cost of the $E$ update-like operations can compensate for increasing the cost of the $V$ extraction-like operations.

### DFS and BFS Applications

The DFS/BFS analysis followed the external-memory traversal framework of Buchsbaum et al.

The role of BRT premarking was studied in more detail.

When a vertex $v$ is discovered, its incoming edges $(x,v)$ are inserted into the BRT using $x$ as the key. Later, when processing $x$, `Extract(x)` reports outgoing edges whose destination has already been discovered.

This replaces many direct random accesses to a `visited` array with batched BRT operations.

For the complexity analysis, the target paper uses:

* $E$ priority-queue `Insert` operations;
* $2V$ priority-queue `ExtractMin` operations;
* $E$ BRT `Insert` operations;
* $2V$ BRT `Extract` operations.

Thus SSSP, DFS, and BFS all exhibit essentially the same asymmetry:

$$
O(E)
$$

modification operations versus:

$$
O(V)
$$

more expensive query operations.

### Dense-Graph Result

The analysis was connected to the final cache-aware result.

For sufficiently dense graphs satisfying the paper's conditions, including:

$$
E=\Omega(V^{1+\varepsilon})
$$

and

$$
V=\Omega(M),
$$

the vertex-dependent expensive-query terms can be absorbed into the edge-dependent terms.

The resulting I/O complexity is:

$$
O\left(
\frac EB
\log_{M/B}\frac EB
\right),
$$

which matches the external-memory sorting bound for $E$ items.

---

## 2026-07-26 — Critical Evaluation

### Goal

After completing the main technical analysis, the project moved from explaining the paper to evaluating its design choices and limitations.

### Main Strength Identified

The central design decision was interpreted as a workload-aware asymmetric tradeoff.

Earlier lower-bound results indicate that supporting `DecreaseKey` introduces an unavoidable difficulty. Instead of attempting to make all priority-queue operations equally cheap, this paper makes `Update` particularly inexpensive while allowing `ExtractMin` and `Delete` to become more expensive.

The graph applications show why this can be useful:

$$
E
$$

edge-related modifications occur much more frequently than:

$$
O(V)
$$

vertex-related queries in dense graphs.

The priority queue and BRT therefore move cost away from the operations that occur most frequently.

### Limitations Identified

Several limitations were recorded.

* The improvement is workload dependent. It becomes less attractive when updates and extractions occur at similar frequencies.
* The I/O-optimal graph result applies to sufficiently dense graphs rather than arbitrary directed graphs.
* The priority queue requires more than linear block space.
* The guarantees are amortized rather than worst-case per operation.
* The x-treap is substantially more complicated to implement than conventional priority queues.
* The paper is primarily theoretical and does not provide an engineering evaluation showing whether the asymptotic improvement dominates implementation constants on real storage hardware.

### Comparison with Other Work

The result was compared with the earlier `DecreaseKey` lower bound and the contemporaneous Jiang–Larsen priority queue.

The main conclusion was that these results should not be treated as a simple ranking of data structures. They optimize different operation tradeoffs.

Later priority-queue work was also checked with attention to the supported operation set. In particular, faster insertion results that do not support `DecreaseKey` are not direct replacements for the priority queue studied here.

### Current Assessment

The overall assessment developed during this stage is:

> The paper is not best understood as a priority queue that uniformly dominates previous structures. Its main contribution is showing that an intentionally asymmetric operation tradeoff can be advantageous when it matches the workload of the target application.

This connects the lower-bound motivation, x-treap design, BRT improvement, and dense-graph applications into one coherent argument.

### Next Stage

The main technical sections of the review are now substantially complete.

The next work should focus on:

* checking the review against the course requirements;
* identifying missing proof, citation, or contribution statements;
* checking consistency of notation and terminology;
* improving the conclusion and overall narrative;
* preparing the review for final submission and possible oral questioning.

---

## 2026-07-27–2026-07-28 — Final Review, Verification, and Revision

### Goal

The main technical analysis had already been completed, including:

* x-treap structure and operations;
* correctness;
* amortized I/O complexity;
* Buffered Repository Tree;
* SSSP, DFS, and BFS applications;
* critical evaluation.

The `review/x-treap-structure` branch was therefore merged, and the project moved to the final revision stage on:

```text
review/final-analysis-and-revision
```

The goal of this stage was not to introduce another major technical section, but to check whether the existing review formed a complete and internally consistent final draft.

### Final Conclusion

Added Section 15 to summarize the complete argument of the review:

```text
DecreaseKey difficulty
        ↓
asymmetric operation tradeoff
        ↓
x-treap + BRT
        ↓
cheap frequent modifications
        ↓
more expensive queries
        ↓
dense graph applications
```

The final interpretation is that the paper does not attempt to make every priority-queue operation simultaneously optimal. Instead, it designs an operation tradeoff that matches applications with many more updates than extractions.

### Completeness Check

The full `review.md` was checked against the course requirements.

The review now contains:

* problem definition and motivation;
* external-memory background;
* previous and later related work;
* main technical contribution;
* x-treap structure and implementation details;
* correctness argument;
* amortized I/O analysis;
* BRT construction;
* SSSP / DFS / BFS applications;
* critical evaluation;
* final conclusion.

At this point, adding another major technical section was judged unnecessary.

### Technical Corrections

Several statements were revised during the final check.

#### Cache-oblivious and cache-aware settings

The earlier description treated the main structure too directly as cache-aware.

This was corrected by distinguishing:

* the parameterized structure using $\lambda$;
* the cache-aware specialization obtained by choosing

$$
\lambda=O(M).
$$

This also makes the later complexity derivations consistent with the notation used in the full version.

#### `Update` semantics

The informal expression

$$
\text{Update}
=============

\text{Insert}+\text{DecreaseKey}
$$

was removed.

The review now states the interface more accurately:

* if the key is absent, `Update` acts as an insertion;
* if the key is already represented, the operation corresponds to the `DecreaseKey` case.

#### Later related work

The discussion of *Lazy B-Trees* was updated after checking the authors' later erratum.

The conference-version priority-queue claim is therefore no longer presented as an established direct improvement over the target paper.

### References and Citations

A complete References section was added.

Citations were inserted for:

* the target ESA paper and its full version;
* the external-memory sorting model;
* earlier priority queues;
* the `DecreaseKey` lower bound;
* Jiang–Larsen;
* the original x-box work;
* the original BRT / graph traversal work;
* later external-memory priority-queue results.

The citation pass also clarified an important distinction in the graph-algorithm section:

* the DFS/BFS traversal framework comes from the earlier Buchsbaum et al. work;
* the aggregate operation counts used in the final complexity calculation are those stated by the target paper.

### Current Status

After this revision, the review has moved from a technically complete draft to a source-backed final draft with checked terminology, explicit references, and corrected related-work claims.

The remaining work is limited to updating the process records, merging the final revision branch, and performing a final submission check.
