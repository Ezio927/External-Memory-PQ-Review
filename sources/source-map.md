# Source Map

This file records the primary academic sources collected and used during the development of the paper review.

The purpose of this file is not only to list references, but also to record why each source is relevant to the review and which parts of the final review it may support.

---

# 1. Primary Paper

## 1.1 ESA 2019 Conference Version

**John Iacono, Riko Jacob, and Konstantinos Tsakalidis.**
*External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms.*
27th Annual European Symposium on Algorithms (ESA 2019).

### Local File

`Iacono-Jacob-Tsakalidis-2019-External-Memory-PQ-ESA.pdf`

### Official Publication Page

<https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ESA.2019.60>

### Official PDF

<https://drops.dagstuhl.de/storage/00lipics/lipics-vol144-esa2019/LIPIcs.ESA.2019.60/LIPIcs.ESA.2019.60.pdf>

### DOI

10.4230/LIPIcs.ESA.2019.60

### Role in the Review

This is the official published version of the target paper.

It is the primary source for:

* the formal problem statement;
* the definition of priority-queue operations;
* the external-memory model;
* the previous-work discussion;
* the stated main contributions;
* the main theorem statements;
* the graph-algorithm applications;
* the official complexity claims.

The conference version is relatively concise, so some technical proofs are omitted or abbreviated.

---

## 1.2 Full Version

**John Iacono, Riko Jacob, and Konstantinos Tsakalidis.**
*Cache-Oblivious Priority Queues with Decrease-Key and Applications to Graph Algorithms.*

### Local File

`Iacono-Jacob-Tsakalidis-2019-External-Memory-PQ-Full-Version.pdf`

### arXiv Page

<https://arxiv.org/abs/1903.03147>

### PDF

<https://arxiv.org/pdf/1903.03147>

### Role in the Review

This is the full version associated with the target paper.

It will be used especially for:

* the detailed definition of the x-treap;
* recursive structure and buffers;
* invariants;
* `Resolve`;
* `Flush-Up`;
* `Flush-Down`;
* batched insertion;
* batched extraction;
* correctness proofs;
* amortized I/O analysis;
* technical details omitted from the conference version.

For theorem statements and publication claims, the ESA version remains the main reference. For detailed technical reconstruction, this full version is expected to be the more important source.

---

# 2. Earlier External-Memory Priority Queues

## 2.1 Fadel et al. — Heaps and Heapsort on Secondary Storage

**R. Fadel, K. V. Jakobsen, J. Katajainen, and J. Teuhola.**
*Heaps and Heapsort on Secondary Storage.*
Theoretical Computer Science, 1999.

### Local File

`Fadel-et-al-1999-Heaps-and-Heapsort.pdf`

### Available PDF

<https://hjemmesider.diku.dk/~jyrki/Paper/TCS-3.11.1998.pdf>

### Official Publication Page

<https://www.sciencedirect.com/science/article/pii/S0304397599000067>

### Role in the Review

This paper is relevant to the development of external-memory priority queues that achieve efficient I/O behavior when `DecreaseKey` is not required.

It will be used in the related-work section to explain the research line in which buffering, multiway structures, and block-efficient processing allow priority queues to approach sorting-type I/O complexity.

---

## 2.2 Kumar and Schwabe — External-Memory Graph Data Structures

**Vijay Kumar and Eric J. Schwabe.**
*Improved Algorithms and Data Structures for Solving Graph Problems in External Memory.*
1996.

### Local File

`Kumar-Schwabe-1996-External-Memory-Graph-Problems.pdf`

### Publication / Metadata Page

<https://scholars.depaul.edu/en/publications/improved-algorithms-and-data-structures-for-solving-graph-problem/>

### IEEE PDF

<https://ieeexplore.ieee.org/iel3/4218/12269/00570330.pdf>

### Role in the Review

This is an important early work on external-memory data structures for graph algorithms.

The target paper cites Kumar and Schwabe as a representative earlier approach that supports operations including `DecreaseKey`, but with weaker logarithmic I/O bounds than the sorting-type bounds available when `DecreaseKey` is not supported.

It will be used to explain the historical gap between:

* very efficient external-memory priority queues without `DecreaseKey`;
* priority queues that support `DecreaseKey` but pay higher I/O costs.

---

# 3. Priority Queues and Sorting

## 3.1 Wei and Yi — Equivalence Between Priority Queues and Sorting

**Zhewei Wei and Ke Yi.**
*Equivalence between Priority Queues and Sorting in External Memory.*
ESA 2014.

### Local File

`Wei-Yi-2014-Priority-Queues-and-Sorting.pdf`

### arXiv Page

<https://arxiv.org/abs/1207.4383>

### PDF

<https://arxiv.org/pdf/1207.4383>

### Role in the Review

This paper studies the relationship between external-memory priority queues and external-memory sorting.

It is relevant because the target paper repeatedly compares its priority-queue operation costs with

`Sort(N)`.

This work helps explain why external sorting complexity is a natural benchmark for evaluating external-memory priority queues.

---

# 4. Lower Bounds for DecreaseKey

## 4.1 Eenberg, Larsen, and Yu

**Kasper Eenberg, Kasper Green Larsen, and Huacheng Yu.**
*DecreaseKeys are Expensive for External Memory Priority Queues.*
STOC 2017.

### Local File

`Eenberg-Larsen-Yu-2017-DecreaseKeys-Are-Expensive.pdf`

### arXiv Page

<https://arxiv.org/abs/1611.00911>

### PDF

<https://arxiv.org/pdf/1611.00911>

### Official Publication Page

<https://doi.org/10.1145/3055399.3055437>

### Role in the Review

This paper provides an important lower-bound result for external-memory priority queues supporting `DecreaseKey`.

It is used to support the argument that the additional cost of `DecreaseKey` is not merely caused by poor data-structure design.

The paper is especially relevant to understanding why the target paper chooses an asymmetric tradeoff:

* optimal `Update`;
* more expensive `ExtractMin` and `Delete`.

This source will be checked carefully when discussing the exact lower-bound model, assumptions, and complexity expression.

---

# 5. Contemporary Work on DecreaseKey

## 5.1 Jiang and Larsen

**Shunhua Jiang and Kasper Green Larsen.**
*A Faster External Memory Priority Queue with DecreaseKeys.*
SODA 2019.

### Local File

`Jiang-Larsen-2019-Faster-External-Memory-PQ.pdf`

### arXiv Page

<https://arxiv.org/abs/1806.07598>

### PDF

<https://arxiv.org/pdf/1806.07598>

### Official Publication Page

<https://epubs.siam.org/doi/10.1137/1.9781611975482.81>

### Role in the Review

This paper is an important contemporary result published around the same period as the target paper.

It provides a randomized external-memory priority queue supporting `DecreaseKey` with improved bounds.

It will be used to compare two different approaches to the same general difficulty:

* improving more balanced support for `DecreaseKey`;
* deliberately making `Update` optimal while accepting slower `ExtractMin` and `Delete`, as in the target paper.

The comparison must distinguish assumptions, randomization, operation sets, and complexity tradeoffs rather than simply ranking one structure as universally better.

---

# 6. Later Research

## 6.1 External-Memory Priority Queues with Optimal Insertions

**Gerth Stølting Brodal, Michael T. Goodrich, John Iacono, Jared Lo, Ulrich Meyer, Victor Pagan, Nodari Sitchinava, and Rolf Svenning.**
*External-Memory Priority Queues with Optimal Insertions.*
ESA 2025.

### Local File

`Brodal-et-al-2025-Optimal-Insertions.pdf`

### Official Publication Page

<https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ESA.2025.5>

### Official PDF

<https://drops.dagstuhl.de/storage/00lipics/lipics-vol351-esa2025/LIPIcs.ESA.2025.5/LIPIcs.ESA.2025.5.pdf>

### Role in the Review

This paper represents later research on asymmetric operation complexity in external-memory priority queues.

Its main focus differs from the target paper because it studies highly efficient `Insert` and `DeleteMin`, rather than focusing on `DecreaseKey`.

It will be used to discuss the broader development of priority queues in which different operations are intentionally assigned different complexity budgets.

It should not be presented as a direct replacement for the target paper because the supported operation sets are different.

---

## 6.2 Lazy B-Trees

**Casper Moldrup Rysgaard and Sebastian Wild.**
*Lazy B-Trees.*
MFCS 2025.

### Local File

`Rysgaard-Wild-2025-Lazy-B-Trees.pdf`

### Official Publication Page

<https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.MFCS.2025.87>

### Official PDF

<https://drops.dagstuhl.de/storage/00lipics/lipics-vol345-mfcs2025/LIPIcs.MFCS.2025.87/LIPIcs.MFCS.2025.87.pdf>

### arXiv Preprint

<https://arxiv.org/abs/2507.00277>

### arXiv PDF

<https://arxiv.org/pdf/2507.00277>

### Role in the Review

This paper represents a later line of research based on lazy or delayed external-memory updates.

It is relevant to the review because it provides another example of intentionally asymmetric operation complexity.

The structure and goals differ substantially from the x-treap, so it will be discussed as a later related direction rather than a direct successor to the target paper.

---

# 7. Current Source Coverage

The current source collection covers:

* the official target paper;
* the full technical version;
* earlier external-memory heaps;
* early external-memory graph data structures supporting `DecreaseKey`;
* the relationship between external-memory priority queues and sorting;
* theoretical lower bounds for `DecreaseKey`;
* a contemporary 2019 randomized priority queue with `DecreaseKey`;
* later work on optimal insertion;
* later work on lazy external-memory data structures.

Additional sources will be added when the review reaches:

* the detailed x-treap construction;
* Buffered Repository Trees;
* external-memory SSSP;
* external-memory DFS;
* external-memory BFS;
* earlier graph-algorithm frameworks used by the target paper.

No source is considered fully reviewed merely because it appears in this file. Technical claims taken from these papers must still be checked against the corresponding original text before inclusion in the final review.
