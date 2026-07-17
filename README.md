# External Memory Priority Queues with Decrease-Key

## 1. Project Information

This repository records my reading and review process for the paper:

**External Memory Priority Queues with Decrease-Key and Applications to Graph Algorithms**

- Authors: John Iacono, Riko Jacob, Konstantinos Tsakalidis
- Conference: ESA 2019
- DOI: 10.4230/LIPIcs.ESA.2019.60
- Official PDF:
  <https://drops.dagstuhl.de/storage/00lipics/lipics-vol144-esa2019/LIPIcs.ESA.2019.60/LIPIcs.ESA.2019.60.pdf>
- Full arXiv version:
  <https://arxiv.org/pdf/1903.03147>

## 2. Current Understanding

The paper studies priority queues in the external-memory model.
Its main goal is to support the Update operation, which combines
Insert and DecreaseKey, while reducing the number of block transfers
between external storage and internal memory.

The main data structure is a recursive structure called an x-treap.
The authors use batched operations, hierarchical buffers and recursive
substructures to reduce random I/O. The resulting priority queue is
then applied to external-memory SSSP, DFS and BFS algorithms.

This is only my preliminary understanding and will be corrected and
expanded during the reading process.

## 3. Repository Structure

- `review/`: unified review draft
- `notes/`: individual reading notes
- `sources/`: papers and reference map
- `process/`: task allocation, meetings, questions and AI-use records
- `figures/`: self-made explanatory figures

## 4. Current Tasks

See `process/task-board.md`.

## 5. AI Usage

AI tools may be used to help locate sources and understand difficult
definitions or proofs. All important factual and mathematical claims
will be checked against the original papers. AI usage is recorded in
`process/ai-usage-log.md`.
