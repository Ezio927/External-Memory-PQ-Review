# Source Map

This file records the main academic sources used during the development and verification of the paper review.

Its purpose is not only to list references, buew it supports.

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
* the priority-queue interface;
* the external-memory setting;
* the previous-work discussion;
* the stated main contributions;
* the main theorem statements;
* the graph-algorithm applications;
* the official complexity claims.

The conference version is relatively concise, so the full version is used for technical details that are omitted or abbreviated here.

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

It is used especially for:

* the parameterized cache-oblivious formulation;
* the detailed definition of the x-treap;
* recursive structure and buffers;
* structural invariants;
* `Resolve`;
* `Flush-Up`;
* `Flush-Down`;
* `Batched-Insert`;
* `Batched-ExtractMin`;
* correctness arguments;
* amortized I/O analysis;
* Buffered Repository Trees;
* detailed graph-application derivations.

For publication claims and the main theorem statements, the ESA version remains the primary reference. For reconstruction of the data structures and proofs, the full version is the main technical source.

---

# 2. External-Memory Model and Sorting

## 2.1 Aggarwal and Vitter — The I/O Complexity of Sorting

**Alok Aggarwal and Jeffrey Scott Vitter.**
*The Input/Output Complexity of Sorting and Related Problems.*
Communications of the ACM, 31(9):1116–1127, 1988.

### Official DOI

<https://doi.org/10.1145/48529.48535>

### Role in the Review

This is a foundational source for the external-memory / I/O model and the complexity of external sorting.

It supports the use of sorting complexity as a basic benchmark in the review, including expressions of the form

[
\operatorname{Sort}(N)
======================

O\left(
\frac{N}{B}
\log_{M/B}\frac{N}{B}
\right).
]

The target paper uses the same external-memory parameters (M) and (B), and repeatedly compares its data-structure and graph-algorithm bounds with sorting complexity.

---

# 3. Earlier External-Memory Priority Queues

## 3.1 Fadel et al. — Heaps and Heapsort on Secondary Storage

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

This paper belongs to the earlier line of external-memory priority queues that can achieve very efficient I/O behavior when `DecreaseKey` is not required.

It is used in the related-work section to explain how buffering, multiway structures, and block-efficient processing allow priority-queue operations to approach sorting-type I/O complexity.

This provides an important contrast with the additional difficulty created by `DecreaseKey`.

---

## 3.2 Kumar and Schwabe — External-Memory Graph Data Structures

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

The target paper uses Kumar and Schwabe as a representative earlier result supporting operations including `DecreaseKey`, but with weaker logarithmic I/O bounds than those available to priority queues that do not support `DecreaseKey`.

It is used to explain the historical gap between:

* highly efficient external-memory priority queues without `DecreaseKey`;
* priority queues that support `DecreaseKey` but incur a larger I/O cost.

---

# 4. Priority Queues and Sorting

## 4.1 Wei and Yi — Equivalence Between Priority Queues and Sorting

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

It is relevant because the target paper repeatedly compares priority-queue operation costs with `Sort(N)`.

The result helps explain why sorting complexity is a natural benchmark for external-memory priority queues when `DecreaseKey` is not part of the required operation set.

---

# 5. Lower Bounds for DecreaseKey

## 5.1 Eenberg, Larsen, and Yu

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

This paper provides the important lower-bound result showing that efficient support for `DecreaseKey` in external-memory priority queues has an inherent cost.

It is used to support the argument that the difficulty of `DecreaseKey` is not merely a consequence of earlier data structures being poorly designed.

The lower bound is also important when interpreting the main design decision of the target paper:

* make `Update` very cheap;
* allow `ExtractMin` and `Delete` to become more expensive.

The final review uses this source when explaining why the target result does not violate the known lower bound, but instead redistributes complexity among different operations.

---

# 6. Contemporary Work on DecreaseKey

## 6.1 Jiang and Larsen

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

The final review uses it to compare two different approaches to the same general difficulty:

* Jiang–Larsen improves the performance of a more balanced set of priority-queue operations;
* the target paper more aggressively optimizes `Update` and accepts more expensive `ExtractMin` and `Delete`.

The comparison therefore focuses on assumptions, randomization, operation sets, and complexity tradeoffs rather than treating one structure as universally better.

---

# 7. Supporting Data Structures and Graph Frameworks

## 7.1 Brodal et al. — x-box / Update-Query Tradeoff

**Gerth Stølting Brodal, Erik D. Demaine, Jeremy T. Fineman, John Iacono, Stefan Langerman, and J. Ian Munro.**
*Cache-Oblivious Dynamic Dictionaries with Update/Query Tradeoffs.*
Proceedings of the Twenty-First Annual ACM-SIAM Symposium on Discrete Algorithms (SODA 2010), pp. 1448–1456.

### Official Publication Page

<https://epubs.siam.org/doi/10.1137/1.9781611973075.117>

### DOI

10.1137/1.9781611973075.117

### Role in the Review

This work is an important predecessor for the recursive update/query tradeoff used by the target paper.

The full version of the target paper builds its improved Buffered Repository Tree using an **x-box** originating from this line of work.

This source is therefore used to distinguish two related but different structures in the final review:

* the **x-treap**, which is the priority-queue structure introduced by the target paper;
* the **x-box**, which is used as a building block in the improved BRT.

It also provides broader context for the idea that external-memory data structures can deliberately trade query cost against update cost.

---

## 7.2 Buchsbaum et al. — Buffered Repository Trees and Graph Traversal

**Adam L. Buchsbaum, Michael H. Goldwasser, Suresh Venkatasubramanian, and Jeffery R. Westbrook.**
*On External Memory Graph Traversal.*
Proceedings of the 11th Annual ACM-SIAM Symposium on Discrete Algorithms (SODA 2000), pp. 859–860.

### Author PDF

<https://cs.slu.edu/~goldwasser/publications/SODA2000.pdf>

### ACM Record

<https://dl.acm.org/doi/10.1145/338219.338650>

### Role in the Review

This paper introduces the **Buffered Repository Tree (BRT)** and applies it to external-memory graph traversal.

It is used in the final review for two related purposes.

First, it provides the original BRT framework that the target paper later improves using x-boxes.

Second, it is the source of the earlier external-memory traversal framework underlying the target paper's DFS/BFS applications.

The review therefore distinguishes between:

* the earlier graph-traversal mechanism originating in Buchsbaum et al.;
* the improved BRT and aggregate complexity analysis given by the target paper.

This distinction is important when attributing the DFS/BFS algorithmic framework and the operation counts used in the final I/O analysis.

---

# 8. Later Research

## 8.1 External-Memory Priority Queues with Optimal Insertions

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

Its main focus differs from the target paper because it studies highly efficient `Insert` and `DeleteMin`, rather than priority queues supporting `DecreaseKey`.

It is used to illustrate the continued development of external-memory priority queues in which different operations are intentionally assigned different complexity budgets.

It is not treated as a direct replacement for the target paper because the supported operation sets are different.

---

## 8.2 Lazy B-Trees

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

### Author Page and Erratum

<https://www.wild-inter.net/publications/rysgaard-wild-2025>

### Role in the Review

This paper represents a later research direction based on lazy or deferred external-memory updates.

The MFCS 2025 version discusses using Lazy B-Trees as an external-memory priority queue and gives favorable bounds for operations including `DecreaseKey`.

However, the authors later published an erratum explaining that the original I/O analysis did not account for the cost of maintaining pointers in the blocked linked list.

As a result, the final review does **not** treat the conference-version `DecreaseKey` priority-queue claim as an established improvement over the target paper.

Lazy B-Trees are instead used as evidence that lazy updates and asymmetric update/query tradeoffs remain an active research direction, while also illustrating how subtle external-memory pointer-maintenance costs can be.

---

# 9. Final Source Coverage

The final source collection and source map cover the main references required by the review:

* the official target paper;
* the full technical version;
* the classical external-memory sorting model;
* earlier external-memory heaps and priority queues;
* early graph-oriented data structures supporting `DecreaseKey`;
* the relationship between priority queues and sorting;
* the theoretical lower bound for `DecreaseKey`;
* contemporary randomized work supporting `DecreaseKey`;
* the x-box / update-query tradeoff used in the BRT construction;
* the original Buffered Repository Tree and graph-traversal framework;
* later work on optimal insertion;
* later work on lazy external-memory data structures and its published erratum.

These sources play different roles in the final review.

The target paper and its full version are the main sources for the new results. Earlier papers are used to establish historical context and theoretical baselines. Later papers are used to evaluate how the research direction developed after 2019.

Inclusion in this source map does not by itself make a claim authoritative. Technical definitions, theorem statements, complexity bounds, and comparisons used in the final review were checked against the corresponding primary source before being cited.
