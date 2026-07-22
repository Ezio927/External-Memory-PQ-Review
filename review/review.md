# 1. 论文解决了什么问题

## 1.1 问题背景：外存环境中的优先队列

本文研究的问题是：**如何在外存模型（External Memory Model，也称 I/O Model）中设计一个能够高效支持 `DecreaseKey` 操作的优先队列，并利用这一数据结构改进大规模图算法的 I/O 复杂度。**

优先队列（Priority Queue）维护一组形如 $(key, priority)$ 的元素。其中，`key` 是元素的标识符，`priority` 是一个可以比较大小的优先级。

按照本文的定义，优先队列需要考虑以下几种操作：

* `Insert(e)`：向优先队列中插入元素 $e$；
* `Delete(k)`：删除所有 key 等于 $k$ 的元素；
* `ExtractMin()`：删除并返回当前优先级最小的元素；
* `DecreaseKey((k,p))`：如果当前已经存在 key 为 $k$、优先级为 $p'$ 的元素，并且 $p<p'$，则把它的优先级从 $p'$ 降低到 $p$。

论文还定义了一个统一的操作 `Update((k,p))`：

* 如果当前不存在 key 为 $k$ 的元素，则执行 `Insert((k,p))`；
* 如果已经存在 key 为 $k$ 的元素，则执行 `DecreaseKey((k,p))`。

因此，本文中的 `Update` 可以理解为：

> **若元素不存在，则插入；若元素已经存在，则尝试降低其优先级。**

它不是一般意义上的“任意修改优先级”，因为新的优先级不会把已有元素的优先级提高。

论文研究的是如何在数据规模大到无法全部装入主存时，仍然高效地支持这些操作。

---

## 1.2 现有方法中的核心矛盾

如果不要求支持 `DecreaseKey`，外存优先队列已经可以做得非常高效。

已有的一些外存优先队列能够充分利用磁盘块传输，一次处理大量元素，从而以接近外存排序的效率支持 `Insert`、`Delete` 和 `ExtractMin`。

但是，这些结构通常无法同时高效支持 `DecreaseKey`。

另一方面，早期能够支持 `DecreaseKey` 的外存优先队列往往需要付出更高的 I/O 复杂度，没有充分利用外存模型中“一次 I/O 可以传输整个数据块”这一特点。

因此，在本文之前存在一个重要矛盾：

> **不支持 `DecreaseKey` 时，可以利用缓冲和批处理获得很高的 I/O 效率；而一旦要求支持 `DecreaseKey`，就很难同时保持这种效率。**

本文的目标并不是让所有优先队列操作同时达到相同的最优复杂度。

相反，作者探索了一种新的性能权衡：

> **重点让大量发生的 `Update` 操作变得非常便宜，即使这意味着 `ExtractMin` 和 `Delete` 的成本会有所增加。**

这是一种明显的非对称设计。

传统的优先队列往往希望各种操作都比较快，而本文更关注一个操作在实际算法中出现多少次，并根据这一特点重新分配复杂度。

---

## 1.3 本文具体想达到的目标

本文最终构造的优先队列在维护 $N$ 个元素时，将 `Update` 的摊还 I/O 复杂度做到

$$
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right).
$$

与此同时，对于任意实数 $\varepsilon\in(0,1)$，`ExtractMin` 和 `Delete`
的摊还 I/O 复杂度为

$$
O\left(
\left\lceil
\frac{M^\varepsilon}{B}
\log_{M/B}
\frac{N}{B}
\right\rceil
\log_{M/B}
\frac{N}{B}
\right).
$$

因此，本文的结果并不是让所有优先队列操作同时达到相同的最优复杂度。
它实际上给出了一种明确的非对称权衡：

$$
\text{I/O-optimal Update}
\quad\Longleftrightarrow\quad
\text{suboptimal ExtractMin/Delete}.
$$

作者明确指出，这是第一个在 cache-aware 设置下以最优 I/O 复杂度支持
`Update`，从而支持 `DecreaseKey`，同时仍然以 I/O 最优复杂度支持
`Insert` 的优先队列。

这一改进的代价是 `ExtractMin` 和 `Delete` 不再保持最优 I/O 复杂度。

换句话说，本文并不是消除了支持 `DecreaseKey` 所需付出的额外成本，
而是重新决定了这些成本应该由哪些操作承担：作者选择优先优化
`Update`，并把一部分额外开销转移给 `ExtractMin` 和 `Delete`。

这一选择与后面的图算法应用密切相关，因为在某些图算法中，更新操作
的数量可能明显多于提取最小元素的操作数量。

从后面的数据结构设计来看，作者解决问题的基本方向是：

> 不要求每一次 `Update` 都立即完成所有数据整理，而是先把大量更新
> 积累起来，再利用递归缓冲结构批量处理。

本文为此提出的主要数据结构称为 **x-treap**。

x-treap 并不是简单地把普通二叉堆搬到磁盘上，而是结合递归结构、
多层缓冲区和批处理机制，使一个元素在结构中的移动可以和大量其他元素
一起完成，从而把一次 I/O 的成本分摊给多个操作。

x-treap 的具体结构和操作将在后文详细讨论。

---

# 2. 为什么这个问题重要

## 2.1 优先队列是许多图算法的核心数据结构

优先队列是计算机科学中最基础的数据结构之一，其中一个非常重要的应用领域就是图算法。

本文重点讨论的应用包括：

* 单源最短路径（Single-Source Shortest Paths, SSSP）；
* 深度优先搜索（Depth-First Search, DFS）；
* 广度优先搜索（Breadth-First Search, BFS）。

其中，最容易理解 `DecreaseKey` 作用的例子是最短路径问题。

在最短路径算法中，算法会维护每个顶点当前已知的最短距离估计。

假设当前认为到顶点 $v$ 的距离为 10，后来通过另一条边发现了一条长度为 6 的更短路径，那么就需要把 $v$ 的优先级从 10 降低到 6。

这正对应于：

$$
\operatorname{DecreaseKey}(v,6).
$$

因此，`DecreaseKey` 并不是为了让优先队列接口更加完整而人为增加的操作。

它直接对应了图算法中非常自然的一类计算：

> 当发现一个更优的候选解时，降低某个元素当前的优先级。

---

## 2.2 当图无法装入内存时，CPU 时间不再是唯一瓶颈

传统算法分析通常主要考虑 CPU 执行了多少次运算。

但是，当数据规模非常大时，这种分析方式可能无法准确反映程序的真实运行成本。

假设一个图的数据规模远远超过计算机主存容量。

此时：

* 大部分顶点和边存储在磁盘或其他外部存储设备中；
* 算法必须不断把数据从外存读入主存；
* 修改后的数据还可能需要重新写回外存。

在这种情况下，即使 CPU 只执行了非常简单的计算，如果算法不断访问磁盘上相距很远的位置，实际运行成本仍然可能非常高。

因此，外存算法关注的问题从

> “算法执行了多少次计算？”

变成了

> “为了完成这些计算，主存和外存之间传输了多少次数据块？”

本文正是在这种背景下研究优先队列。

---

## 2.3 `DecreaseKey` 的性能可能直接影响大规模图算法的整体性能

这一问题尤其重要，是因为在很多图算法中，不同优先队列操作出现的次数并不相同。

以最短路径算法为直觉例子：

* `ExtractMin` 通常与“确定一个顶点”有关；
* `Update` 或 `DecreaseKey` 通常与“检查一条边并尝试改进距离”有关。

设图中有 $V$ 个顶点和 $E$ 条边。

在很多图中，尤其是较稠密的图中，可能有：

$$
E \gg V.
$$

这意味着算法可能只执行大约 $V$ 次与顶点相关的提取操作，却需要执行大约 $E$ 次与边相关的更新操作。

因此，如果一个优先队列能够做到：

* `Update` 非常便宜；
* `ExtractMin` 稍微昂贵一些；

那么整个图算法仍然可能变得更快。

为了直观理解这种权衡的意义，可以暂时把某类图算法中的优先队列成本
粗略想象成：

$$
E\cdot T_{\text{Update}}
+
V\cdot T_{\text{ExtractMin}}.
$$

这里的表达式只是帮助理解本文设计动机的简化模型，并不是本文所有
SSSP、DFS 和 BFS 算法复杂度的正式推导。

它想说明的是：如果一个算法产生的更新操作数量与边数 $E$ 相关，而
提取操作数量主要与顶点数 $V$ 相关，那么在 $E$ 明显大于 $V$ 时，
降低一次 `Update` 的成本可能比继续优化 `ExtractMin` 更有价值。

本文后面对图算法复杂度的正式分析还需要同时使用新的优先队列和
Buffered Repository Tree，并分别计算各种数据结构操作的数量。
因此，具体复杂度将在图算法应用一节中重新严格推导。

这也是理解本文设计思想的一个重要角度。

本文接受如下权衡：

$$
\text{更快的 Update}
\quad\Longleftrightarrow\quad
\text{更慢的 ExtractMin/Delete}.
$$

对于更新次数远多于取最小值次数的问题，这种非对称设计可能比“所有操作具有类似复杂度”更加合适。

因此，这项工作的意义不仅在于设计了一个新的优先队列，也在于说明：

> **针对具体算法中不同操作出现频率的差异，可以重新分配数据结构不同操作之间的性能预算，从而获得更好的整体算法复杂度。**

本文最终将新的优先队列与 Buffered Repository Tree 结合。

对于具有 $V$ 个顶点和 $E$ 条有向边的图，当图满足

$$
E=\Omega\left(V^{1+\varepsilon}\right),
\qquad \varepsilon>0,
$$

并且

$$
V=\Omega(M),
$$

时，作者得到 SSSP、DFS 和 BFS 的 I/O 复杂度

$$
O\left(
\frac{E}{B}
\log_{M/B}
\frac{E}{B}
\right).
$$

这一复杂度与在外存中排序 $E$ 个元素的 I/O 最优复杂度处于相同量级。

**需要强调的是：**这一最优性结论针对论文所规定的稠密图条件，并不是说本文对所有规模和稠密程度的图都得到了相同的最优界

---

# 3. 外存模型与基本定义

## 3.1 External Memory Model

本文采用 External Memory Model，也称为 **I/O Model**。

该模型假设需要处理的数据量远远大于计算机主存容量。

设：

* $M$ 表示主存能够容纳的元素数量；
* $B$ 表示一个外存数据块能够容纳的元素数量。

数据主要存储在外部存储设备中，并按照连续的数据块组织。

一次 I/O 操作指：

> 在主存与外存之间传输一个包含 $B$ 个元素的数据块。

因此，在这个模型中，一次 I/O 不是只读取一个元素，而是一次读取或写入一个完整的数据块。

论文的复杂度分析主要采用以下约定：

* 主存内部的普通计算不计入 I/O 复杂度；
* 算法的主要时间成本使用 I/O 次数衡量；
* 数据主要存储在外存中；
* 算法的目标是尽可能减少主存和外存之间的数据块传输次数。

由于本文的数据结构显式使用参数 $M$ 和 $B$，因此本文正式版本中的主要数据结构属于 **cache-aware** 数据结构。

也就是说，算法在设计时知道主存大小和数据块大小，并利用这些参数决定缓冲区和递归结构的规模。

---

## 3.2 为什么一次只处理一个元素通常是不划算的

理解本文的关键，是认识到外存环境中的基本成本单位不是“一个元素”，而是“一个数据块”。

假设：

$$
B=1000.
$$

这表示一次 I/O 可以从磁盘读取 1000 个连续元素。

如果算法执行一次 I/O，却只使用其中的一个元素，那么其余 999 个被读取的元素都没有产生有效工作。

此时，从 I/O 复杂度的角度看，这次访问非常浪费。

相反，如果算法能够做到：

1. 先积累大量待处理操作；
2. 一次读取一个完整数据块；
3. 对块中的大量元素统一处理；

那么一次 I/O 的成本就可以由许多元素共同承担。

因此，外存数据结构中两个非常重要的设计思想是：

* **Batching（批处理）**
* **Buffering（缓冲）**

它们的基本思想都是：

> 不要立即处理每一个单独的请求，而是先把多个请求积累起来，再进行批量操作。

这也是后续理解 x-treap 的基础。

与普通堆中“每次操作立即调整结构”不同，x-treap 允许一些更新暂时停留在缓冲区中。

当缓冲区积累到足够多的元素之后，数据结构再统一进行移动、排序或整理。

这样，就可以让一次 I/O 服务于多个优先队列操作。

---

## 3.3 Scan 复杂度

如果需要顺序读取 $x$ 个连续元素，而每一次 I/O 可以读取 $B$ 个元素，那么所需 I/O 次数大约为：

$$
\operatorname{Scan}(x)
=

O\left(
\frac{x}{B}
\right).
$$

这是外存算法中最基本的复杂度之一。

例如，假设：

$$
x=10^6,
$$

并且：

$$
B=10^3.
$$

那么顺序扫描全部数据所需的数据块传输次数大约为：

$$
\frac{10^6}{10^3}
=

10^3.
$$

也就是说，只需要大约 1000 次块传输，而不是对 100 万个元素分别执行 100 万次独立 I/O。

因此，可以得到一个非常重要的结论：

> **顺序的批量访问通常非常便宜，而大量随机访问通常非常昂贵。**

本文后面的许多结构设计，本质上都是试图把大量零散的优先队列操作转化为较大规模的顺序扫描或批量数据移动。

---

## 3.4 Sort 复杂度

对于 $x$ 个元素，外存排序的最优 I/O 复杂度通常记为：

$$
\operatorname{Sort}(x)
=

O\left(
\frac{x}{B}
\log_{M/B}
\frac{x}{B}
\right).
$$

其中：

$$
\frac{x}{B}
$$

表示这些数据大约占用多少个外存块。

而：

$$
\frac{M}{B}
$$

表示主存一次大约能够容纳多少个外存块。

因此，$\operatorname{Sort}(x)$ 是外存算法中一个非常重要的复杂度基准。

如果某个数据结构处理大量元素的总成本能够与外存排序处于相同数量级，通常意味着它已经比较充分地利用了批处理和数据块传输。

本文给出的 `Update` 摊还 I/O 复杂度为：

$$
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right).
$$

当我们考虑规模为 $N$ 的一批更新，并且整个过程中数据结构的规模保持
在相应数量级时，把这一摊还成本乘到所有更新上，会得到与

$$
O\left(
\frac{N}{B}
\log_{M/B}
\frac{N}{B}
\right)
$$

相同数量级的总成本。

这正是

$$
\operatorname{Sort}(N)
$$

的复杂度量级。

因此，可以从直觉上把本文的结果理解为：大量 `Update` 操作平均到
每个元素上的成本，达到了外存排序中每个元素平均承担的 I/O 成本量级。

严格的最优性结论仍应以论文给出的定理和外存模型下界为准。

---

# 4. 为什么 `DecreaseKey` 特别困难

## 4.1 `Insert` 和 `DecreaseKey` 有一个本质区别

从表面上看，`Insert` 和 `DecreaseKey` 都是在向数据结构中加入新的 $(key,priority)$ 信息。

但是两者之间存在一个非常重要的区别。

对于：

$$
\operatorname{Insert}((k,p)),
$$

新插入的元素原则上可以先独立处理。

因此，大量 `Insert` 可以先积累在缓冲区中，然后统一排序，再批量向数据结构的下一层移动。

但是对于：

$$
\operatorname{DecreaseKey}((k,p)),
$$

这个操作的含义依赖于数据结构之前保存的状态。

假设数据结构中已经存在：

$$
(k,p').
$$

那么只有当：

$$
p<p'
$$

时，新的优先级 $p$ 才应该代替原来的 $p'$。

因此，`DecreaseKey` 并不仅仅是在数据结构中加入一个新的元素。

它意味着：

> **新的更新必须与历史上相同 key 的状态发生联系。**

而这个历史状态可能存储在外存中的另一个位置。

这使得 `DecreaseKey` 比单纯的 `Insert` 更难批量处理。

---

## 4.2 最直接的实现方法会产生大量随机 I/O

一种最直接的 `DecreaseKey` 实现方法是：

1. 根据 key $k$ 找到原来元素的位置；
2. 从磁盘读取包含该元素的数据块；
3. 修改它的优先级；
4. 恢复优先队列结构。

问题在于，不同的 `DecreaseKey` 操作可能针对完全不同的 key。

例如连续执行：

$$
\operatorname{DecreaseKey}(k_1),
$$

$$
\operatorname{DecreaseKey}(k_2),
$$

$$
\operatorname{DecreaseKey}(k_3),
$$

等等。

而 $k_1,k_2,k_3$ 对应的元素可能分别位于磁盘上完全不同的位置。

这样，每一次操作都可能触发一次独立的随机 I/O。

即使一个数据块能够容纳 $B$ 个元素，每次实际需要修改的却可能只有其中一个元素。

因此，就无法获得理想情况下接近 $1/B$ 的批处理收益。

从外存算法设计的角度看，这是一种非常不理想的情况：

> 每一次逻辑操作只处理一个元素，却支付了传输整个数据块的 I/O 成本。

所以，一个高效的外存优先队列通常希望避免立即寻找目标元素，而是先把更新缓存起来。

但是，一旦允许延迟更新，就会产生新的问题。

---

## 4.3 延迟更新会产生“多个版本”的问题

假设当前数据结构中存在元素：

$$
(A,10).
$$

现在执行：

$$
\operatorname{DecreaseKey}(A,6).
$$

为了避免立即在磁盘中寻找 $(A,10)$，一种可能的方法是暂时保留原来的元素，同时把：

$$
(A,6)
$$

作为一个新的更新写入某个缓冲区。

之后又执行：

$$
\operatorname{DecreaseKey}(A,3).
$$

那么物理存储中可能暂时同时出现：

$$
(A,10),
\quad
(A,6),
\quad
(A,3).
$$

但是从逻辑上看，真正应该代表 key $A$ 的只有：

$$
(A,3).
$$

这意味着数据结构必须解决一个非常重要的问题：

> **如果物理上允许同一个 key 的多个版本暂时同时存在，如何保证逻辑上始终只使用正确的那个版本？**

这个问题不仅影响 `DecreaseKey`。

它还会直接影响 `ExtractMin`。

假设某个旧版本已经失效，但它仍然保存在数据结构中。

如果 `ExtractMin` 错误地把一个已经失效的旧版本返回，那么整个优先队列的逻辑就会出错。

因此，延迟处理 `DecreaseKey` 虽然减少了立即执行随机 I/O 的需求，却必须增加某种机制来处理：

* 相同 key 的多个版本；
* 已经失效的旧版本；
* 不同缓冲区中的重复元素；
* 当前真正有效的最低优先级。

本文后面出现的 `Resolve` 操作，就是理解这一问题的重要部分之一。

---

## 4.4 `DecreaseKey` 与批处理之间存在天然冲突

外存算法通常希望采用下面的处理方式：

$$
\text{大量独立操作}
\rightarrow
\text{先积累}
\rightarrow
\text{批量处理}.
$$

但是 `DecreaseKey` 的正确语义要求：

$$
\text{新的更新}
\rightarrow
\text{与已有状态发生联系}
\rightarrow
\text{判断哪个版本有效}.
$$

因此，两者之间存在一种天然的矛盾。

从 I/O 效率角度看，我们希望：

> 尽量延迟处理更新，把许多操作积累起来以后统一处理。

但是从 `DecreaseKey` 的正确性角度看，我们又必须：

> 识别同一个 key 在不同时间产生的多个状态，并最终保留正确的版本。

因此，一个高效的外存 `DecreaseKey` 数据结构必须同时做到以下几点：

1. 不为每一次 `DecreaseKey` 单独执行昂贵的随机 I/O；
2. 允许更新暂时保存在不同缓冲区或不同层次中；
3. 能够批量发现相同 key 的多个版本；
4. 在多个版本中保留逻辑上正确的优先级；
5. 保证 `ExtractMin` 不会返回已经失效的旧版本；
6. 同时维持整个优先队列所需要的优先级顺序关系。

这正是后续 x-treap 结构以及 `Resolve`、`Flush-Up`、`Flush-Down` 等操作需要解决的问题。

---

## 4.5 `DecreaseKey` 的困难具有理论下界依据

`DecreaseKey` 的困难并不只是因为已有数据结构设计得还不够巧妙。

在本文发表之前，Eenberg、Larsen 和 Yu 已经研究了支持
`DecreaseKey` 的外存优先队列下界。

目标论文在 Previous Work 中引用了这一结果：对于一个支持
`DecreaseKey` 的外存优先队列，在一系列操作中，至少有一种操作的
摊还 I/O 复杂度必须达到

$$
\Omega\left(
\frac{1}{B}
\log_{\log N} B
\right).
$$

这里考虑的操作包括：

* `Insert`
* `Delete`
* `ExtractMin`
* `DecreaseKey`

也就是说，不能指望在一般情况下简单地设计一个数据结构，使所有这些
操作都同时变得任意便宜。

这一结果非常重要，因为它说明：

> 支持 `DecreaseKey` 所带来的困难不只是现有实现的问题，而具有
> 理论复杂度下界的依据。

目标论文正是在这一背景下选择了一种非对称的复杂度分配方式。

它没有试图让：

* `Insert`
* `Update`
* `ExtractMin`
* `Delete`

全部同时达到相同的最优 I/O 复杂度。

相反，本文优先保证：

$$
\operatorname{Update}
=
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right)
$$

的最优摊还 I/O 复杂度，而允许 `ExtractMin` 和 `Delete` 承担更高的
I/O 成本。

论文还特别指出，在

$$
M=\Omega(B\log^2 N)
$$

的条件下，这种复杂度分配仍然遵守已有的 `DecreaseKey` 下界。

因此，本文的设计思路不是“绕过”理论下界，而是：

> **在下界允许的范围内重新安排各类操作之间的复杂度，把更低的成本
> 分配给对目标图算法更重要的 `Update`。**

从这个角度看，本文真正解决的问题可以更准确地表述为：

> **在支持 `DecreaseKey` 必然带来额外外存复杂度压力的情况下，如何
> 设计一种优先队列，使频繁发生的 `Update` 操作达到 I/O 最优，并把
> 必须承担的额外代价转移到 `ExtractMin` 和 `Delete`，从而改善目标
> 图算法的整体 I/O 复杂度。**
>

---

# 5. 相关工作

理解本文的贡献，不能只看它最终给出的复杂度公式，还需要先理解外存优先队列研究此前存在的两条不同发展路线：

1. 不支持 `DecreaseKey`，但尽可能接近外存排序的最优 I/O 复杂度；
2. 支持 `DecreaseKey`，但接受更高的 I/O 成本。

本文的核心贡献，正是在这两条路线之间寻找一种新的折中方式。

---

## 5.1 不支持 `DecreaseKey` 的高效外存优先队列

外存优先队列的一个重要目标，是充分利用一次 I/O 可以传输整个数据块的特点，通过缓冲和批处理降低单个操作平均承担的 I/O 成本。

Fadel、Jakobsen、Katajainen 和 Teuhola 在 1999 年的工作 *Heaps and Heapsort on Secondary Storage* 中研究了面向二级存储的堆结构。其基本思想之一是使用多路树和较大的节点，并充分利用主存中的缓冲空间，从而减少外存页面传输。该工作能够高效支持类似 `Insert` 和删除极值元素的操作，并由此得到 I/O 高效的外部 heapsort。

对于只需要维护插入、删除和提取最小值的外存优先队列，后来可以达到

$$
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right)
$$

的摊还 I/O 复杂度。

这一复杂度与外存排序中每个元素平均承担的 I/O 成本处于相同量级。因此，从传统外存优先队列的角度看，如果不要求支持 `DecreaseKey`，问题已经可以做到非常高效。目标论文也把 Fadel 等人的工作作为“不支持 `DecreaseKey` 时可以达到最优 I/O 复杂度”的代表结果。

Wei 和 Yi 在 2014 年进一步研究了外存模型中优先队列与排序之间的关系。他们证明，在一定温和条件下，外存优先队列与外存排序在计算复杂度上具有等价关系。这进一步说明，排序复杂度是理解不含 `DecreaseKey` 的外存优先队列性能时一个非常自然的基准。

但是，这一类高效结构通常依赖于一个重要条件：

> 它们不需要高效支持 `DecreaseKey`。

一旦加入 `DecreaseKey`，数据结构就不仅需要批量组织新插入的数据，还需要处理同一个 key 的历史状态和新状态之间的关系，这正是上一节讨论的困难所在。

---

## 5.2 早期支持 `DecreaseKey` 的外存优先队列

Kumar 和 Schwabe 在 1996 年研究了用于外存图算法的数据结构，并设计了能够支持相关图算法操作的 I/O 高效数据结构。

目标论文将 Kumar–Schwabe 的结构作为早期支持完整优先队列操作、包括 `DecreaseKey` 的代表结果。其各项操作可以达到

$$
O\left(
\frac{1}{B}
\log_2
\frac{N}{B}
\right)
$$

的摊还 I/O 复杂度。

这一结果的重要性在于，它证明了外存环境中确实可以设计支持 `DecreaseKey` 的优先队列，并可将这些数据结构用于最短路径、BFS 等图问题。

但是，与不支持 `DecreaseKey` 的最优结构相比，这里的对数底数仍然是 2，而不是 $M/B$。

两者的差别是：

$$
\log_2\frac{N}{B}
$$

与

$$
\log_{M/B}\frac{N}{B}.
$$

当 $M/B$ 较大时，后者可能明显更小。

因此，在较长一段时间内，外存优先队列研究中存在这样一个明显的性能差距：

> 不支持 `DecreaseKey` 时，可以达到以 $M/B$ 为底的排序型最优复杂度；支持 `DecreaseKey` 后，已知结构只能达到以 2 为底的较差对数复杂度。

目标论文正是把这一差距视为它要处理的主要背景之一。

---

## 5.3 `DecreaseKey` 下界：性能损失是否不可避免？

面对上述差距，一个自然问题是：

> 支持 `DecreaseKey` 后的性能下降，究竟只是因为已有数据结构设计得还不够好，还是这个操作本身就必然带来额外成本？

Eenberg、Larsen 和 Yu 在 2017 年的 *DecreaseKeys are Expensive for External Memory Priority Queues* 中回答了这个问题。

他们证明，对于混合执行 `Insert`、`ExtractMin` 和 `DecreaseKey` 的操作序列，外存优先队列存在总 I/O 下界

$$
\Omega\left(
\frac{N}{B}
\log_{\log N} B
\right).
$$

这一结果在 cell-probe 模型中成立，因此并不仅限于比较型数据结构。

这一结果的重要意义在于：

> `DecreaseKey` 带来的性能损失具有理论依据，不能简单假设存在一个未知的数据结构，可以让所有操作同时拥有不支持 `DecreaseKey` 时的最优复杂度。

因此，2017 年之后，问题的重点发生了一定变化。

研究者不再只是问：

> “怎样消除 `DecreaseKey` 的所有额外开销？”

而开始更认真地考虑：

> “既然某些额外成本不可避免，应当怎样在不同操作之间分配这些成本？”

从这个角度看，目标论文采用非对称复杂度的设计并不是偶然的工程选择，而与外存优先队列已有的理论下界背景密切相关。

---

## 5.4 2019 年的另一条路线：Jiang–Larsen 的随机化优先队列

在目标论文发表的同一年，Jiang 和 Larsen 的 *A Faster External Memory Priority Queue with DecreaseKeys* 也对这一问题取得了重要进展。

他们的目标是缩小支持 `DecreaseKey` 时已有上界与理论下界之间的差距，并提出了一个随机化的外存优先队列。

该结构支持 `DecreaseKey`，其操作具有期望摊还 I/O 复杂度

$$
O\left(
\frac{1}{B}
\frac{
\log(N/B)
}{
\log\log N
}
\right).
$$

作者指出，这是多年以后首次改进支持 `DecreaseKey` 的外存优先队列上界，并同时带来了更快的外存单源最短路径算法。

Jiang–Larsen 与本文的思路并不完全相同。

Jiang–Larsen 试图改善支持 `DecreaseKey` 的优先队列整体性能，而目标论文更突出地采用了**非对称操作权衡**：

* `Update` 做到 I/O 最优；
* `ExtractMin` 和 `Delete` 接受更高成本。

因此，这两项 2019 年的工作可以看成是面对同一困难采取的两种不同路线。

一条路线试图改善支持 `DecreaseKey` 时较为均衡的整体复杂度；

另一条路线则主动让操作复杂度不平衡，把最好的复杂度集中在 `Update` 上。

对于本文而言，后者尤其适合更新次数明显多于提取次数的图算法应用场景。

---

## 5.5 本文发表之后的相关研究

本文发表之后，外存优先队列的研究并没有停止，但是后续工作并不都在直接改进本文的 x-treap。

更准确地说，研究者继续探索了不同操作集合和不同性能目标下的优先队列设计。

### 5.5.1 External-Memory Priority Queues with Optimal Insertions

2025 年，Brodal、Goodrich、Iacono 等人在 *External-Memory Priority Queues with Optimal Insertions* 中研究了另一个方向：

> 如果只要求支持 `Insert` 和 `DeleteMin`，能否让插入操作比传统排序型优先队列更快？

他们给出的结构支持摊还

$$
O\left(\frac{1}{B}\right)
$$

I/O 的 `Insert`，以及摊还

$$
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right)
$$

I/O 的 `DeleteMin`。

该工作进一步推动了外存优先队列中“不同操作采用不同复杂度预算”的思想，但是它解决的操作集合与本文不同：它并不以 `DecreaseKey` 为核心，而是重点优化 `Insert`。

因此，它不能被简单理解为“取代了本文”。

本文关心的是：

$$
\text{Update}
=

\text{Insert}+\text{DecreaseKey}
$$

的高效支持；

而 2025 年这一工作关注的是更基础的 `Insert`/`DeleteMin` 接口。

两者共同反映出一个值得注意的发展趋势：

> 外存优先队列研究越来越重视根据具体 workload 中不同操作的重要程度，设计非对称的复杂度结构，而不是要求所有操作拥有相同复杂度。

---

### 5.5.2 Lazy B-Trees

2025 年的 *Lazy B-Trees* 从另一种数据结构方向讨论了外存中的延迟更新和查询问题。

该工作把 lazy search tree 的思想推广到外存，并指出 lazy B-tree 可以作为一种外存优先队列使用。

在其给出的优先队列推论中，`DecreaseKey` 和 `Insert` 可以达到

$$
O\left(
\log_B\log_B N
\right)
$$

的最坏情况 I/O 复杂度。

作者同时明确指出，这种结构在面向排序复杂度的传统外存堆指标上并不具有竞争力，但在非常大的 $N$ 下，它可以提供比此前一些外存优先队列更快的 `DecreaseKey` 和 `Insert`，代价是删除最小值相关操作变慢。

这一结果与本文之间有一个有趣的共同点：

> 两者都接受不同操作具有明显不对称的性能。

但是它们的结构、分析目标和具体复杂度完全不同。

因此，Lazy B-Trees 更适合作为“本文之后出现的另一种不对称外存数据结构设计思路”，而不是直接作为 x-treap 的后继版本。

---

## 5.6 相关工作的整体脉络

综合来看，外存优先队列的发展可以粗略整理为以下过程：

### 第一阶段：追求排序型 I/O 最优性

当操作集合主要是 `Insert` 和 `ExtractMin` 或类似操作时，研究者可以利用缓冲和批处理，使优先队列达到与外存排序相匹配的 I/O 效率。

### 第二阶段：加入 `DecreaseKey`

图算法对 `DecreaseKey` 的需求使问题变得更复杂。

Kumar–Schwabe 等工作证明了可以在外存中支持这些操作，但其复杂度无法达到不支持 `DecreaseKey` 时的排序型最优界。

### 第三阶段：证明额外成本不可完全避免

2017 年的下界结果证明，`DecreaseKey` 确实会带来固有的额外复杂度。

于是问题从“能否让所有操作都和排序一样快”逐渐转向“怎样分配不可避免的成本”。

### 第四阶段：不同形式的复杂度权衡

2019 年，Jiang–Larsen 改进支持 `DecreaseKey` 的随机化优先队列上界。

同年，本文选择另一种路线：让 `Update` 达到 I/O 最优，同时接受 `ExtractMin` 和 `Delete` 的次优成本。

2025 年之后的工作继续探索更强的操作非对称性，例如重点优化 `Insert`，或者利用 lazy data structure 获得另一类 `DecreaseKey`/查询权衡。

因此，本文在整个研究脉络中的位置可以概括为：

> **它不是最终解决了所有外存优先队列操作的最优复杂度问题，而是明确展示了：在已有下界约束下，通过有意识地牺牲某些操作，可以让对目标应用最重要的操作达到更优复杂度，并由此改善整体算法。**

---

# 6. 本文的主要贡献

## 6.1 第一项贡献：一种新的外存优先队列操作权衡

本文最核心的数据结构结果，是构造一个支持 `Update`、`ExtractMin` 和 `Delete` 的外存优先队列。

对于维护 $N$ 个元素的数据结构，其 `Update` 摊还 I/O 复杂度为

$$
O\left(
\frac{1}{B}
\log_{M/B}
\frac{N}{B}
\right).
$$

这个界达到了外存排序意义下每个元素的最优 I/O 量级。

与此同时，对于任意固定的 $\varepsilon\in(0,1)$，`ExtractMin` 和 `Delete` 的复杂度更高。

因此，本文真正的新意不是单纯地把所有操作都改快了一点，而是提供了一种此前没有实现的复杂度分配：

> **让包含 `DecreaseKey` 的 `Update` 达到 I/O-optimal，同时让较少发生的 `ExtractMin` 和 `Delete` 承担更多成本。**

目标论文将其描述为第一个在 cache-aware 设置下，以最优 I/O 复杂度支持 `Update`，从而支持 `DecreaseKey`，同时仍保持 I/O-optimal `Insert` 的结构。

这是本文最重要的理论贡献之一。

---

## 6.2 第二项贡献：提出 x-treap

为了实现上述复杂度权衡，本文提出一种称为 **x-treap** 的递归数据结构。

x-treap 的核心思想不是在每次更新时立即寻找并修改元素，而是通过：

* 多层 buffer；
* front/rear 分区；
* 递归 subtreap；
* 批量插入；
* 批量提取；
* 延迟处理重复 key；

把大量零散操作转化为批量数据移动。

因此，x-treap 可以被理解为本文复杂度结果背后的主要技术工具。

其真正困难之处并不只是“把元素放进缓冲区”。

它必须同时保证：

1. 同一个 key 的多个版本最终能够正确消解；
2. 优先级较小的元素能够逐渐移动到更容易被提取的位置；
3. 批量移动不会破坏逻辑上的优先队列语义；
4. 每个元素在递归结构中的移动次数受到控制；
5. 所有这些操作的 I/O 成本可以进行摊还分析。

因此，后文对本文技术的详细解释将主要围绕 x-treap 展开。

---

## 6.3 第三项贡献：新的 Buffered Repository Tree

本文不仅改进了优先队列，还提出了新的 Buffered Repository Tree。

它维护一个多重集合，并支持批量式的插入和提取操作。

本文改进了此前 Buffered Repository Tree 中以 base 2 对数为主的复杂度，使其能够更充分利用外存模型中的 $M/B$ 分支能力。

这一结构并不是论文中的附属结果。

在后面的图算法应用中，作者需要同时使用：

* 新的优先队列；
* 新的 Buffered Repository Tree。

因此，图算法复杂度的改进不是单靠 x-treap 优先队列就自动得到的，而是两个数据结构结果共同作用的结果。

---

## 6.4 第四项贡献：改进稠密有向图上的外存图算法

作者最终把上述数据结构用于：

* SSSP；
* DFS；
* BFS。

对于满足相应稠密条件的有向图，本文得到

$$
O\left(
\frac{E}{B}
\log_{M/B}
\frac{E}{B}
\right)
$$

的 I/O 复杂度。

这个界与对 $E$ 个值进行外存排序的最优 I/O 复杂度相同，因此在论文给定条件下达到 I/O-optimal。

这一应用结果说明，本文提出的非对称数据结构并不仅仅是理论上的特殊构造。

它证明了一种更一般的算法设计思想：

> 如果一个高层算法对不同底层数据结构操作的调用次数高度不对称，那么底层数据结构也不一定需要追求所有操作复杂度对称。

相反，可以针对高层算法的实际操作频率，主动优化最频繁的操作。

本文正是利用这一思想，让便宜的 `Update` 抵消大量与边相关的操作成本，从而改善稠密图算法的整体 I/O 复杂度。

---

## 6.5 本文最值得关注的地方

如果要用一句话概括本文最“厉害”的地方，可以表述为：

> **本文没有试图正面消除 `DecreaseKey` 的理论困难，而是重新设计了问题中的复杂度分配方式。**

已有研究已经表明，支持 `DecreaseKey` 会带来真正的额外成本。

本文接受这一事实，然后进一步追问：

> 如果这些成本无法完全消失，是否可以把它们从最常执行的操作上移走？

作者的答案是肯定的。

通过 x-treap，他们把 `Update` 做到 I/O-optimal，把更高成本放到 `ExtractMin` 和 `Delete` 上；随后又证明，对于某些更新远多于提取的图算法，这种看似“不均衡”的优先队列反而能得到更好的总体算法复杂度。

因此，本文的创新不仅是一个具体数据结构，还包括一种重要的算法设计观点：

> **数据结构的最优性不一定意味着每一种操作单独都最优；真正重要的是数据结构的操作复杂度组合是否与上层算法的 workload 相匹配。**

---

# 7. x-treap 的核心直觉

## 7.1 x-treap 为什么要设计得这么复杂

如果只考虑普通的外存优先队列，一个很自然的做法是建立若干大小逐渐增加的 buffer。

新的 `Update` 先进入较小的 buffer；当 buffer 满时，再把一批元素向更大的 buffer 移动。这样可以通过顺序扫描和批处理，把一次 I/O 的成本分摊到许多元素上。

已有的 cache-oblivious priority queue 基本采用这种思想：不同层具有逐渐增大的 front buffer 和 rear buffer，更新先进入 rear buffer，再随着溢出逐渐向下移动；当某个元素的优先级适合进入 front buffer 时，它再逐渐向上移动，最终从最上层 front buffer 被 `ExtractMin` 取出。

问题在于，如果每一层的规模只是按照

$$
1,2,4,8,\ldots
$$

这样的几何级数增长，那么一个元素在结构中会经过大约

$$
O\left(
\log_2\frac{N}{B}
\right)
$$

层。

这正是早期支持 `DecreaseKey` 的结构中经常出现 base-2 logarithm 的原因。

本文希望把这一层数降低到与外存排序相似的

$$
O\left(
\log_{M/B}\frac{N}{B}
\right)
$$

量级，因此不能继续简单使用“每层扩大两倍”的结构。

作者的解决办法是引入一种**递归增长结构**：x-treap。它借鉴了此前 x-box 和外存哈希结构中的递归布局，把许多简单层级压缩进一个递归结构中。
（Full Version §1.3）

---

## 7.2 从 x-box 到 x-treap

理解 x-treap 的一个比较好的办法，是先暂时忽略优先级，只考虑数据如何在递归结构中移动。

论文首先给出了一个简化的 x-box 直觉。

一个规模参数为 $x$ 的递归结构大致包含：

* top buffer；
* upper-level recursive structures；
* middle buffer；
* lower-level recursive structures；
* bottom buffer。

在最简单的 $\alpha=1$ 情况下，可以把三个主要 buffer 的规模粗略理解成：

$$
x,\qquad x^{1.5},\qquad x^2.
$$

而内部的递归结构自身又是规模大约为 $\sqrt{x}$ 的同类结构。

于是元素不是简单地从：

$$
2^i\rightarrow 2^{i+1}
$$

这样一级一级移动，而是在一个“平方根递归”的结构中移动。

一个元素大致经历：

$$
\text{top}
\rightarrow
\text{upper subtreaps}
\rightarrow
\text{middle}
\rightarrow
\text{lower subtreaps}
\rightarrow
\text{bottom}.
$$

每次某个区域出现 overflow，结构都尽量通过批量扫描，把大量元素一起移动到相邻递归区域。

这种递归设计的关键好处是：**元素经过的有效层数不再由简单的 base-2 几何增长决定，而由递归尺度决定。**

完整版论文将这种结构作为 x-treap 的几何基础。

---

## 7.3 x-treap 实际上同时维护两种“顺序”

x-treap 并不是普通意义上的二叉堆，也不是标准的随机 treap。

它更像是把两个不同维度的顺序同时叠加到一个递归结构中。

第一个维度是：

> **key 的顺序。**

递归 subtreap 按照 key range 被划分。

例如，一个 subtreap 只负责：

$$
[k_1,k_2)
$$

范围中的 key，而相邻 subtreap 负责：

$$
[k_2,k_3).
$$

因此，当需要处理相同 key、合并重复 key 或将元素分配到子结构时，可以利用 key-sorted array 进行顺序扫描，而不需要逐个随机查找。

第二个维度是：

> **priority 的顺序。**

x-treap 要保证越靠近结构顶部的某些元素具有越小的 priority，使 `ExtractMin` 最终只需要关注顶部区域。

论文把这一点称为一种 **treap-like arrangement**：

* key 决定一个方向上的递归划分；
* priority 决定另一个方向上的上下关系。

因此，可以把 x-treap 粗略想象成一个二维结构：

```text
            priority 方向
            小
            ↑
            │       top
            │
            │   upper subtreaps
            │
            │      middle
            │
            │   lower subtreaps
            │
            │      bottom
            ↓
            大

        ←------ key ------→
```

这个二维观点非常重要。

后续很多看起来复杂的 invariant，本质上都是在保证：

* 水平方向能够按 key 批量处理；
* 垂直方向能够按 priority 找到最小元素。

论文明确描述 x-treap 为使用 key 和 priority 两个维度形成的 treap-like arrangement。

---

## 7.4 rear buffer 和 front buffer 分别扮演什么角色

x-treap 中每一个主要 buffer 都被分成：

* front buffer；
* rear buffer。

理解这两个部分，是理解整个结构的关键。

可以先用一个并不完全正式、但非常有用的直觉来理解：

> **rear buffer 更像“更新流”；front buffer 更像“候选最小元素流”。**

### rear buffer：让更新便宜地向下流动

当新的更新进入数据结构后，它通常首先出现在较高位置的 rear buffer 中。

这些元素不需要立刻被安排到最终的 priority 位置。

当 rear buffer 逐渐变满时，大量元素可以一起被推向结构更深的位置。

因此 rear buffer 的主要价值是：

> **允许 Update 延迟整理，从而使大量元素共享一次批量移动的 I/O 成本。**

论文在高层分析中描述了一个元素的典型生命周期：元素从顶部 rear buffer 开始，随着 buffer overflow 逐渐向下移动。

### front buffer：保存更接近 ExtractMin 的元素

front buffer 则承担另一种职责。

对于同一个 buffer，front 中元素的 priority 必须小于 rear 中元素的 priority。

而且更重要的是，一个 front buffer 中的元素，还必须比位于它下方 buffer 中的元素拥有更小的 priority。

因此，front buffer 可以理解为：

> **从当前区域中筛选出的、更加接近全局最小值的一批候选元素。**

当最上层 front buffer 中的元素不足时，`Flush-Up` 会从下面的结构中找出优先级较小的元素，并把它们向上补充。

最终，`Batched-ExtractMin` 只需要从 top front buffer 中取元素。论文明确指出，经过 `Flush-Up` 后，top front buffer 保存的是结构中最小 priority 的代表元素。

---

## 7.5 一个元素的典型生命周期

忽略许多实现细节，一个元素在 x-treap 中可以经历下面的过程：

```text
Update
  ↓
top rear
  ↓
rear buffers 向下移动
  ↓
某个位置被 Resolve
  ↓
rear → front
  ↓
front buffers 向上移动
  ↓
top front
  ↓
ExtractMin
```

这里存在明显的不对称性：

### 向下运动

Update 产生的元素可以大量聚集后一起向下移动。

这种移动主要由扫描完成，因此每个元素承担的成本可以很低。

### 向上运动

一个元素只有在成为比较重要的“小 priority 候选”之后，才需要进入 front buffer，并逐渐向上移动。

这类操作本身更加昂贵。

因此，本文实际上把：

> **便宜的向下传播**

主要归给 `Update`，

而把：

> **昂贵的向上恢复最小元素**

主要归给 `ExtractMin`。

这正好对应前文已经讨论过的复杂度权衡：

$$
\text{cheap Update}
\quad\Longleftrightarrow\quad
\text{expensive ExtractMin}.
$$

论文的高层解释正是通过这种“先向下、再转入 front、再向上”的元素生命周期解释其摊还复杂度设计。

---

## 7.6 为什么相同 key 的多个版本不会立即被删除

这一设计还需要解决 `DecreaseKey` 带来的重复版本问题。

例如连续执行：

$$
(A,10),
\qquad
(A,6),
\qquad
(A,3).
$$

x-treap 并不要求每次出现 $(A,3)$ 时，立即在整个外存结构中寻找并删除 $(A,10)$ 和 $(A,6)$。

那样会重新产生大量随机 I/O。

相反，结构允许同一个 key 的物理副本暂时存在于不同区域。

但是，在**同一个已经 Resolve 的 buffer 中**，每个 key 最终只保留其中 priority 最小的版本。

也就是说：

$$
(A,10),(A,6),(A,3)
$$

在同一个 buffer 被 Resolve 后，只留下：

$$
(A,3).
$$

这样做把“全局立即删除旧版本”的问题，转换成了：

> **元素经过某个 buffer 时，顺便批量消除该 buffer 中的重复版本。**

由于 buffer 按 key 排序，这一去重可以通过扫描完成，而不是通过随机查找完成。`Resolve` 的正式算法正是把 front/rear 按 key merge，再为每个 key 保留最低 priority。

---

## 7.7 物理存储和逻辑状态必须区分

因此，理解 x-treap 时必须区分两个概念：

### 物理上存储的元素

结构中可能同时存在：

$$
(A,10),
(A,6),
(A,3).
$$

### 逻辑上代表的元素

从优先队列语义来看，key $A$ 当前真正代表的 priority 应该只有：

$$
(A,3).
$$

论文把 x-treap 当前逻辑代表的集合记作：

$$
D.rep=
\bigcup_{k:\exists p,(k,p)\in D}
\left{
\left(
k,
\min{p:(k,p)\in D}
\right)
\right}.
$$

因此，x-treap 本质上允许：

> **物理结构暂时有冗余，但逻辑语义保持唯一。**

这正是延迟处理 `DecreaseKey` 的关键。

如果不能接受这种物理冗余，就必须在每次更新时马上找到旧版本，而那正是外存算法希望避免的随机访问。论文将 minimum-priority copy 定义为该 key 的 representative element。

---

## 7.8 x-treap 最重要的设计思想

从目前的层次来看，x-treap 的核心可以概括成四句话：

### 第一：不要立即完成 Update

先缓存，让更新批量向下传播。

### 第二：key 和 priority 分工

key 用于决定递归分区和批量 merge；

priority 用于决定哪些元素应该靠近结构顶部。

### 第三：允许物理冗余

不同版本可以暂时共存，从而避免随机删除。

### 第四：把昂贵工作转移给 ExtractMin

Update 主要沿 rear buffer 便宜地下沉；

真正需要找最小元素时，再通过 front buffer 和 `Flush-Up` 承担较昂贵的工作。

因此，x-treap 的复杂性并不是为了“做一个更复杂的堆”，而是为了实现前文的核心目标：

> **把外存优先队列中不可避免的整理成本，从频繁发生的 Update 上移走。**

---

# 8. x-treap 的具体结构

## 8.1 参数与逻辑表示

一个 x-treap 记作 $D$。

它有一个尺度参数：

$$
D.x.
$$

需要特别注意：

> $D.x$ 并不是当前存储的元素个数。

它是决定整个递归结构各 buffer 大小的**尺度参数**。

对于参数：

$$
\alpha\in(0,1],
$$

一个 x-treap 的总容量小于：

$$
2(D.x)^{1+\alpha}.
$$

它同时负责一个 key range：

$$
[D.k_{\min},D.k_{\max}).
$$

x-treap 中可能物理存储多个具有相同 key 的元素，但其逻辑集合 $D.rep$ 对每个仍被表示的 key 只保留最低 priority 的 pair。

---

## 8.2 三个主 buffer

非 base-case 的 x-treap 包含三个主要 buffer：

```text
top buffer
middle buffer
bottom buffer
```

其容量分别为：

$$
D.x,
$$

$$
(D.x)^{1+\frac{\alpha}{2}},
$$

以及：

$$
(D.x)^{1+\alpha}.
$$

因此，从 top 到 bottom，buffer 会迅速变大。

如果取最容易理解的：

$$
\alpha=1,
$$

则三个规模分别变成：

$$
D.x,
\qquad
(D.x)^{3/2},
\qquad
(D.x)^2.
$$

这就是论文在高层直觉中使用的：

$$
x,\quad x^{1.5},\quad x^2
$$

结构。

正式定义使用参数 $\alpha$，是为了控制最终的复杂度权衡。

---

## 8.3 两层 recursive subtreap

三个主 buffer 之间还夹着两层递归结构。

整体排列为：

```text
top buffer

upper-level subtreaps

middle buffer

lower-level subtreaps

bottom buffer
```

每一个递归 subtreap 本身又是一个：

$$
\sqrt{D.x}\text{-treap}.
$$

因此，整个结构具有明显的自相似性质。

### upper level

upper level 最多包含：

$$
\frac14(D.x)^{1/2}
$$

个 subtreap。

### lower level

lower level 最多包含：

$$
\frac14(D.x)^{\frac{1+\alpha}{2}}
$$

个 subtreap。

这些常数 $\frac14$ 本身不是理解结构的重点。

真正重要的是指数。

一个 $\sqrt{D.x}$-treap 的容量大约处在

$$
(D.x)^{\frac{1+\alpha}{2}}
$$

这一尺度。

因此 upper level 的总容量大致是：

$$
(D.x)^{1/2}
\cdot
(D.x)^{\frac{1+\alpha}{2}}
==========================

(D.x)^{1+\frac{\alpha}{2}},
$$

与 middle buffer 的尺度相匹配。

类似地，lower level 的总容量大致是：

$$
(D.x)^{\frac{1+\alpha}{2}}
\cdot
(D.x)^{\frac{1+\alpha}{2}}
==========================

(D.x)^{1+\alpha},
$$

与 bottom buffer 的尺度相匹配。

这解释了一个非常关键的结构设计：

> **相邻层级能够容纳数量级相近的数据，因此 overflow 或 underflow 时可以通过大规模批量移动来重新平衡。**

这一点正是 x-box 递归设计降低 I/O 的核心。

---

## 8.4 每个 buffer 都进一步分为 front 和 rear

top、middle 和 bottom 并不是单一数组。

每一个 buffer 都进一步分为：

```text
front | rear
```

因此，从逻辑上看，一个 x-treap 实际包含：

```text
top front      top rear

upper recursive subtreaps

middle front   middle rear

lower recursive subtreaps

bottom front   bottom rear
```

front/rear 的区别不是按照 key range 划分。

两者最重要的区别在于 **priority**。

front 保存该 buffer 中 priority 较小的一部分元素；

rear 保存 priority 较大的一部分元素。

同时，两边内部仍然按照 key 排序。

这使得同一个 buffer 同时满足两个需求：

* 可以按 key 做 merge 和 duplicate elimination；
* 可以按 priority 区分“更接近 ExtractMin”的元素和“仍在更新流中”的元素。

---

## 8.5 “above”和“below”不是简单的数组上下位置

论文为了描述 priority 关系，定义了一个 partial order：

$$
\text{top buffer}
\preceq
\text{upper subtreaps}
\preceq
\text{middle buffer}
\preceq
\text{lower subtreaps}
\preceq
\text{bottom buffer}.
$$

论文把这个顺序称为：

* above；
* below。

这里的“上”和“下”不是简单的内存地址位置，而是整个递归结构中的逻辑顺序。

例如：

* top 在 upper subtreaps 上方；
* upper subtreaps 在 middle 上方；
* middle 在 lower subtreaps 上方；
* lower subtreaps 在 bottom 上方。

这个 partial order 是后续 priority invariant 的基础。

---

# 8.6 五个核心 Invariant

x-treap 的正确性主要由五个结构不变量保证。

这些 invariant 在每次**接口操作完成后**必须成立。

在 `Resolve`、`Flush-Up`、`Flush-Down` 等辅助操作执行过程中，它们可以暂时被破坏，但操作结束时必须恢复。

---

### Invariant 1：buffer 内按 key 排序

对于任意 buffer $b$：

> front 和 rear 中的元素分别按照 key 排序，并且元素在数组中 left-justified。

这里的 left-justified 可以理解为：

```text
有元素 有元素 有元素 有元素 空 空 空
```

而不是：

```text
有元素 空 有元素 空 有元素 ...
```

这样 buffer 可以通过连续扫描进行：

* merge；
* split；
* duplicate elimination。

这是保证外存批处理效率的基础。

---

### Invariant 2：front 的 priority 小于 rear

同一个 buffer 中：

> front 中所有元素的 priority 都小于 rear 中所有元素的 priority。

因此可以粗略理解成：

```text
small priority  |  large priority
     front      |      rear
```

这就是 front 能够作为“更接近 ExtractMin 的候选集合”的原因。

`Resolve` 的一个主要任务，就是在其他操作暂时打乱这一关系后恢复这个 invariant。

---

### Invariant 3：front 的元素比所有下方元素优先级更小

对于任意 buffer $b$：

> $b$ 的 front buffer 中元素的 priority，小于所有位于 $b$ 下方 buffer 中元素的 priority。

这比 Invariant 2 更强。

Invariant 2 只比较：

```text
同一个 buffer：
front vs rear
```

Invariant 3 比较的是：

```text
front
vs
整个结构中位于它下面的 buffer
```

这意味着，如果 top front 中存在足够多元素，那么这些元素确实是整个 x-treap 中 priority 最小的一批 representative elements。

因此，`Batched-ExtractMin` 不需要搜索整棵递归结构，只需要保证 top front 被正确补充，然后从那里提取即可。

---

### Invariant 4：recursive subtreap 按 key range 分区

假设 top 或 middle buffer $b$ 的 key range 为：

$$
[b.k_{\min},b.k_{\max}).
$$

位于它正下方的 subtreap 会把这一范围划分成若干连续且互不重叠的 key range。

若这些 subtreap 为：

$$
D_1,D_2,\ldots,D_r,
$$

则有：

$$
b.k_{\min}
==========

D_1.k_{\min}
<
D_1.k_{\max}
============

D_2.k_{\min}
<
\cdots
<
D_r.k_{\max}
============

b.k_{\max}.
$$

因此，一旦知道某个元素的 key，就能够确定它属于哪个 subtreap 的 key range。

更重要的是，当许多元素已经按 key 排序时，可以按顺序扫描整个数组，同时按 key range 把它们批量分发给相应 subtreap。

这避免了逐个元素进行随机 child lookup。

---

### Invariant 5：下层 buffer 非空意味着中间递归层也非空

如果 middle buffer 非空，则至少有一个 upper-level subtreap 非空。

类似地，如果 bottom buffer 非空，则至少有一个 lower-level subtreap 非空。

也就是说，结构不能出现类似：

```text
middle 有元素
但所有 upper subtreap 都完全为空
```

这样的状态。

这一 invariant 保证了递归层和相邻 buffer 之间具有一致的结构关系，并为后续的 `Flush-Up` 和 `Flush-Down` 提供合法的递归路径。正式论文将其列为第五个结构不变量。

---

## 8.7 为什么前三个 invariant 最重要

从理解优先队列正确性的角度，前三个 invariant 尤其关键。

它们分别保证了：

### Invariant 1

$$
\text{key 可批量处理}
$$

### Invariant 2

$$
\text{front 是 buffer 内较小 priority 的部分}
$$

### Invariant 3

$$
\text{越靠上的 front 越接近全局 minimum}
$$

因此，x-treap 的两个核心维度再次体现出来：

```text
Invariant 1
    ↓
key order

Invariant 2 + 3
    ↓
priority order
```

而 Invariant 4、5 主要负责让这种顺序能够嵌入递归结构中持续维护。

---

## 8.8 Base Case

x-treap 不会无限递归下去。

完整版中，当：

$$
D.x
\le
c'\lambda^{\frac{1}{1+\alpha}}
$$

时，递归停止，改用一个简单的 base-case structure。

由于一个 x-treap 的容量尺度为：

$$
O\left(
(D.x)^{1+\alpha}
\right),
$$

因此在这一阈值下，base case 实际存储的元素量为：

$$
O(\lambda).
$$

在 cache-aware 情况下，作者取：

$$
\lambda=\Theta(M),
$$

于是整个 base case 可以落在主存规模附近。

这时就没有必要继续维持复杂的递归 buffer 结构，可以使用简单数组进行处理。

这种设计还有一个重要意义：

> **x-treap 的递归结构最终把大问题不断压缩成能够在较快存储层中廉价处理的小问题。**

正式完整版给出的 base-case 阈值和其 `Batched-Insert`、`Batched-ExtractMin` 性能正是整个递归分析的终止条件。

---

## 8.9 x-treap 的整体结构总结

到这里，可以把一个 x-treap $D$ 总结成下面的结构：

```text
                 top
          ┌────────────────┐
          │ front | rear   │
          └────────────────┘

              upper level
       ┌────┬────┬────┬──────┐
       │ √x │ √x │ √x │ ...  │
       └────┴────┴────┴──────┘

                middle
       ┌──────────────────────┐
       │   front  |  rear     │
       └──────────────────────┘

              lower level
   ┌────┬────┬────┬────┬─────────┐
   │ √x │ √x │ √x │ √x │  ...    │
   └────┴────┴────┴────┴─────────┘

                bottom
   ┌──────────────────────────────┐
   │        front | rear          │
   └──────────────────────────────┘
```

其中：

* **key** 决定元素应该进入哪个 recursive subtreap；
* **priority** 决定元素更应该处在 front 还是 rear，以及应该位于结构的较高还是较低位置；
* **rear** 主要承载便宜的更新传播；
* **front** 主要承载最小元素候选；
* **递归结构** 降低一个元素需要经过的有效层数；
* **Resolve** 负责局部消除重复 key 并恢复 front/rear 的 priority 关系；
* **Flush-Up** 将较小 priority 的元素向上移动；
* **Flush-Down** 将较大 priority 的元素向下移动。

因此，后续所有复杂的辅助操作实际上都可以看成是在解决同一个问题：

> **当某个 buffer 太满、太空或者 front/rear 顺序被破坏时，怎样只通过大规模批处理重新恢复这五个 invariant，而不是逐个元素进行随机访问。**

这正是下一节分析 `Resolve`、`Flush-Up`、`Flush-Down` 等具体操作时的主线。
