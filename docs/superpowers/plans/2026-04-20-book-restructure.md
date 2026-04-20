# ArcKit Book Restructure Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restructure `docs/book/ARCKIT-BOOK.md` from 13 internals-ordered chapters into a 4-part, 20-chapter book organised around the governance lifecycle (Part II) with internals as reference (Part III). Dissolve the monolithic Chapter 4 ("Commands Deep Dive") into 8 phase-based chapters, renumber and lightly reorder Parts III/IV, add new chapter intros/outros, and keep all existing content.

**Architecture:** Three mechanical phases followed by one writing phase and one cross-cutting cleanup phase. Phase 1 extracts content from the two fat chapters (Ch 3, Ch 4) into 8 new Part II chapter skeletons. Phase 2 renumbers Parts III and IV (no content changes). Phase 3 adds Part separators and regenerates the TOC. Phase 4 writes the new per-chapter intros and outros. Phase 5 fixes cross-references, re-sorts Appendix A, and verifies nothing was lost. Each phase ends in a commit so the branch can be paused and resumed safely.

**Tech Stack:** Markdown, `markdownlint-cli2`, `git`, `wc`, `grep`.

**Baseline (2026-04-20, re-baselined after `feat/book-deepwiki-diagrams` merged):** 4,025 lines, 25,749 words, 18 embedded Mermaid diagrams (Figures 3-1, 4-1 through 4-11, 7-1 / 7-2, 9-1, 10-1 / 10-2, 12-1). Use these numbers as post-restructure sanity checks — word count should rise by ~2,400 from new prose. All 18 Mermaid figures must still be present after restructure; grep verification in Task 19 below.

**Important:** this plan was originally written against a pre-diagram 2,905-line book. Line numbers in the "Line-range map" below have been updated. Every task still calls `grep -n` before operating, so the line-number drift is already handled automatically — just do not hard-code any of these numbers into edits.

---

## Why this plan exists

The current book (v4.6.13, 2026-04-20) is technically complete but organised around *ArcKit's internals* (Commands, Agents, Hooks, Skills, Templates, Autoresearch). An EA practitioner picking up the book to *run a project* has to reverse-engineer the governance phase order from the dependency matrix. Deepwiki's phase-based left nav (Foundation → Requirements → Research → Procurement → Design Reviews → Delivery → Compliance) matches how readers use ArcKit.

This plan reshapes the book into that order without rewriting existing material — 90% of the work is moving existing sections into new chapter homes. About 2,400 words of new intro/outro prose is the only genuinely new writing.

---

## Target TOC (post-restructure)

```
PART I — INTRODUCTION
  1. What is ArcKit?
  2. Getting Started
  3. The ArcKit Workflow                    (dependency matrix, 5 paths, Gantt)

PART II — THE GOVERNANCE LIFECYCLE
  4. Foundation: Principles, Stakeholders, Risk, Business Case
  5. Requirements and Design
  6. Strategy and Wardley Mapping
  7. Market Research and Knowledge Compounding
  8. Procurement and Vendor Evaluation
  9. Design Reviews (HLD / DLD)
 10. Delivery and Operations
 11. Compliance and Quality Assurance

PART III — HOW ARCKIT WORKS
 12. The Prompt Engineering Anatomy        (absorbs Command Anatomy)
 13. The Agent System
 14. The Hook System
 15. Skills, MCP Servers and References
 16. The Template and Document System       (moved before Multi-AI)
 17. Multi-AI Distribution Architecture
 18. The Autoresearch System

PART IV — OPERATIONS AND HISTORY
 19. Development and Operations
 20. Highlights from the Commit History

Appendices A–F (unchanged, except Appendix A re-sorted by new chapter order)
```

---

## File Structure

Modified:
- `docs/book/ARCKIT-BOOK.md` — the entire restructure

Created (intermediate scratch file, deleted in final task):
- `/tmp/arckit-book-baseline.txt` — baseline word/line counts for verification

No other files change. Images in `docs/book/images/` stay where they are; image references in the book are updated only if an image is referenced from a newly-moved section.

---

## Line-range map (SOURCE → DESTINATION)

Line numbers below are from the file after `feat/book-deepwiki-diagrams` merged (commit `b242ac94`, re-baselined 2026-04-20). **Re-grep every anchor before operating** — each task already instructs this. Line numbers are advisory only; the anchor strings (heading text, unique phrases) are authoritative.

### From Chapter 3 (The ArcKit Workflow)
| Source lines | Content | Destination |
|---|---|---|
| 332–523 | DSM, 5 workflow paths, decision tree, Gantt, Fast-Track, Compliance-Only, plus Figure 3-1 Mermaid (command ecosystem by phase) | Stays in Ch 3 |
| 524–537 | Wardley Mapping Suite | **New Ch 6** (leave 3-line teaser in Ch 3) |
| 538–583 | Wardley Mathematical Models | **New Ch 6** |
| 584–594 | Government Code Discovery | **New Ch 7** (leave 2-line teaser in Ch 3) |

### From Chapter 4 (Commands Deep Dive) — entirely dissolved
**All 11 Mermaid figures (4-1 through 4-11) now sit adjacent to their command tables or deep-dive sections.** Each figure moves with its host section — do not separate them.

| Source lines | Content | Destination |
|---|---|---|
| 598–641 | Command Anatomy (frontmatter, `$ARGUMENTS`) | **Ch 12** (Prompt Engineering) intro |
| 644–744 | Foundation command table (Tier 0–1) **+ Figure 4-1** (Stakeholder tree) | **New Ch 4** |
| 745–752 | Business Case table (`risk`, `sobc`, `requirements`) | Split: `risk`+`sobc` → **Ch 4**; `requirements` → **Ch 5** |
| 753–878 | Research and Discovery commands **+ Figures 4-4 (cloud research agents) and 4-5 (knowledge compounding)** | **New Ch 7** |
| 879–887 | Data Architecture commands | **New Ch 5** |
| 888–897 | Wardley Mapping commands | **New Ch 6** |
| 898–1031 | Strategic Planning commands **+ Figures 4-2 (strategy decision flow) and 4-3 (roadmap command end-to-end)** | Split: `roadmap`,`strategy`,`platform-design`,`framework` → **Ch 6** (+ both figures); `glossary`,`diagram`,`adr` → **Ch 5** |
| 1032–1098 | Procurement commands **+ Figure 4-6 (vendor evaluation framework)** | **New Ch 8** |
| 1099–1156 | Design Review commands **+ Figure 4-7 (design review phases)** | **New Ch 9** |
| 1157–1164 | Implementation commands | **New Ch 10** |
| 1165–1174 | Operations commands | **New Ch 10** |
| 1175–1247 | Compliance commands **+ Figure 4-8 (UK Gov compliance gates)** | **New Ch 11** |
| 1248–1441 | Quality and Analysis commands **+ Figures 4-9 (/arckit.analyze flow) and 4-10 (traceability DAG)** | **New Ch 11** |
| 1442–1450 | Reporting and Publishing commands | **New Ch 10** |
| 1451–1456 | Conversational Gathering Pattern | **Ch 12** |
| 1457–1478 | Deep Dive: Wardley Map Command | **New Ch 6** |
| 1479–1540 | Deep Dive: Health Command **+ Figure 4-11 (health scan rule flow)** | **New Ch 11** |
| 1541–1553 | Deep Dive: Score Command | **New Ch 8** |

### Diagrams that stay in place (Ch 7, 9, 10, 12)
These chapters get renumbered only; their diagrams move with them by default:

- **Ch 7 (Hooks) → Ch 14:** Figures 7-1 (hook lifecycle) and 7-2 (filename validator). Stay in place; chapter heading changes.
- **Ch 9 (Multi-AI) → Ch 17:** Figure 9-1 (converter pipeline Mermaid, sitting alongside the SVG).
- **Ch 10 (Templates) → Ch 16:** Figures 10-1 (document type registry) and 10-2 (project → manifest aggregation).
- **Ch 12 (Dev/Ops) → Ch 19:** Figure 12-1 (release pipeline sequence).

### From Chapter 12 (old Development and Operations)
| Source lines | Content | Destination |
|---|---|---|
| 3152–3186 | Pages Dashboard + Dependency Map Visualization | **New Ch 10** (Delivery and Operations) — Pages is a delivery artifact |

All other old-Ch-12 content stays in place and becomes new Ch 19. Figure 12-1 (release pipeline) stays in old Ch 12 / new Ch 19 — do **not** move it.

---

## Execution order (safest)

```
Phase 1 (mechanical):   Tasks 1–11  → content moves to their new chapter homes
Phase 2 (renumbering):  Tasks 12–13 → rename chapter headings for Parts III/IV
Phase 3 (structure):    Tasks 14–15 → Part separators, TOC regeneration
Phase 4 (prose):        Tasks 16    → write new intros/outros (~2,400 words)
Phase 5 (cleanup):      Tasks 17–20 → cross-refs, Appendix A, verify, PR
```

Per CLAUDE.md: all work on a feature branch, merged to `main` via PR. Commit after each task.

---

## Task 1: Create feature branch and baseline snapshot

**Files:**
- None (git-only)
- Scratch: `/tmp/arckit-book-baseline.txt`

- [ ] **Step 1: Verify clean working tree**

```bash
git status
```

Expected: `working tree clean`, branch `main`.

- [ ] **Step 2: Pull latest main**

```bash
git checkout main && git pull
```

- [ ] **Step 3: Create feature branch**

```bash
git checkout -b feat/book-restructure
```

- [ ] **Step 4: Record baseline metrics**

```bash
{
  echo "=== Baseline (pre-restructure) ==="
  echo "Date: $(date -Iseconds)"
  echo "Commit: $(git rev-parse HEAD)"
  echo
  wc -l -w docs/book/ARCKIT-BOOK.md
  echo
  echo "Chapter headings:"
  grep -n '^## Chapter\|^## Appendix' docs/book/ARCKIT-BOOK.md
} > /tmp/arckit-book-baseline.txt
cat /tmp/arckit-book-baseline.txt
```

Expected output contains: `2905` lines, `22234` words. If either differs, the file has changed since plan-write time — stop and reconcile line numbers in the map above against current line numbers before proceeding.

- [ ] **Step 5: Commit baseline snapshot is not needed; scratch file is gitignored by default**

Do not `git add /tmp/arckit-book-baseline.txt`. It stays local for reference during the restructure.

---

## Task 2: Insert the 8 Part II chapter skeletons after Chapter 3

**Purpose:** Create empty headings for new chapters 4–11 before any content moves. This gives every subsequent Task a concrete insertion target.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md` (insert after line 496, before old `## Chapter 4`)

- [ ] **Step 1: Confirm insertion point**

```bash
sed -n '494,500p' docs/book/ARCKIT-BOOK.md
```

Expected: line 496 is `---`, line 498 is `## Chapter 4: Commands Deep Dive`.

- [ ] **Step 2: Use Edit tool to insert skeletons**

Insert the block below **immediately after** the `---` at line 496 and **before** `## Chapter 4: Commands Deep Dive`. Use the Edit tool with `old_string` = the `---\n\n## Chapter 4: Commands Deep Dive` pair and `new_string` = the same pair with the block below inserted between them.

```markdown
## Chapter 4: Foundation — Principles, Stakeholders, Risk, Business Case

_Intro pending (Task 16)._

---

## Chapter 5: Requirements and Design

_Intro pending (Task 16)._

---

## Chapter 6: Strategy and Wardley Mapping

_Intro pending (Task 16)._

---

## Chapter 7: Market Research and Knowledge Compounding

_Intro pending (Task 16)._

---

## Chapter 8: Procurement and Vendor Evaluation

_Intro pending (Task 16)._

---

## Chapter 9: Design Reviews (HLD / DLD)

_Intro pending (Task 16)._

---

## Chapter 10: Delivery and Operations

_Intro pending (Task 16)._

---

## Chapter 11: Compliance and Quality Assurance

_Intro pending (Task 16)._

---

```

**IMPORTANT:** The old `## Chapter 4: Commands Deep Dive` heading stays in place below the skeletons — it will be dissolved in Tasks 3–11 and deleted when empty.

- [ ] **Step 3: Verify skeletons inserted**

```bash
grep -n '^## Chapter' docs/book/ARCKIT-BOOK.md | head -15
```

Expected: new chapters 4–11 appear, then the old Chapter 4 (still called "Commands Deep Dive"), then the rest.

- [ ] **Step 4: Lint check**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
```

Expected: no new violations vs baseline. If any, fix only those introduced by the skeleton block.

- [ ] **Step 5: Commit**

```bash
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: insert 8 Part II chapter skeletons"
```

---

## Task 3: Move Foundation content into Chapter 4

**Purpose:** Populate new Chapter 4 with the Foundation command tables plus `risk` and `sobc` from the Business Case table.

**Source content:**
- Old Ch 4 line range **546–554** (Foundation Tier 0-1 table)
- Old Ch 4 line range **556–562** (Business Case table) — keep `risk` and `sobc` rows; leave `requirements` row for Task 4

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Re-check line numbers against current file**

```bash
grep -n '^#### Foundation\|^#### Business Case' docs/book/ARCKIT-BOOK.md
```

Line numbers will have drifted by the skeleton insert — add ~40 lines.

- [ ] **Step 2: Copy Foundation table content**

Read the Foundation subsection (heading `#### Foundation (Tier 0-1)` through the table's last row — 5 rows: `plan`, `principles`, `stakeholders`, `start`, `init`). Preserve verbatim.

- [ ] **Step 3: Copy `risk` and `sobc` rows from Business Case table**

Read lines for the Business Case subsection (heading `#### Business Case (Tier 2-4)`). The table has 3 rows — `risk`, `sobc`, `requirements`. Keep `risk` and `sobc`; exclude `requirements`.

- [ ] **Step 4: Insert under new Chapter 4 heading**

Use the Edit tool to replace the `_Intro pending (Task 16)._` line under `## Chapter 4: Foundation — …` with a stub-then-table block:

```markdown
_Intro pending (Task 16)._

### Foundation Commands (Tier 0-1)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `plan` | PLAN | high | Create implementation plan with phases, milestones, risks |
| `principles` | PRIN | max | Define architecture principles (goes in 000-global) |
| `stakeholders` | STKE | max | Analyze stakeholder drivers, goals, outcomes, RACI |
| `start` | -- | -- | Guided onboarding -- presents a tailored command plan |
| `init` | -- | -- | Initialize project directory structure |

### Risk Register and Business Case (Tier 2-3)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `risk` | RISK | max | HM Treasury Orange Book risk management |
| `sobc` | SOBC | max | HM Treasury Green Book Strategic Outline Business Case |
```

- [ ] **Step 5: Delete the now-moved content from old Chapter 4**

Use the Edit tool to remove:
- The `#### Foundation (Tier 0-1)` subsection (heading + table)
- The `risk` and `sobc` rows from the `#### Business Case (Tier 2-4)` table (keep the subsection heading and the `requirements` row for Task 4)

- [ ] **Step 6: Verify no content loss**

```bash
grep -c 'principles.*PRIN' docs/book/ARCKIT-BOOK.md
grep -c 'HM Treasury Orange Book' docs/book/ARCKIT-BOOK.md
```

Both should still return `≥1`. If `0`, content was lost — revert the Edit and re-apply carefully.

- [ ] **Step 7: Lint**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
```

- [ ] **Step 8: Commit**

```bash
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Foundation commands into new Chapter 4"
```

---

## Task 4: Move Requirements and Design content into Chapter 5

**Purpose:** Populate Ch 5 with `requirements`, the Data Architecture table, and the three Strategic Planning commands that belong with detailed design (`glossary`, `diagram`, `adr`).

**Source content:**
- `requirements` row from Business Case table (still in old Ch 4 after Task 3)
- Old Ch 4 Data Architecture table
- Old Ch 4 Strategic Planning table — rows `glossary`, `diagram`, `adr` only

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert content block under Ch 5 heading**

Replace the `_Intro pending._` placeholder under `## Chapter 5: Requirements and Design` with:

```markdown
_Intro pending (Task 16)._

### Requirements

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `requirements` | REQ | max | Business and technical requirements (BR/FR/NFR/INT/DR) |

### Data Architecture (Tier 6)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `data-model` | DATA | max | Comprehensive data modeling with ERD |
| `data-mesh-contract` | DMC | high | Data mesh contract generation |
| `dpia` | DPIA | max | Data Protection Impact Assessment |
| `dfd` | DFD | high | Data flow diagrams |

### Detailed Design Artifacts

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `glossary` | GLOS | medium | Project glossary with contextual definitions |
| `diagram` | DIAG | high | Architecture diagrams (Mermaid) |
| `adr` | ADR | max | Architecture Decision Records |
```

- [ ] **Step 2: Delete moved rows from old Chapter 4**

Remove from old Ch 4:
- The `requirements` row (last row of Business Case table)
- The entire `#### Data Architecture (Tier 6)` subsection
- The `glossary`, `diagram`, `adr` rows from the `#### Strategic Planning (Tier 5-6)` table

- [ ] **Step 3: Verify**

```bash
grep -c 'requirements.*REQ.*max' docs/book/ARCKIT-BOOK.md
grep -c 'data-mesh-contract' docs/book/ARCKIT-BOOK.md
grep -c 'MADR\|ADR.*max' docs/book/ARCKIT-BOOK.md
```

All should return `≥1`.

- [ ] **Step 4: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Requirements and Design content into new Chapter 5"
```

---

## Task 5: Move Strategy and Wardley content into Chapter 6

**Purpose:** Populate Ch 6 with the Wardley command table, the four remaining Strategic Planning commands, the Wardley Mapping Suite section and Mathematical Models section from Chapter 3, and the "Deep Dive: The Wardley Map Command" section from old Chapter 4.

**Source content (pre-skeleton line numbers):**
- Ch 3 lines **426–438**: Wardley Mapping Suite
- Ch 3 lines **440–484**: Wardley Mathematical Models
- Old Ch 4 Wardley Mapping table (`wardley`, `wardley.value-chain`, `wardley.doctrine`, `wardley.gameplay`, `wardley.climate`)
- Old Ch 4 Strategic Planning table — rows `roadmap`, `strategy`, `platform-design`, `framework`
- Old Ch 4 lines **685–705**: Deep Dive: The Wardley Map Command

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert command tables under Ch 6 heading**

Under `## Chapter 6: Strategy and Wardley Mapping`, replace the intro stub with:

```markdown
_Intro pending (Task 16)._

### Strategic Planning Commands

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `roadmap` | ROAD | max | Technology roadmap |
| `strategy` | STRT | max | Strategic analysis |
| `platform-design` | PLAT | max | Platform architecture design |
| `framework` | FRMK | max | Transform artifacts into structured framework (agent) |

### The Wardley Mapping Suite

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `wardley.value-chain` | WVCH | max | Value chain decomposition |
| `wardley` | WARD | max | Wardley Map (OWM + Mermaid dual output) |
| `wardley.doctrine` | WDOC | max | Doctrine maturity (40+ principles, 4 phases) |
| `wardley.climate` | WCLM | max | Climatic patterns (32 patterns, 6 categories) |
| `wardley.gameplay` | WGAM | max | Gameplay analysis (60+ patterns, D&D alignment) |
```

- [ ] **Step 2: Move three sections verbatim from Chapter 3 and old Chapter 4**

Append (in order) under the tables above, preserving each section's `###` heading verbatim:

1. The `### The Wardley Mapping Suite` section from old Chapter 3 (pre-skeleton lines 426–438)
2. The `### Wardley Mapping Mathematical Models` section from old Chapter 3 (pre-skeleton lines 440–484)
3. The `### Deep Dive: The Wardley Map Command` section from old Chapter 4 (pre-skeleton lines 685–705)

Use the Read tool to capture each section verbatim, then Edit tool (or Write tool for the first insertion, Edit for subsequent) to drop them under Ch 6.

- [ ] **Step 3: Replace Chapter 3's moved content with a teaser**

In old Chapter 3, replace the two Wardley sections (Suite + Mathematical Models) with a 3-line teaser:

```markdown
### The Wardley Mapping Suite

ArcKit includes a five-command Wardley mapping pipeline covering value chain, doctrine, climate, gameplay, and the main map itself. The suite is validated against 147 real-world Wardley maps with a 98% pass rate. See **Chapter 6: Strategy and Wardley Mapping** for the full command reference, mathematical models, and deep dive.
```

- [ ] **Step 4: Delete moved rows from old Chapter 4**

- Delete the `#### Wardley Mapping (Tier 6)` subsection entirely
- Delete the `roadmap`, `strategy`, `platform-design`, `framework` rows from the `#### Strategic Planning (Tier 5-6)` table
- Delete the `### Deep Dive: The Wardley Map Command` section entirely

- [ ] **Step 5: Verify**

```bash
grep -c 'wardley.doctrine' docs/book/ARCKIT-BOOK.md   # ≥ 1
grep -c '40+ principles' docs/book/ARCKIT-BOOK.md     # ≥ 1
grep -c '147 real-world' docs/book/ARCKIT-BOOK.md     # ≥ 2 (teaser + full)
grep -c 'Deep Dive: The Wardley' docs/book/ARCKIT-BOOK.md  # = 1 (only in Ch 6)
```

- [ ] **Step 6: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Strategy and Wardley content into new Chapter 6"
```

---

## Task 6: Move Market Research content into Chapter 7

**Purpose:** Populate Ch 7 with the Research and Discovery command table and the Government Code Discovery section from Chapter 3.

**Source content:**
- Old Ch 4 Research and Discovery table (9 commands including all `*-research`, `datascout`, `gov-*`, `grants`)
- Ch 3 pre-skeleton lines **486–494**: Government Code Discovery

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert command table under Ch 7 heading**

Under `## Chapter 7: Market Research and Knowledge Compounding`, replace the intro stub with:

```markdown
_Intro pending (Task 16)._

### Research and Discovery Commands (Tier 6)

| Command | Doc Type | Effort | Agent? | Description |
|---------|----------|--------|--------|-------------|
| `research` | RSCH | max | Yes | Market research, vendor eval, build vs buy, TCO |
| `datascout` | DSCR | max | Yes | Data source discovery, API catalogues, scoring |
| `aws-research` | RSCH | max | Yes | AWS service research via AWS Knowledge MCP |
| `azure-research` | RSCH | max | Yes | Azure research via Microsoft Learn MCP |
| `gcp-research` | RSCH | max | Yes | GCP research via Google Developer Knowledge MCP |
| `gov-reuse` | GOVR | max | Yes | Government code reuse assessment |
| `gov-code-search` | GCSR | max | Yes | Government code semantic search |
| `gov-landscape` | GLND | max | Yes | Government code landscape analysis |
| `grants` | GRNT | max | Yes | UK grants, funding, and accelerator research |
```

- [ ] **Step 2: Append Government Code Discovery section from Chapter 3**

Read pre-skeleton lines 486–494 of Chapter 3 (the `### Government Code Discovery` section) and append it verbatim under the command table in Ch 7.

- [ ] **Step 3: Replace Chapter 3's moved content with a teaser**

Replace the moved `### Government Code Discovery` section in Chapter 3 with:

```markdown
### Government Code Discovery

For UK Government projects, three commands search 24,500+ repositories via the `govreposcrape` MCP server: `gov-code-search`, `gov-reuse`, and `gov-landscape`. See **Chapter 7: Market Research and Knowledge Compounding** for details on how these integrate with the main research command's build-vs-buy analysis.
```

- [ ] **Step 4: Delete moved rows from old Chapter 4**

Delete the entire `#### Research and Discovery (Tier 6)` subsection.

- [ ] **Step 5: Verify**

```bash
grep -c 'govreposcrape' docs/book/ARCKIT-BOOK.md  # ≥ 2
grep -c 'gov-landscape' docs/book/ARCKIT-BOOK.md  # ≥ 1 (in Ch 7 table)
grep -c 'arckit-aws-research' docs/book/ARCKIT-BOOK.md  # unchanged
```

- [ ] **Step 6: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Market Research content into new Chapter 7"
```

---

## Task 7: Move Procurement content into Chapter 8

**Purpose:** Populate Ch 8 with the Procurement command table and the "Deep Dive: The Score Command" section.

**Source content:**
- Old Ch 4 Procurement table (`sow`, `dos`, `gcloud-search`, `gcloud-clarify`, `evaluate`, `score`)
- Old Ch 4 Deep Dive: The Score Command (pre-skeleton lines 725–734)

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert content block under Ch 8 heading**

```markdown
_Intro pending (Task 16)._

### Procurement Commands (Tier 7)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `sow` | SOW | max | Statement of Work for vendor RFP |
| `dos` | DOS | max | Digital Outcomes and Specialists procurement |
| `gcloud-search` | -- | high | G-Cloud catalogue search |
| `gcloud-clarify` | -- | high | G-Cloud clarification questions |
| `evaluate` | EVAL | max | Technology evaluation matrix |
| `score` | -- | high | Vendor scoring with JSON storage and audit trail |
```

- [ ] **Step 2: Move Deep Dive: The Score Command**

Append the `### Deep Dive: The Score Command` section (verbatim) under the table above.

- [ ] **Step 3: Delete from old Chapter 4**

Delete the `#### Procurement (Tier 7)` subsection and the `### Deep Dive: The Score Command` section.

- [ ] **Step 4: Verify + Lint + Commit**

```bash
grep -c 'score-validator' docs/book/ARCKIT-BOOK.md  # = 1 (moved, not duplicated)
grep -c 'docs/scores.json' docs/book/ARCKIT-BOOK.md  # = 1
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Procurement content into new Chapter 8"
```

---

## Task 8: Move Design Reviews content into Chapter 9

**Purpose:** Populate Ch 9 with the Design Reviews table. This is the smallest move — 2 commands, no deep dives.

**Source content:**
- Old Ch 4 Design Reviews table (`hld-review`, `dld-review`)

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert command table**

```markdown
_Intro pending (Task 16)._

### Design Review Commands (Tier 8)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `hld-review` | HLDR | max | High-level design review |
| `dld-review` | DLDR | max | Detailed-level design review |
```

- [ ] **Step 2: Delete from old Chapter 4**

Delete the `#### Design Reviews (Tier 8)` subsection.

- [ ] **Step 3: Verify + Lint + Commit**

```bash
grep -c 'dld-review.*DLDR' docs/book/ARCKIT-BOOK.md  # = 1
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Design Reviews content into new Chapter 9"
```

---

## Task 9: Move Delivery and Operations content into Chapter 10

**Purpose:** Populate Ch 10 with Implementation, Operations, Reporting tables plus the Pages Dashboard / Dependency Map visualization sections from old Chapter 12.

**Source content:**
- Old Ch 4 Implementation table (`backlog`, `story`, `trello`)
- Old Ch 4 Operations table (`servicenow`, `devops`, `mlops`, `finops`, `operationalize`)
- Old Ch 4 Reporting and Publishing table (`presentation`, `pages`, `template-builder`, `customize`)
- Old Ch 12 lines **2032–2061**: Pages Dashboard + Dependency Map Visualization (pre-skeleton — add skeleton offset)

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert command tables**

```markdown
_Intro pending (Task 16)._

### Implementation Commands (Tier 9-10)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `backlog` | BKLG | max | Product backlog generation from requirements |
| `story` | STORY | high | User stories from requirements |
| `trello` | -- | medium | Trello board JSON export |

### Operations Commands (Tier 11-12)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `servicenow` | SNOW | max | ServiceNow service management design |
| `devops` | DVOP | max | DevOps maturity assessment |
| `mlops` | MLOP | max | MLOps assessment (AI projects) |
| `finops` | FNOP | max | FinOps assessment |
| `operationalize` | OPER | max | Operational readiness assessment |

### Reporting and Publishing Commands (Tier 14-15)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `presentation` | PRES | max | Presentation slide deck (MARP) |
| `pages` | -- | medium | Interactive HTML dashboard |
| `template-builder` | -- | -- | Interactive template creation |
| `customize` | -- | -- | Template customization helper |
```

- [ ] **Step 2: Move Pages Dashboard and Dependency Map Visualization sections from old Chapter 12**

Append the `### The Pages Dashboard` section and the `### The Dependency Map Visualization` section (verbatim) from old Chapter 12 to the bottom of Ch 10.

- [ ] **Step 3: Delete moved content**

- Delete `#### Implementation (Tier 9-10)`, `#### Operations (Tier 11-12)`, and `#### Reporting and Publishing (Tier 14-15)` subsections from old Chapter 4.
- Delete `### The Pages Dashboard` and `### The Dependency Map Visualization` sections from old Chapter 12.

- [ ] **Step 4: Verify + Lint + Commit**

```bash
grep -c 'Pages Dashboard' docs/book/ARCKIT-BOOK.md     # = 1
grep -c 'operationalize' docs/book/ARCKIT-BOOK.md      # ≥ 1
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Delivery and Operations content into new Chapter 10"
```

---

## Task 10: Move Compliance and QA content into Chapter 11

**Purpose:** Populate Ch 11 with the Compliance table, Quality and Analysis table, and "Deep Dive: The Health Command" section.

**Source content:**
- Old Ch 4 Compliance table (10 commands)
- Old Ch 4 Quality and Analysis table (5 commands)
- Old Ch 4 Deep Dive: The Health Command (pre-skeleton lines 707–723)

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert content block**

```markdown
_Intro pending (Task 16)._

### Compliance Commands (Tier 13)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `tcop` | TCOP | max | Technology Code of Practice review |
| `secure` | SECR | max | UK Government Secure by Design (NCSC CAF) |
| `mod-secure` | MSBD | max | MOD Secure by Design (JSP 453) |
| `jsp-936` | J936 | max | MOD JSP 936 AI safety assurance |
| `ai-playbook` | AIPB | max | UK Government AI Playbook |
| `atrs` | ATRS | max | Algorithmic Transparency Recording Standard |
| `conformance` | CONF | max | Architecture conformance assessment |
| `maturity-model` | MATM | max | Capability maturity assessment |
| `service-assessment` | SVCA | max | GDS Service Standard assessment |
| `principles-compliance` | PRCM | high | Principles compliance check |

### Quality and Analysis Commands

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `analyze` | ANAL | high | Governance quality analysis |
| `traceability` | TRAC | max | Requirements traceability matrix |
| `impact` | -- | high | Blast radius / reverse dependency analysis |
| `search` | -- | medium | Cross-project artifact search |
| `health` | -- | medium | Stale artifact detection |
```

- [ ] **Step 2: Append Deep Dive: The Health Command**

Append the `### Deep Dive: The Health Command` section verbatim after the Quality and Analysis table.

- [ ] **Step 3: Delete moved content from old Chapter 4**

Delete the `#### Compliance (Tier 13)` subsection, the `#### Quality and Analysis` subsection, and the `### Deep Dive: The Health Command` section.

- [ ] **Step 4: Verify + Lint + Commit**

```bash
grep -c 'STALE-RSCH' docs/book/ARCKIT-BOOK.md              # = 1
grep -c 'UNRESOLVED-COND' docs/book/ARCKIT-BOOK.md         # = 1
grep -c 'jsp-936.*J936' docs/book/ARCKIT-BOOK.md           # = 1
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: move Compliance and QA content into new Chapter 11"
```

---

## Task 11: Delete the emptied old Chapter 4 wrapper and fold its intro into Chapter 12

**Purpose:** Old Chapter 4 should now be empty except for its heading, the Command Anatomy subsection, and the Conversational Gathering Pattern subsection. Move these into Chapter 12 (Prompt Engineering) and delete the Chapter 4 shell.

**Source content:**
- Old Ch 4 lines ~500–542 (pre-skeleton): `### Command Anatomy` — move to Ch 12 (which is currently called `## Chapter 5: The Prompt Engineering Anatomy`; still numbered 5 until Task 12)
- Old Ch 4 lines ~679–683 (pre-skeleton): `### The Conversational Gathering Pattern` — move to Ch 12

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Verify old Chapter 4 contains only these two subsections plus the chapter heading**

```bash
awk '/^## Chapter 4: Commands Deep Dive/,/^## Chapter 5/' docs/book/ARCKIT-BOOK.md | grep '^###'
```

Expected output: exactly two `###` headings — `Command Anatomy` and `The Conversational Gathering Pattern`. If any other `###` heading appears, re-run the relevant Task 3–10 to move the remaining content before continuing.

- [ ] **Step 2: Move `### Command Anatomy` to the top of current Chapter 5 (Prompt Engineering)**

Current Ch 5 opens at `## Chapter 5: The Prompt Engineering Anatomy` followed by `### Full Anatomy of a Command Prompt`. Insert the `### Command Anatomy` section verbatim between them.

- [ ] **Step 3: Move `### The Conversational Gathering Pattern` to Chapter 5**

Append it as a new `###` section after `### The Handoffs Schema` at the end of Chapter 5.

- [ ] **Step 4: Delete the entire old Chapter 4 heading and remaining shell**

Delete from `## Chapter 4: Commands Deep Dive` through (but not including) the next `## Chapter` heading.

- [ ] **Step 5: Verify old Chapter 4 is gone**

```bash
grep -c '^## Chapter 4: Commands Deep Dive' docs/book/ARCKIT-BOOK.md  # = 0
grep -c '^## Chapter 4: Foundation' docs/book/ARCKIT-BOOK.md          # = 1
grep -c 'Command Anatomy' docs/book/ARCKIT-BOOK.md                    # ≥ 1 (now in Ch 5)
grep -c 'Conversational Gathering' docs/book/ARCKIT-BOOK.md           # ≥ 1 (now in Ch 5)
```

- [ ] **Step 6: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: dissolve old Chapter 4, fold Command Anatomy into Prompt Engineering"
```

---

## Task 12: Renumber Part III chapters

**Purpose:** The current Chapter 5 (Prompt Engineering) through Chapter 11 (Autoresearch) must be renumbered to 12–18. Also swap the order of Templates and Multi-AI so Templates comes first.

**Rename map (old → new, in file order):**

| Old heading | New heading |
|---|---|
| `## Chapter 5: The Prompt Engineering Anatomy` | `## Chapter 12: The Prompt Engineering Anatomy` |
| `## Chapter 6: The Agent System` | `## Chapter 13: The Agent System` |
| `## Chapter 7: The Hook System` | `## Chapter 14: The Hook System` |
| `## Chapter 8: Skills, MCP Servers and References` | `## Chapter 15: Skills, MCP Servers and References` |
| `## Chapter 9: Multi-AI Distribution Architecture` | `## Chapter 17: Multi-AI Distribution Architecture` |
| `## Chapter 10: The Template and Document System` | `## Chapter 16: The Template and Document System` |
| `## Chapter 11: The Autoresearch System` | `## Chapter 18: The Autoresearch System` |

**Note:** Old Chapter 10 (Templates) becomes new Chapter 16, and old Chapter 9 (Multi-AI) becomes new Chapter 17 — a deliberate swap so Templates comes before Multi-AI (the converter chapter references templates).

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Rename each chapter heading**

Use seven Edit tool calls, one per rename pair. Use exact heading text to avoid ambiguity.

- [ ] **Step 2: Physically move Chapter 10 (Templates) to appear before Chapter 9 (Multi-AI)**

Because the file order after Step 1 has Ch 17 (Multi-AI) before Ch 16 (Templates), use the Read tool to capture the full Ch 16 (Templates) section — from `## Chapter 16: The Template and Document System` through (but not including) the next `## Chapter` heading — then Edit to delete it from its old position and re-insert immediately before `## Chapter 17: Multi-AI Distribution Architecture`.

- [ ] **Step 3: Verify ordering**

```bash
grep -n '^## Chapter 1[2-8]' docs/book/ARCKIT-BOOK.md
```

Expected (numerically ascending): 12, 13, 14, 15, 16, 17, 18.

- [ ] **Step 4: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: renumber Part III chapters 12-18 and swap Templates/Multi-AI"
```

---

## Task 13: Renumber Part IV chapters

**Purpose:** Old Chapter 12 (Development and Operations) becomes 19; old Chapter 13 (Highlights from the Commit History) becomes 20.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Rename two headings**

- `## Chapter 12: Development and Operations` → `## Chapter 19: Development and Operations`
- `## Chapter 13: Highlights from the Commit History` → `## Chapter 20: Highlights from the Commit History`

- [ ] **Step 2: Verify**

```bash
grep -n '^## Chapter ' docs/book/ARCKIT-BOOK.md
```

Expected numbering: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20. Exactly 20 chapter headings and no gaps.

- [ ] **Step 3: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: renumber Part IV chapters 19-20"
```

---

## Task 14: Insert Part separators

**Purpose:** Add four level-1 sub-headings (`# Part I`, etc.) to group chapters.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Insert "Part I — Introduction" before Chapter 1**

Before `## Chapter 1: What is ArcKit?`, insert:

```markdown
# Part I — Introduction

```

- [ ] **Step 2: Insert "Part II — The Governance Lifecycle" before Chapter 4**

Before `## Chapter 4: Foundation — …`, insert:

```markdown
# Part II — The Governance Lifecycle

```

- [ ] **Step 3: Insert "Part III — How ArcKit Works" before Chapter 12**

Before `## Chapter 12: The Prompt Engineering Anatomy`, insert:

```markdown
# Part III — How ArcKit Works

```

- [ ] **Step 4: Insert "Part IV — Operations and History" before Chapter 19**

Before `## Chapter 19: Development and Operations`, insert:

```markdown
# Part IV — Operations and History

```

- [ ] **Step 5: Verify**

```bash
grep -c '^# Part' docs/book/ARCKIT-BOOK.md  # = 4
```

- [ ] **Step 6: Lint + Commit**

Markdown lint will complain about multiple H1s. Add a line-specific suppression at the top of the file if not already present, or accept the violation if the existing doc already has multiple H1s for the main title. Check baseline first:

```bash
grep -c '^# ' docs/book/ARCKIT-BOOK.md  # pre-insert was 1 (just the title); now 5
```

If the lint rule is `MD025`, add one `<!-- markdownlint-disable MD025 -->` at the top of the file (immediately after the title `# The ArcKit Book`).

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: add Part I-IV separators"
```

---

## Task 15: Regenerate the Table of Contents

**Purpose:** The TOC at lines 11–31 (original) still lists old 13-chapter structure. Replace it with the new 20-chapter + 4-part structure.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Locate current TOC block**

```bash
grep -n '^## Table of Contents' docs/book/ARCKIT-BOOK.md
```

- [ ] **Step 2: Replace TOC block with the new structure**

Replace the existing `## Table of Contents` and all its bullets (up to and including the next `---` separator) with:

```markdown
## Table of Contents

**Part I — Introduction**
- [Chapter 1: What is ArcKit?](#chapter-1-what-is-arckit)
- [Chapter 2: Getting Started](#chapter-2-getting-started)
- [Chapter 3: The ArcKit Workflow](#chapter-3-the-arckit-workflow)

**Part II — The Governance Lifecycle**
- [Chapter 4: Foundation — Principles, Stakeholders, Risk, Business Case](#chapter-4-foundation--principles-stakeholders-risk-business-case)
- [Chapter 5: Requirements and Design](#chapter-5-requirements-and-design)
- [Chapter 6: Strategy and Wardley Mapping](#chapter-6-strategy-and-wardley-mapping)
- [Chapter 7: Market Research and Knowledge Compounding](#chapter-7-market-research-and-knowledge-compounding)
- [Chapter 8: Procurement and Vendor Evaluation](#chapter-8-procurement-and-vendor-evaluation)
- [Chapter 9: Design Reviews (HLD / DLD)](#chapter-9-design-reviews-hld--dld)
- [Chapter 10: Delivery and Operations](#chapter-10-delivery-and-operations)
- [Chapter 11: Compliance and Quality Assurance](#chapter-11-compliance-and-quality-assurance)

**Part III — How ArcKit Works**
- [Chapter 12: The Prompt Engineering Anatomy](#chapter-12-the-prompt-engineering-anatomy)
- [Chapter 13: The Agent System](#chapter-13-the-agent-system)
- [Chapter 14: The Hook System](#chapter-14-the-hook-system)
- [Chapter 15: Skills, MCP Servers and References](#chapter-15-skills-mcp-servers-and-references)
- [Chapter 16: The Template and Document System](#chapter-16-the-template-and-document-system)
- [Chapter 17: Multi-AI Distribution Architecture](#chapter-17-multi-ai-distribution-architecture)
- [Chapter 18: The Autoresearch System](#chapter-18-the-autoresearch-system)

**Part IV — Operations and History**
- [Chapter 19: Development and Operations](#chapter-19-development-and-operations)
- [Chapter 20: Highlights from the Commit History](#chapter-20-highlights-from-the-commit-history)

**Appendices**
- [Appendix A: Complete Command Reference](#appendix-a-complete-command-reference)
- [Appendix B: Document Type Code Registry](#appendix-b-document-type-code-registry)
- [Appendix C: Hook Reference](#appendix-c-hook-reference)
- [Appendix D: Adding a New Command](#appendix-d-adding-a-new-command)
- [Appendix E: Glossary of Terms](#appendix-e-glossary-of-terms)
- [Appendix F: Frequently Asked Questions](#appendix-f-frequently-asked-questions)
```

- [ ] **Step 3: Verify every TOC anchor resolves**

```bash
# Extract TOC anchors and confirm each has a matching heading
awk '/^## Table of Contents/,/^---/' docs/book/ARCKIT-BOOK.md \
  | grep -o '#chapter-[^)]*\|#appendix-[^)]*' \
  | while read anchor; do
      slug=$(echo "$anchor" | sed 's/^#//')
      # GitHub's slugifier lowercases, strips punctuation, and joins words with -
      # Approximate check: try to find the heading
      if ! grep -qi "^## ${slug//-/.}" docs/book/ARCKIT-BOOK.md; then
        echo "MAYBE BROKEN: $anchor"
      fi
    done
```

Manually review any "MAYBE BROKEN" output — GitHub's slugifier has edge cases (em-dashes, parentheses, slashes). Fix the TOC anchor or heading to match.

- [ ] **Step 4: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: regenerate TOC for 4-part, 20-chapter structure"
```

---

## Task 16: Write new intros and outros for the 8 Part II chapters

**Purpose:** Replace the 8 `_Intro pending._` stubs with real chapter intros. Each chapter gets ~300 words of new prose: an intro framing the phase and what commands do in it, plus a 1–2 sentence outro handing off to the next chapter.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

**Writing guidance (applies to every chapter):**
- Use the existing book's voice (see Chapter 1 for tone — direct, no hype, concrete).
- Mention the commands covered in the chapter by name.
- Reference the governance framework(s) each chapter draws on.
- End with a 1-sentence pointer to the next chapter.
- Do NOT introduce new facts not already established in the book or the `.devin/wiki.json` repo_notes.
- Do NOT use emoji.

### Task 16a: Chapter 4 intro (Foundation)

Replace `_Intro pending._` under Chapter 4 with:

```markdown
Every ArcKit project starts with four artifacts: architecture principles, stakeholder analysis, risk register, and Strategic Outline Business Case. These are not interchangeable — they come in this order because each depends on the one before it. Stakeholders cannot be identified without principles that define which decisions matter. Risks cannot be scored without a RACI that says who owns each one. Business cases cannot justify benefits without goals traced back to named stakeholders.

ArcKit enforces this order by making each command read the previous command's output. The `principles` command writes `ARC-000-PRIN-v1.0.md` to the global `projects/000-global/` directory — it is the only artifact shared across projects. The `stakeholders` command reads those principles and builds a Power-Interest Grid and RACI matrix. The `risk` command reads the RACI and links every risk to a named stakeholder using HM Treasury Orange Book 2023 risk management (six treatment options, Three Lines of Defence, cascade analysis — not the popular but incorrect "4Ts"). The `sobc` command reads risks and goals to produce a five-case Strategic Outline Business Case (Strategic, Economic, Commercial, Financial, Management) following HM Treasury Green Book 2026 vocabulary: BAU, Do Minimum, Preferred Way Forward.

None of this is negotiable — every downstream command in Parts II and III assumes these four artifacts exist. Skip them and later commands will either refuse to run or produce requirements with no traceable justification.

The next chapter (Requirements and Design) turns these foundational artifacts into the specifications that drive implementation.
```

Add at the end of Chapter 4 (after all tables), before the closing `---`:

```markdown
With foundation artifacts in place, the next chapter translates business goals and stakeholder needs into the technical specifications that drive implementation.
```

### Task 16b: Chapter 5 intro (Requirements and Design)

Replace `_Intro pending._` under Chapter 5 with:

```markdown
Requirements are the most-connected artifact in the ArcKit dependency matrix. They feed into 38 downstream consumers: research, data models, procurement, design, backlog, compliance, and most quality commands. Get requirements right and everything downstream follows; get them wrong and the whole project inherits the error.

ArcKit's requirements taxonomy uses five prefixes. `BR-xxx` for business requirements (drivers, success criteria). `FR-xxx` for functional requirements (system capabilities, user interactions). `NFR-xxx` for non-functional (with sub-prefixes like `NFR-P-xxx` for performance and `NFR-SEC-xxx` for security). `INT-xxx` for integration (upstream/downstream interfaces). `DR-xxx` for data requirements (entities, attributes, retention, quality). Every requirement ID is cited by at least one downstream artifact — data models cite the DR-xxx they implement, ADRs cite the FR-xxx they resolve, compliance assessments cite the NFR-SEC-xxx they verify.

This chapter covers the commands that produce those specifications: `requirements` for the hub document, `data-model` and `data-mesh-contract` for structured data design, `dpia` for GDPR impact assessments, `dfd` for data flow diagrams, `glossary` for contextual definitions, `diagram` for C4-model architecture diagrams, and `adr` for architecture decision records in MADR v4.0 format. Data quality uses the National Data Framework's six dimensions (accuracy, completeness, consistency, timeliness, uniqueness, validity) with measurable targets.

Chapter 6 turns requirements into strategic posture through Wardley mapping and roadmaps.
```

Outro:

```markdown
Requirements tell you what to build. Chapter 6 adds a strategic lens that decides how to build it — buy, build, or outsource, and when each component commoditises.
```

### Task 16c: Chapter 6 intro (Strategy and Wardley Mapping)

Replace `_Intro pending._` under Chapter 6 with:

```markdown
Between requirements and implementation sits a strategic question every architect must answer: for each component the project depends on, should we build, buy, or reuse? Most enterprise architecture toolkits answer this with opinion. ArcKit answers it with Wardley maps.

Wardley mapping places components on a 2D grid — visibility to the user on the Y-axis, evolution stage on the X-axis (Genesis, Custom, Product, Commodity). The answer follows from position: components in Genesis are built because they do not yet exist; components in Product are bought because mature vendors offer them; components in Commodity are rented from cloud platforms because building them is waste. ArcKit ships a five-command Wardley suite — `wardley.value-chain` decomposes user needs into components, `wardley` produces the map (with dual output: OWM syntax for create.wardleymaps.ai and Mermaid `wardley-beta` blocks for GitHub rendering), `wardley.doctrine` assesses organisational maturity against 40+ principles in 4 phases, `wardley.climate` evaluates 32 climatic patterns, and `wardley.gameplay` analyses 60+ strategic patterns. The Mermaid syntax has been validated against 147 real-world Simon Wardley maps with a 98% pass rate.

This chapter also covers the broader strategic planning commands: `roadmap` for multi-year transformation timelines, `strategy` for executive synthesis, `platform-design` for multi-sided ecosystems using the Platform Design Toolkit, and `framework` which turns a collection of ArcKit artifacts into a structured capability framework.

Chapter 7 is the natural next step: once you know what to build versus buy, research finds the vendors, the open-source alternatives, and — for UK Government projects — the existing government code you can reuse.
```

Outro:

```markdown
Wardley maps tell you what to buy. Chapter 7 tells you from whom, and surfaces existing code you might reuse before you buy.
```

### Task 16d: Chapter 7 intro (Market Research and Knowledge Compounding)

Replace `_Intro pending._` under Chapter 7 with:

```markdown
Architecture research is a first-class governance artifact in ArcKit — not a side task. When Wardley mapping concludes a component should be bought, the research commands answer three questions: which vendors exist, how they compare on TCO and capability, and (for UK Government projects) whether existing government code can be reused instead.

Research commands are ArcKit's heaviest users of external data. They run as autonomous agents in Claude Code (inlined as direct prompts on other platforms) so the 10+ WebSearch and MCP calls they make happen in an isolated Task subprocess rather than polluting the main conversation's context. Each cloud provider gets a dedicated research agent backed by a dedicated MCP server: `aws-research` uses AWS Knowledge, `azure-research` uses Microsoft Learn, `gcp-research` uses Google Developer Knowledge. General vendor research uses `research`. Data source discovery uses `datascout` backed by Data Commons MCP. UK grants and accelerator research uses `grants`.

The UK Government code discovery commands are distinctive to ArcKit: `gov-code-search`, `gov-reuse`, and `gov-landscape` query 24,500+ UK public-sector repositories through the `govreposcrape` MCP server. They feed into the main `research` command as a fifth build-vs-buy option — "reuse existing government code" — which is often the right answer for projects in regulated domains where incumbent departments have already built and battle-tested components.

Research artifacts go stale at 6 months per the `health` command's STALE-RSCH rule; regenerating them is a scheduled activity, not a one-off. Chapter 8 turns a shortlist of vendors from research into a procurement package.
```

Outro:

```markdown
Research produces the shortlist. Chapter 8 turns that shortlist into a Statement of Work, a scored vendor comparison, and — for UK Government projects — a G-Cloud or Digital Outcomes procurement.
```

### Task 16e: Chapter 8 intro (Procurement and Vendor Evaluation)

Replace `_Intro pending._` under Chapter 8 with:

```markdown
ArcKit's procurement commands are biased toward UK public-sector procurement routes because that is where governance rigour pays off most: if your procurement justification is weak, a G-Cloud challenge or an FOI request will expose it. The same rigour helps private-sector projects even without the legal backstop.

Two procurement routes are supported natively. G-Cloud (for commodity cloud services) is covered by `gcloud-search` for catalogue search and `gcloud-clarify` for the clarification questions that weed out incompatible vendors. Digital Outcomes and Specialists (for custom services) is covered by `dos`. Both feed into `sow` which produces a Statement of Work vendor RFP, and `evaluate` which produces a weighted technology evaluation matrix.

The evaluation matrix feeds into the only ArcKit command that writes structured JSON rather than Markdown: `score`. The scoring command writes to `docs/scores.json` instead of a document, appends to existing data rather than overwriting, supports sensitivity analysis (how much does the ranking change if weights shift?), and maintains an audit trail recording who scored what, when, and with what weights. A PreToolUse hook (`score-validator.mjs`) validates JSON structure before every write to prevent corruption.

Chapter 9 covers the governance gate that sits between a chosen vendor and implementation: design review.
```

Outro:

```markdown
With a vendor selected and scored, the design review gate in Chapter 9 catches design-level problems before implementation spends any of the budget.
```

### Task 16f: Chapter 9 intro (Design Reviews)

Replace `_Intro pending._` under Chapter 9 with:

```markdown
Design reviews are the cheapest place to catch architecture problems. A mistake found at HLD review costs a meeting; the same mistake found after implementation costs a sprint. ArcKit's two design-review commands formalise this gate so it happens consistently.

High-Level Design review (`hld-review`) validates system architecture and strategic alignment — does the proposed design meet the principles, cover the requirements, align with the Wardley map, and respect security and compliance frameworks appropriate to the project? Detailed-Level Design review (`dld-review`) validates the technical specification and operational readiness — monitoring, disaster recovery, runbook quality, scalability under load.

Both reviews evaluate across five dimensions: requirements coverage, principles compliance, security/compliance (using NCSC frameworks for UK Government projects), technical quality, and operational readiness. Reviews produce four verdicts: APPROVED (proceed), APPROVED WITH CONDITIONS (proceed but track conditions), REJECTED (revise), and NEEDS RE-REVIEW (architectural change requires HLD update). The UNRESOLVED-COND rule in the `health` command flags "APPROVED WITH CONDITIONS" reviews that have no resolution evidence after a threshold period — conditional approvals cannot quietly become permanent technical debt.

Chapter 10 covers the commands that follow a successful design review: backlog, user stories, service management design, and the maturity assessments that measure operational readiness.
```

Outro:

```markdown
With design reviews passed, the delivery and operations chapter turns approved designs into implementation artifacts and operational assessments.
```

### Task 16g: Chapter 10 intro (Delivery and Operations)

Replace `_Intro pending._` under Chapter 10 with:

```markdown
Delivery and operations is where governance artifacts become running services. The chapter covers three distinct phases: converting approved designs into implementation backlog, producing the service-management configurations that surround a live service, and assessing operational readiness across DevOps, MLOps, and FinOps maturity.

Implementation commands turn designs into work. `backlog` generates a prioritised product backlog from requirements and design artifacts. `story` generates Agile user stories traceable back to their parent requirement IDs. `trello` exports the backlog to a Trello board JSON for teams that use Trello as their primary tracker.

Operations commands configure the service-management tooling a running service needs. `servicenow` produces a ServiceNow CMDB design including CIs, service maps, and CRQ templates. `devops` produces a DevOps maturity assessment against an industry-standard framework. `mlops` does the same for AI projects against MLOps-specific maturity scales. `finops` covers cloud cost governance. `operationalize` produces a comprehensive operational-readiness assessment integrating all of the above.

Reporting and publishing commands turn governance artifacts into shareable formats. `presentation` produces a MARP slide deck for steering committees. `pages` — documented in detail at the end of this chapter — produces an interactive HTML dashboard at `docs/index.html` that visualises the full artifact hierarchy, dependency map, health status, and traceability coverage. `template-builder` and `customize` help teams adapt ArcKit templates to their organisation.

Chapter 11 closes Part II with the compliance and quality assurance commands that verify everything produced so far.
```

Outro:

```markdown
Chapter 11 completes the governance lifecycle with the compliance and quality commands that verify everything Parts II-III produced.
```

### Task 16h: Chapter 11 intro (Compliance and Quality Assurance)

Replace `_Intro pending._` under Chapter 11 with:

```markdown
Compliance is the governance gate where a project proves to external assessors that the architecture meets regulatory, security, and service-standard requirements. ArcKit's compliance commands are organised around UK public-sector frameworks because that is where the assessment burden is heaviest and most formal, but they apply anywhere compliance evidence must be traced to source artifacts.

The UK Government compliance set covers: `tcop` (Technology Code of Practice 14 points), `secure` (UK Secure by Design against NCSC CAF), `mod-secure` (MOD Secure by Design against JSP 453), `jsp-936` (MOD JSP 936 AI safety assurance with tiered approval paths by risk classification), `ai-playbook` (UK Government AI Playbook 10 principles), `atrs` (Algorithmic Transparency Recording Standard publication), `service-assessment` (GDS Service Standard assessment for Alpha/Beta/Live gates), `conformance` (architecture conformance against principles and patterns), `maturity-model` (capability maturity assessment), and `principles-compliance` (quick check of a single artifact against the ARC-000-PRIN principles).

Alongside compliance sit the quality and analysis commands that maintain artifact health over time. `traceability` generates end-to-end requirement-to-artifact mapping — the matrix that compliance assessors will eventually ask to see. `analyze` scores governance quality across coverage, consistency, and currency dimensions. `impact` performs reverse-dependency (blast-radius) analysis when a change is proposed — touching FR-042 triggers warnings for every downstream artifact that cites it. `search` indexes every artifact across every project for cross-project lookups. `health` scans for seven stale or orphan patterns (STALE-RSCH, FORGOTTEN-ADR, UNRESOLVED-COND, ORPHAN-REQ, MISSING-TRACE, VERSION-DRIFT, STALE-EXT) and writes `docs/health.json` for the `pages` dashboard.

With Part II complete, the book turns inward. Part III documents how ArcKit itself is built — the prompt engineering patterns, agents, hooks, skills, templates, and conversion pipeline that make the commands in Parts I-II possible.
```

Outro:

```markdown
Part III opens the hood. Readers using ArcKit as a black box can stop here; readers extending or modifying ArcKit should continue.
```

- [ ] **Step 1: Replace each `_Intro pending._` stub in turn (Tasks 16a–16h)**

Use 8 Edit tool calls, one per chapter. Match the stub exactly: `_Intro pending (Task 16)._`.

- [ ] **Step 2: Verify no stubs remain**

```bash
grep -c '_Intro pending' docs/book/ARCKIT-BOOK.md  # = 0
```

- [ ] **Step 3: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: write Part II chapter intros and outros"
```

---

## Task 17: Update cross-references throughout the book

**Purpose:** Every mention of "Chapter N" or "see Chapter N" in the body of the book refers to the old chapter numbers. Update them to the new numbers.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Find all cross-references**

```bash
grep -nE 'Chapter [0-9]+|see Chapter|in Chapter|of Chapter' docs/book/ARCKIT-BOOK.md
```

- [ ] **Step 2: Build the old→new mapping**

| Old ref | New ref |
|---|---|
| Chapter 4 (Commands Deep Dive) | Distributed across Chapters 4–11; update each reference to the *specific* new chapter it means |
| Chapter 5 (Prompt Engineering) | Chapter 12 |
| Chapter 6 (Agent System) | Chapter 13 |
| Chapter 7 (Hook System) | Chapter 14 |
| Chapter 8 (Skills/MCP) | Chapter 15 |
| Chapter 9 (Multi-AI) | Chapter 17 |
| Chapter 10 (Templates) | Chapter 16 |
| Chapter 11 (Autoresearch) | Chapter 18 |
| Chapter 12 (Dev/Ops) | Chapter 19 |
| Chapter 13 (Commit History) | Chapter 20 |

Chapters 1–3 keep their numbers.

- [ ] **Step 3: Apply the mapping**

Go through the grep output from Step 1 line by line. For each mention, determine from context which specific new chapter is meant, then use Edit to update. **This is not a pure find-and-replace** — a reference to "Chapter 4" could mean new Ch 4 (Foundation), new Ch 5 (Requirements), or new Ch 12 (Prompt Engineering). Read the surrounding paragraph to disambiguate.

- [ ] **Step 4: Verify**

```bash
# Every "Chapter N" should now resolve to a heading in the file
grep -oE 'Chapter [0-9]+' docs/book/ARCKIT-BOOK.md | sort -u | while read ref; do
  num=$(echo "$ref" | grep -oE '[0-9]+')
  if ! grep -q "^## Chapter $num:" docs/book/ARCKIT-BOOK.md; then
    echo "DANGLING: $ref"
  fi
done
```

Expected: no `DANGLING` output.

- [ ] **Step 5: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: update cross-references for new chapter numbering"
```

---

## Task 18: Re-sort Appendix A (Complete Command Reference)

**Purpose:** Appendix A currently groups commands by the old chapter order. Re-sort by the new Part II phase order so Appendix A mirrors the book's structure.

**Target grouping (in order):** Foundation → Requirements and Design → Strategy and Wardley → Market Research → Procurement → Design Reviews → Delivery and Operations → Compliance and Quality.

**Files:**
- Modify: `docs/book/ARCKIT-BOOK.md`

- [ ] **Step 1: Locate Appendix A**

```bash
grep -n '^## Appendix A' docs/book/ARCKIT-BOOK.md
```

- [ ] **Step 2: Read current Appendix A content**

Use the Read tool to capture all content from `## Appendix A` to the next `## Appendix` heading.

- [ ] **Step 3: Rewrite Appendix A with Part II phase groupings**

Replace the entire section with a new version that uses eight subsection headings matching the new Part II chapter titles. Within each subsection, list the commands that appear in that chapter's tables (from Tasks 3–10 above) in the same order.

Example structure:

```markdown
## Appendix A: Complete Command Reference

### Foundation (Chapter 4)

| Command | Doc Type | Effort | Description |
|---------|----------|--------|-------------|
| `plan` | PLAN | high | ... |
| `principles` | PRIN | max | ... |
| ...

### Requirements and Design (Chapter 5)

...
```

- [ ] **Step 4: Verify command count unchanged**

```bash
# Before restructure: count unique backtick command names in Appendix A
# After: same count (or higher if the original had omissions)
grep -oE '`[a-z][a-z0-9.-]+`' docs/book/ARCKIT-BOOK.md | sort -u | wc -l
```

Compare to the 68 documented commands — should be at least that many unique names.

- [ ] **Step 5: Lint + Commit**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
git add docs/book/ARCKIT-BOOK.md
git commit -m "book: re-sort Appendix A by Part II phase order"
```

---

## Task 19: Final verification

**Purpose:** Prove nothing was lost, everything renders, and cross-references resolve.

**Files:**
- None (read-only verification)

- [ ] **Step 1: Compare word count to baseline**

```bash
wc -w docs/book/ARCKIT-BOOK.md
cat /tmp/arckit-book-baseline.txt
```

Expected: new word count is between baseline (25,749) and baseline + 3,000. The addition should be ~2,400 words from new intros/outros plus a small amount from teasers. If the new count is *less* than baseline, content was lost — run:

```bash
git log --oneline feat/book-restructure ^main
```

and bisect to find the commit that dropped content.

- [ ] **Step 2: Verify all chapter and appendix headings exist**

```bash
grep -E '^## (Chapter [0-9]+|Appendix [A-F])' docs/book/ARCKIT-BOOK.md
```

Expected: Chapters 1–20 in order, Appendices A–F in order.

- [ ] **Step 3: Verify no content duplication from moves**

```bash
# These deep dive section titles should appear exactly once each
for title in 'Deep Dive: The Wardley Map Command' 'Deep Dive: The Health Command' 'Deep Dive: The Score Command' 'Command Anatomy' 'Conversational Gathering Pattern' 'The Wardley Mapping Suite' 'Government Code Discovery' 'The Pages Dashboard'; do
  count=$(grep -c "$title" docs/book/ARCKIT-BOOK.md)
  if [ "$count" -ne 1 ] && [ "$count" -ne 2 ]; then
    echo "UNEXPECTED COUNT for '$title': $count"
  fi
done
```

Note: "The Wardley Mapping Suite" and "Government Code Discovery" appear 2× (teaser in Ch 3 + full in Ch 6/7). All others appear exactly 1×.

- [ ] **Step 4: Verify all 18 Mermaid figures survived**

```bash
# Count must be exactly 18
count=$(grep -c '^```mermaid' docs/book/ARCKIT-BOOK.md)
echo "Mermaid blocks: $count"
[ "$count" = "18" ] || echo "MISMATCH: expected 18"

# Each figure caption must appear exactly once
for fig in '3-1' '4-1' '4-2' '4-3' '4-4' '4-5' '4-6' '4-7' '4-8' '4-9' '4-10' '4-11' '7-1' '7-2' '9-1' '10-1' '10-2' '12-1'; do
  c=$(grep -c "Figure $fig:" docs/book/ARCKIT-BOOK.md)
  [ "$c" = "1" ] || echo "Figure $fig: expected 1, found $c"
done
```

Note: figure numbers inherit the old-chapter prefix (e.g. Figure 4-1 still lives in what is now Part II, not literally Chapter 4). Renumbering figure captions to match new chapter numbers is optional polish — leave for a separate pass if desired.

- [ ] **Step 5: Verify lint passes**

```bash
npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md
```

Expected: zero new violations vs baseline. If the baseline had violations, they should still be there unchanged.

- [ ] **Step 6: Render preview**

If the project has a PDF/HTML renderer for the book, run it and visually inspect the new Part separators and TOC. If no renderer exists, open the Markdown in the editor's preview pane and skim the TOC + first page of each new chapter.

- [ ] **Step 7: If all verification passes, commit a no-op marker**

```bash
git commit --allow-empty -m "book: restructure verification passed"
```

---

## Task 20: Open pull request

**Purpose:** Push the branch and open a PR for review. Per CLAUDE.md rule: never push directly to main; always via PR.

**Files:**
- None (git-only)

- [ ] **Step 1: Push branch**

```bash
git push -u origin feat/book-restructure
```

- [ ] **Step 2: Open PR**

```bash
gh pr create --title "book: restructure into 4-part, 20-chapter layout" --body "$(cat <<'EOF'
## Summary
- Restructures `docs/book/ARCKIT-BOOK.md` into 4 parts and 20 chapters organised around the governance lifecycle.
- Dissolves the monolithic Chapter 4 (Commands Deep Dive) into 8 phase-based Part II chapters (Foundation → Compliance).
- Reorders Part III so Templates precedes Multi-AI Distribution (the converter chapter references templates).
- Adds Part I–IV separators, regenerates the TOC, re-sorts Appendix A by Part II phases.
- Adds ~2,400 words of new per-chapter intros/outros; all existing content preserved.

## Rationale
The book was organised around ArcKit's internals (Commands, Agents, Hooks, Skills, Templates). Readers using the book to run a project had to reverse-engineer the phase order from the dependency matrix. The new structure matches how practitioners pick up ArcKit — phase by phase — while keeping the internals as Part III reference.

## Test plan
- [ ] Word count within expected range (baseline + ~2,400)
- [ ] Chapters 1–20 all present and in order
- [ ] No duplicated Deep Dive sections (each appears exactly once)
- [ ] All cross-references (`Chapter N` mentions) resolve to an existing heading
- [ ] `npx markdownlint-cli2 docs/book/ARCKIT-BOOK.md` passes with no new violations
- [ ] Appendix A covers all 68 commands across the 8 phase subsections

## Implementation plan
See `docs/superpowers/plans/2026-04-20-book-restructure.md`.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

- [ ] **Step 3: Clean up scratch file**

```bash
rm -f /tmp/arckit-book-baseline.txt
```

---

## Estimated time

| Phase | Tasks | Time |
|---|---|---|
| 1 — Feature branch + skeletons + content moves | 1–11 | 4–5 hrs |
| 2 — Renumber Part III/IV | 12–13 | 30 min |
| 3 — Part separators + TOC | 14–15 | 30 min |
| 4 — Write intros/outros | 16 | 2–3 hrs |
| 5 — Cross-refs, Appendix A, verify, PR | 17–20 | 1–1.5 hrs |
| **Total** | | **8–10 hrs** |

A single session is feasible for a focused engineer; spreading across two sessions is equally reasonable given Task 16's writing load.

---

## Rollback

Every task ends in a commit. If verification fails after any task:

```bash
git log --oneline feat/book-restructure ^main
# Find the last-good commit hash
git reset --hard <last-good-hash>
# Re-run the failed task
```

If the whole restructure proves wrong, close the PR and delete the branch:

```bash
gh pr close <pr-number> --delete-branch
```

No main-branch cleanup is needed because the feature branch never merged.

---

## Self-review checklist (ran at plan-write time)

- **Spec coverage:** The earlier in-chat spec had 4 phases; this plan has 20 tasks across 5 phases covering every content move named in the spec. Part II skeletons (Task 2), 8 content moves (Tasks 3–10), Ch 4 dissolution (Task 11), renumbering (Tasks 12–13), structure (Tasks 14–15), new prose (Task 16), cross-refs (Task 17), Appendix A (Task 18), verify (Task 19), PR (Task 20). All covered.
- **Placeholder scan:** No "TBD", "implement later", or "fill in details". Task 16's 8 intros are written in full. Task 17's cross-ref mapping is explicit. Task 18 gives the target grouping order explicitly.
- **Type consistency:** Chapter numbers are consistent throughout (old 4 → split; old 5 → 12; etc.). Command names match existing repo conventions.
- **Line-number caveat:** The line-range map uses pre-skeleton numbers. Task 2's skeleton insert adds ~40 lines, so all subsequent line ranges shift. Each task tells the executor to `grep -n` before operating, which handles this automatically.
