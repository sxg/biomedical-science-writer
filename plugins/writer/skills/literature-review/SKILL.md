---
name: literature-review
description: Process user-provided PDFs using isolated subagents, generate structured notes, synthesize findings, and draft the Introduction section. Third step of writer workflow. Requires scope.md.
---

# Literature Review

Processes user-provided PDFs one at a time using isolated subagents to prevent context overflow, then synthesizes findings and drafts the Introduction section.

## CRITICAL CONSTRAINTS

```
┌────────────────────────────────────────────────────────────────────┐
│  ⛔ ORCHESTRATOR MUST NEVER READ PDF FILES DIRECTLY ⛔             │
│                                                                    │
│  The orchestrator (you, running this skill) may ONLY read:         │
│  - scope.md                                                        │
│  - inventory.md                                                    │
│  - notes/ethics-summary.md                                            │
│  - notes/papers/*.md (the condensed notes, NOT PDFs)               │
│  - notes/bibliography.md                                           │
│  - notes/literature-synthesis.md                                   │
│                                                                    │
│  If you find yourself about to use the Read tool on a .pdf file,   │
│  STOP. Spawn a subagent instead.                                   │
├────────────────────────────────────────────────────────────────────┤
│  ⛔ EACH SUBAGENT PROCESSES EXACTLY ONE PAPER ⛔                   │
│                                                                    │
│  - One Task tool call = One PDF                                    │
│  - Never pass multiple PDFs to a single subagent                   │
│  - Never ask a subagent to "process the remaining papers"          │
│  - Wait for each subagent to complete before spawning the next     │
│                                                                    │
│  WRONG: Task("Process papers/a.pdf, papers/b.pdf, papers/c.pdf")   │
│  RIGHT: Task("Process papers/a.pdf") → wait → Task("Process b.pdf")│
└────────────────────────────────────────────────────────────────────┘
```

## Architecture: Subagent Pattern

Each paper is processed by an isolated subagent to prevent context overflow:

```
┌─────────────────────────────────────────────────────────────────┐
│                    LITERATURE REVIEW WORKFLOW                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [ORCHESTRATOR]                                                 │
│       │                                                         │
│       ├──► Read scope.md (extract research context)             │
│       │                                                         │
│       ├──► List papers/*.pdf                                    │
│       │                                                         │
│       ├──► For each PDF, spawn isolated subagent:               │
│       │         │                                               │
│       │         ├──► [Subagent] Read ONE PDF                    │
│       │         ├──► [Subagent] Generate notes/papers/*.md      │
│       │         └──► [Subagent] Exit (context cleared)          │
│       │                                                         │
│       ├──► Read all notes/papers/*.md (small files)             │
│       │                                                         │
│       ├──► Generate bibliography + synthesis                    │
│       │                                                         │
│       └──► Draft Introduction                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Why subagents?**
- Each PDF can be 10-50 pages of dense content
- Processing 30 papers in one context overflows limits
- Subagents process one paper, write condensed notes, then exit
- Orchestrator only reads small markdown note files

## Prerequisites

- `scope.md` must exist (from scoping step)
- `notes/ethics-summary.md` may exist (provides study objectives and population context)
- `papers/` folder with PDF files to process
- Create directories: `notes/papers/`, `notes/papers-library/`, `drafts/`

## Workflow Overview

```
[Step 1: Read scope.md for context]
     │
     ▼
[Step 2: Inventory papers/ folder]
     │
     ▼
[Step 3: Process each PDF via subagent] ─── One at a time
     │                                       └── notes/papers/*.md
     ▼
[Step 4: Generate bibliography] ─── From all notes
     │                              └── notes/bibliography.md
     ▼
[Step 5: Synthesize literature] ─── Aggregate findings
     │                              └── notes/literature-synthesis.md
     ▼
[Step 6: Draft Introduction]
     │
     ▼
[Output] ─── drafts/introduction.md
```

---

## Step 1: Read Scope Context

From `scope.md`, extract comprehensive context to pass to each subagent:
- Research question (frames how to evaluate relevance)
- Key findings (what results need literature context)
- Target journal (determines citation depth)
- Hypothesis (what the manuscript argues)
- Population/methods (for identifying comparable studies)

Create a detailed scope summary to pass to each subagent:

```markdown
## Manuscript Context

You are helping write a scientific research manuscript. Your job is to read ONE paper and extract information most relevant to this manuscript.

### Our Research Question
[Full research question from scope.md]

### Our Hypothesis
[What we expect to find / are arguing]

### Our Key Findings
[Summary of main results - what we found]

### Our Methods
[Brief description of study design, population, techniques used]

### Target Journal
[Journal name] - [word limit] words - [citation style]

### What to Look For in This Paper

As you read, focus on extracting:
1. **Background claims** we can cite to establish the problem's importance
2. **Methodological precedents** that justify our approach
3. **Comparable results** we can compare our findings against
4. **Contradictory findings** we need to address in Discussion
5. **Gaps identified** that our study fills
6. **Quotable statements** that frame the field well
```

If `notes/ethics-summary.md` exists, also include:
- Study objectives from ethics document
- Target population details
- Approved procedures

---

## Step 2: Inventory Papers

List all PDFs in the `papers/` folder:

```bash
ls papers/*.pdf
```

Create processing queue:

```markdown
## Papers to Process

| # | Filename | Status |
|---|----------|--------|
| 1 | smith-2023.pdf | Pending |
| 2 | jones-2022.pdf | Pending |
| 3 | wilson-2021.pdf | Pending |
...
```

Copy PDFs to central library:

```bash
mkdir -p notes/papers-library
cp papers/*.pdf notes/papers-library/
```

---

## Step 3: Process Each PDF via Subagent

**CRITICAL: Process ONE paper at a time using the Task tool.**

For each PDF in the queue:

### 3a. Spawn Subagent

Use the Task tool to spawn an isolated subagent.

**IMPORTANT**: Each Task call must specify exactly ONE PDF file. Do not batch.

```
Task(
  subagent_type: "general-purpose",
  prompt: """
  ⛔ YOU ARE PROCESSING EXACTLY ONE PAPER. DO NOT READ ANY OTHER PDFs. ⛔

  ## Your Role

  You are a research assistant helping write a scientific manuscript. Your job is to:
  1. Read ONE paper
  2. Extract information most relevant to our manuscript
  3. Write condensed notes highlighting what's useful for our paper
  4. Exit immediately after

  ## Your ONE Paper

  **PDF to read**: papers/{filename}.pdf
  **Output file**: notes/papers/{filename}.md

  ## Manuscript Context (What We're Writing)

  {scope_summary}

  ## Instructions

  1. Read ONLY the PDF file specified above
  2. Extract citation metadata (authors, title, journal, DOI, PMID)
  3. Summarize the paper's main contribution
  4. **Focus on relevance**: What from this paper helps our manuscript?
     - Background claims we can cite?
     - Methods similar to ours?
     - Results we can compare against?
     - Contradictions we need to address?
     - Quotes that frame the problem well?
  5. Write condensed notes to the output file
  6. Return a one-line summary
  7. EXIT - Do not process any other papers

  ## Note Template

  # [First Author] et al., [Year]

  **Processed**: [timestamp]

  ## Citation
  - **Authors**: [full author list]
  - **Title**: [title]
  - **Journal**: [journal]
  - **Year**: [year]
  - **DOI**: [doi or "not found"]
  - **PMID**: [pmid or "not found"]

  ## Paper Summary
  [2-3 sentences on what this paper is about]

  ## Study Design
  - **Type**: [RCT, cohort, retrospective, etc.]
  - **Population**: [N, key characteristics]
  - **Methods**: [key techniques used]

  ## Key Findings
  | Finding | Statistic | Interpretation |
  |---------|-----------|----------------|
  | [finding] | [p-value, CI, effect size] | [what it means] |

  ## Relevance to Our Manuscript

  **Relationship**: [Supports / Contradicts / Provides Context / Methodological Reference]

  ### For Introduction
  - [Claims or context we can cite to frame our problem]
  - [Gaps this paper identifies that we address]

  ### For Methods
  - [Methodological precedents that justify our approach]

  ### For Discussion
  - [Results to compare against ours]
  - [Contradictions we need to explain]

  ## Key Quotes
  > "[Quotable statement that frames the field or problem well]" (p. X)

  > "[Another useful quote if available]" (p. X)

  ## Tags
  #[topic1] #[topic2] #[methodology]
  """
)
```

### 3b. Wait for Completion

Wait for the subagent to complete before processing the next paper.

### 3c. Update Status

```markdown
| # | Filename | Status |
|---|----------|--------|
| 1 | smith-2023.pdf | ✓ Complete |
| 2 | jones-2022.pdf | ✓ Complete |
| 3 | wilson-2021.pdf | Processing... |
```

### 3d. Handle Errors

If a subagent fails:
- Log the error
- Mark as "Needs manual review"
- Continue with next paper

---

## Step 4: Generate Bibliography

After ALL papers are processed, read the notes and compile bibliography.

Read each `notes/papers/*.md` and extract citations:

Create `notes/bibliography.md`:

```markdown
# Master Bibliography

**Generated**: [timestamp]
**Total References**: [n]

## References

| # | Citation | DOI | PMID |
|---|----------|-----|------|
| 1 | Smith JA, et al. Title. Journal. 2023;1:1-10. | 10.xxx | 123456 |
| 2 | Jones BB, et al. Title. Journal. 2022;2:20-30. | 10.xxx | 234567 |

## Papers Needing Attention

| Paper | Issue |
|-------|-------|
| [filename] | [missing DOI / couldn't process / etc.] |
```

---

## Step 5: Synthesize Literature

Read all `notes/papers/*.md` files and create synthesis.

**Note**: At this point you're only reading small markdown files, not full PDFs.

Create `notes/literature-synthesis.md`:

```markdown
# Literature Synthesis

**Generated**: [timestamp]
**Research Question**: [from scope.md]
**Papers Reviewed**: [n]

---

## Paper Citation Tracker

Track where each paper naturally fits. **Do not force papers** — if a paper doesn't contribute meaningfully, mark it as "Not Applicable" without guilt.

| # | Citation | Note File | Natural Fit | Status |
|---|----------|-----------|-------------|--------|
| 1 | Smith et al., 2023 | notes/papers/smith-2023.md | Intro: establishes prevalence | Planned |
| 2 | Jones et al., 2022 | notes/papers/jones-2022.md | Discussion: comparable methods | Planned |
| 3 | Wilson et al., 2021 | notes/papers/wilson-2021.md | Discussion: contrasting findings | Planned |
| 4 | Brown et al., 2020 | notes/papers/brown-2020.md | Different population, tangential | Not Applicable |

**Status Legend**:
- **Planned**: Paper has a natural place in the manuscript
- **Not Applicable**: Paper doesn't fit naturally — this is acceptable

**Guidance**: The goal is a coherent manuscript, not maximum citations. A paper that requires an awkward sentence or forced parenthetical to include should be marked "Not Applicable."

---

## Source Inventory

| # | Citation | Type | Relationship |
|---|----------|------|--------------|
| 1 | Smith et al., 2023 | Original Research | Supports |
| 2 | Jones et al., 2022 | Review | Context |

---

## Key Themes

### Theme 1: [Theme Name]

**Summary**: [2-3 sentences]

**Sources**: Smith 2023, Jones 2022

**Relevance**: [connection to our study]

### Theme 2: [Theme Name]

[repeat pattern]

---

## Supporting Findings

| Finding | Source | How It Supports Our Work |
|---------|--------|--------------------------|
| [finding] | Smith 2023 | [explanation] |

---

## Contradictory Findings

| Finding | Source | Possible Explanation |
|---------|--------|---------------------|
| [finding] | Jones 2022 | [why it differs] |

---

## Gaps in Literature

| Gap | Evidence | How We Address It |
|-----|----------|-------------------|
| [gap] | No studies on X | We examine X |

---

## Implications for Manuscript

### Introduction
- **Paragraph 1 (Problem)**: Cite [sources]
- **Paragraph 2 (Knowledge)**: Cite [sources]
- **Paragraph 3 (Gap)**: Cite [sources]
- **Paragraph 4 (This Study)**: Reference scope.md

### Discussion
- **Comparisons**: [sources for comparing results]
- **Interpretation**: [sources for mechanisms]
```

---

## Step 6: Draft Introduction

Using `notes/literature-synthesis.md`, draft the Introduction.

### Structure (4 paragraphs)

**Paragraph 1: The Problem**
- Significance of the clinical/scientific problem
- Epidemiology, impact, importance

**Paragraph 2: Current Knowledge**
- What is currently known
- Existing approaches and successes

**Paragraph 3: The Gap**
- What remains unknown
- Limitations of current approaches

**Paragraph 4: This Study**
- Purpose and approach
- What this paper contributes

### Writing Guidelines

- Present tense for established facts ("MRI provides...")
- Past tense for specific findings ("Smith et al. found...")
- Every claim needs a citation [1]
- Numbered citations in order of appearance
- Aim for ~500-800 words

### Draft Format

Create `drafts/introduction.md`:

```markdown
# Introduction

[Paragraph 1 - Problem and significance]

[Paragraph 2 - Current knowledge]

[Paragraph 3 - Gap and limitations]

[Paragraph 4 - This study's purpose]

---

## References Used

1. [Citation] - notes/papers/smith-2023.md
2. [Citation] - notes/papers/jones-2022.md
...
```

---

## Output

Save to:
- `notes/papers-library/*.pdf` - Central PDF storage
- `notes/papers/*.md` - Condensed notes per paper
- `notes/bibliography.md` - Master reference list
- `notes/literature-synthesis.md` - Aggregated synthesis
- `drafts/introduction.md` - Introduction draft

Return summary:
- Papers processed: [n]
- Papers with planned citations: [n]
- Papers not applicable: [n]
- Papers with issues: [n]
- Key themes identified: [n]
- Introduction word count: [n]
