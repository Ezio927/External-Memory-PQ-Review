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
