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

---

## 2026-07-22 — x-treap Structure and Operations Study

### Study Focus

This stage focused on understanding the technical core of the paper rather than only recording its final complexity results.

The main questions investigated during reading were:

* why the x-treap uses a recursive layout instead of a conventional sequence of exponentially growing buffers;
* how key order and priority order are represented simultaneously;
* why every main buffer is divided into front and rear parts;
* how recursive subtreaps divide the key space;
* what role each of the five structural invariants plays;
* how delayed `DecreaseKey` operations can coexist with multiple physical copies of the same key;
* how representative elements and ghost elements should be distinguished;
* how elements move upward or downward while the structure maintains its logical priority-queue semantics.

The study then continued from the static structure to the dynamic operations:

* `Resolve`;
* `Flush-Up`;
* `Flush-Down`;
* `Initialize`;
* `Split`;
* `Batched-Insert`;
* `Batched-ExtractMin`.

The goal was to connect each operation to a specific structural problem rather than treating the pseudocode as an isolated sequence of steps.

### AI Assistance

ChatGPT was used as an interactive aid while working through these questions.

The main uses included:

* discussing alternative interpretations of the x-treap layout and checking which interpretation was consistent with the paper;
* explaining the distinction between the key dimension and the priority dimension;
* constructing small examples to test understanding of front/rear buffers and duplicate keys;
* answering questions about why individual invariants are necessary;
* explaining how `Resolve`, `Flush-Up`, and `Flush-Down` interact with those invariants;
* helping trace the movement of an element through the recursive structure;
* discussing the purpose of constants and thresholds appearing in the operations, such as the fractions used by `Flush-Up` and `Flush-Down`;
* relating `Split` and `Initialize` to the maintenance of key ranges;
* connecting the internal auxiliary operations to the two batched interfaces;
* helping reorganize the resulting understanding into coherent technical explanations for the review.

The interaction was mainly organized around specific questions arising from the paper, with the full version of the paper used to check the resulting interpretation.

### Technical Verification

The full version of the target paper was used to verify the main structural and algorithmic details discussed during this stage.

Items checked included:

* the capacities of the top, middle, and bottom buffers;
* the scale of recursive $\sqrt{x}$-treaps;
* the maximum numbers of upper- and lower-level subtreaps;
* the five x-treap invariants;
* the base-case condition involving $\lambda$;
* the distinction between representative elements and physical copies;
* the role of ghost elements;
* the behavior of `Resolve`;
* the recursive structure of `Flush-Up`;
* the $1/6$, $1/3$, and $2/3$ thresholds appearing in `Flush-Down`;
* the roles of `Initialize` and `Split`;
* the main control flow of `Batched-Insert`;
* the use of `Flush-Up` by `Batched-ExtractMin`.

Several explanations were revised after comparison with the original paper. In particular, high-level intuition such as “elements move downward and later upward” was kept separate from stronger claims about the exact path of an individual element.

### Review and Formatting Check

The complete `review.md` was also checked during this stage for technical and Markdown issues.

This included:

* checking displayed mathematical expressions for KaTeX/Markdown syntax problems;
* correcting several formulas whose line breaks caused rendering problems;
* correcting the representation of $D.\mathrm{rep}$;
* checking heading levels;
* revising the description of representative and ghost elements;
* correcting the cache-aware base-case discussion involving $\lambda$;
* checking terminology and notation for consistency across the earlier and newly added sections.

The corrections were incorporated into the working review before continuing to the operation analysis.

---

## 2026-07-23 — Correctness and Complexity Verification

### Questions Investigated

After completing the x-treap operations, the next reading stage focused on several questions that were not clear from the pseudocode alone:

* What exactly is preserved when several physical copies of the same key remain in the structure?
* Why does keeping only the minimum-priority version give the correct logical priority-queue semantics?
* Which invariants are actually required for the correctness of `Batched-ExtractMin`?
* Why does `Flush-Up` expose the correct minimum-priority representatives?
* Why is the hash table required in addition to local duplicate removal?
* What future work is the potential function paying for?
* Why is upward movement more expensive than downward batching?
* How do the internal x-treap bounds lead to the final cache-aware priority-queue bounds?

### AI Assistance

ChatGPT was used to discuss these questions during the reading process.

The assistance included:

* clarifying the distinction between physical copies, representatives, and ghosts;
* constructing small examples to test the representative semantics;
* tracing the dependency from the x-treap invariants to `Flush-Up` and `Batched-ExtractMin`;
* explaining the role of the hash table in filtering old copies after `ExtractMin` or `Delete`;
* interpreting the potential function as accounting for future structural work rather than treating it only as a formal expression;
* discussing why batching gives approximately $O(1/B)$ per-element movement cost;
* checking the relationship between $\alpha$ and $\varepsilon$;
* checking algebraic transitions used to obtain the final operation bounds;
* comparing interpretations with the full version of the paper when details were unclear.

The resulting reasoning was then organized into the correctness and amortized-complexity sections of the review.

---

## 2026-07-24 — BRT and Graph-Algorithm Cross-Checking

### Questions Investigated

The graph-application stage raised a different set of questions:

* Why is a BRT needed if the priority queue has already been improved?
* Is the BRT based on the x-treap or on the earlier x-box?
* What exactly does `Extract(k)` return?
* Why does its complexity contain an output term $K/B$?
* How many priority-queue and BRT operations are performed by the SSSP algorithm?
* Why is the difference between $E$ edge-related operations and $V$ vertex-related operations important?
* How does BRT premarking work in the DFS/BFS framework?
* Which parts of the graph algorithms come from previous work and which parts are new in the target paper?
* Why should the cache-aware and cache-oblivious density conditions be treated separately?

### AI Assistance

ChatGPT was used mainly for explanation, source cross-checking, and consistency checks.

The assistance included:

* distinguishing the x-box used by the BRT from the x-treap used by the priority queue;
* explaining the BRT interface and the output-sensitive term in `Extract`;
* checking the operation counts stated by the target paper for SSSP, DFS, and BFS;
* tracing the SSSP complexity calculation from the operation counts to the final bound;
* explaining why the asymmetric data-structure tradeoff becomes useful when $E\gg V$;
* discussing the premarking mechanism used by the earlier BRT graph-traversal framework;
* distinguishing details inherited from earlier graph algorithms from the new data-structure contribution of the target paper;
* checking the interpretation of the dense-graph assumptions;
* checking that graph-algorithm claims remained consistent with the statements in the target paper rather than being inferred from a simplified Dijkstra/DFS/BFS description.

This stage helped connect the previously studied data structures to their intended applications instead of treating the graph results as independent corollaries.

---

## 2026-07-26 — Critical Evaluation and Related-Result Verification

### Questions Investigated

After the main technical analysis was complete, the next step was to evaluate the paper rather than only summarize it.

The questions considered included:

* Is it accurate to say that the paper breaks the previous `DecreaseKey` lower bound?
* What is the real benefit of deliberately making `ExtractMin` more expensive?
* Under what workloads does this tradeoff stop being attractive?
* How important is the dense-graph assumption?
* What additional space or implementation costs are hidden behind the improved Update bound?
* How does the result differ from the contemporaneous Jiang–Larsen priority queue?
* Can later insertion-optimized priority queues be considered direct replacements?
* Which statements are direct conclusions from cited work and which are interpretation or evaluation?

### AI Assistance

ChatGPT was used to challenge and verify the evaluation developed from the preceding technical analysis.

The assistance included:

* checking that the relationship to the earlier `DecreaseKey` lower bound was described as a tradeoff rather than as a violation of the lower bound;
* comparing the operation objectives of the target paper and the Jiang–Larsen result;
* checking the operation sets of later external-memory priority queues before making comparisons;
* identifying cases where a later result improves `Insert` but does not support `DecreaseKey`;
* checking whether stated limitations followed from the paper's complexity, space, or model assumptions;
* distinguishing direct technical facts from personal evaluation;
* identifying wording that could overstate the generality or superiority of the target paper;
* helping organize the final evaluation around workload-aware data-structure design.

The main conclusion retained in the review is that the target paper should be evaluated as an asymmetric structure designed for update-heavy workloads, rather than as a uniform replacement for all external-memory priority queues.

---

## 2026-07-27–2026-07-28 — Final Audit and Source Verification

### Questions Discussed

AI was mainly used for final-stage checking rather than extending the technical analysis.

The main questions discussed were:

* whether the existing review already covered the required technical and evaluation components;
* whether the cache-oblivious / cache-aware terminology was being used consistently;
* whether later related-work claims, especially *Lazy B-Trees*, were still valid as originally written;
* where explicit citations were needed to make technical and historical claims traceable to their sources.

### AI Assistance

AI was used to:

* review the complete `review.md` and identify remaining consistency or citation gaps;
* compare several statements with the target paper and full version;
* check later related work and locate the published *Lazy B-Trees* erratum;
* help organize the final References section and citation placement;
* help reorganize the final conclusion so that it summarized the existing analysis without introducing a new technical argument.

### Verification and Corrections

The final checking process led to several concrete revisions.

The cache-oblivious / cache-aware description was corrected after comparing the review with the full version's $\lambda$-parameterized formulation.

The *Lazy B-Trees* discussion was qualified after checking the authors' erratum rather than relying only on the original conference claim.

Citation attribution was also clarified for the graph applications, especially the distinction between the earlier traversal framework and the operation counts used by the target paper.

### Result

At this stage, AI was used mainly as an interactive verification and editing aid.

The technical content itself was not expanded into a new section. The focus was instead on checking whether existing claims were supported, correcting several statements where necessary, and making the final review easier to verify from its cited sources.
