---
name: biomedical-science-writer
description: Draft biomedical research manuscripts from research data. Use when user wants to write a research paper, has a project folder with papers, data, figures, and a GitHub repository link. Orchestrates context-ingestion, scoping, literature-review, code-analyzer, results-interpreter, synthesis, and assembler sub-skills.
---

# Biomedical Science Writer

Orchestrates the creation of scientific manuscript drafts through a structured, note-generating workflow.

## Expected Project Structure

User must organize their project folder as follows:

```
project/
├── papers/              # PDF files of relevant literature
│   ├── smith-2023.pdf
│   ├── jones-2022.pdf
│   └── ...
├── data/                # Raw data outputs (CSV, Excel)
│   ├── results.csv
│   ├── demographics.csv
│   └── ...
├── figures/             # Generated figures (PNG, JPG, SVG)
│   ├── figure1.png
│   ├── figure2.png
│   └── ...
└── config.md            # Project configuration (see template below)
```

### config.md Template

```markdown
# Project Configuration

## GitHub Repository
url: https://github.com/username/repo-name
branch: main
access: private  # or public

## Constraints
word_limit: 3500
target_journal: Radiology: Artificial Intelligence
citation_style: AMA

## Additional Notes
[Any other context for the manuscript]
```

## Output Structure

The skill generates intermediate notes and drafts:

```
project/
├── notes/
│   ├── papers/              # One .md per PDF analyzed
│   │   ├── smith-2023.md
│   │   └── jones-2022.md
│   ├── search/              # Web search results
│   │   └── pubmed-search-001.md
│   ├── code-analysis.md     # GitHub repo analysis
│   └── data-analysis.md     # Data/figures interpretation
├── drafts/
│   ├── introduction.md
│   ├── methods.md
│   ├── results.md
│   ├── discussion.md
│   └── abstract.md
├── inventory.md             # What's available in project
├── scope.md                 # Research question, findings, constraints
└── manuscript.md            # Final assembled draft
```

## Workflow

```
[1. Context Ingestion] ─── skills/context-ingestion/SKILL.md
│   - Scan project folder structure
│   - Validate required folders exist
│   - Clone/analyze GitHub repository
│   - Generate inventory.md
│
▼
[2. Scoping] ─── skills/scoping/SKILL.md
│   - Ask: research question
│   - Ask: key findings (cross-check with inventory)
│   - Ask: constraints (word limit, journal)
│   - Generate scope.md
│
▼
[3. Literature Review] ─── skills/literature-review/SKILL.md
│   - Process PDFs → notes/papers/*.md
│   - Web search for gaps → notes/search/*.md
│   - Draft Introduction → drafts/introduction.md
│
▼
[4. Code Analysis] ─── skills/code-analyzer/SKILL.md
│   - Analyze GitHub repository
│   - Extract methodology → notes/code-analysis.md
│   - Draft Methods → drafts/methods.md
│
▼
[5. Results Interpretation] ─── skills/results-interpreter/SKILL.md
│   - Analyze CSV data files
│   - Interpret figures
│   - Generate → notes/data-analysis.md
│   - Draft Results → drafts/results.md
│
▼
[6. Synthesis] ─── skills/synthesis/SKILL.md
│   - Read all notes/*.md
│   - Read drafts/introduction.md, methods.md, results.md
│   - Draft Discussion → drafts/discussion.md
│   - Draft Abstract → drafts/abstract.md
│
▼
[7. Assembly] ─── skills/assembler/SKILL.md
    - Combine all drafts
    - Apply formatting constraints
    - Generate → manuscript.md
```

## Entry Points

The workflow supports multiple entry points:

| Command | Starts At | Use When |
|---------|-----------|----------|
| `/biomedical-science-writer:draft` | Step 1 | Full workflow from scratch |
| `/biomedical-science-writer:literature` | Step 3 | Scope exists, need lit review |
| `/biomedical-science-writer:methods` | Step 4 | Need to analyze code only |
| `/biomedical-science-writer:results` | Step 5 | Need to interpret data only |
| `/biomedical-science-writer:synthesize` | Step 6 | All drafts exist, need Discussion |
| `/biomedical-science-writer:assemble` | Step 7 | All sections drafted, need final doc |

## Executing the Workflow

### Step 1: Context Ingestion

Read `skills/context-ingestion/SKILL.md` and follow it to:
- Validate project folder structure
- Parse config.md for GitHub URL and constraints
- Clone or fetch GitHub repository
- Inventory all available materials
- Output: `inventory.md`

### Step 2: Scoping

Read `skills/scoping/SKILL.md` and follow it to:
- Review inventory.md
- Ask user for research question
- Ask user for key findings (validate against data)
- Confirm constraints from config.md
- Output: `scope.md`

### Step 3: Literature Review

Read `skills/literature-review/SKILL.md` and follow it to:
- Process each PDF in `papers/` → `notes/papers/*.md`
- Conduct web searches to fill gaps → `notes/search/*.md`
- Synthesize into Introduction draft
- Output: `drafts/introduction.md`

### Step 4: Code Analysis

Read `skills/code-analyzer/SKILL.md` and follow it to:
- Analyze cloned GitHub repository
- Extract methodology, statistical approaches, tools
- Output: `notes/code-analysis.md`, `drafts/methods.md`

### Step 5: Results Interpretation

Read `skills/results-interpreter/SKILL.md` and follow it to:
- Analyze CSV files in `data/`
- Interpret figures in `figures/`
- Output: `notes/data-analysis.md`, `drafts/results.md`

### Step 6: Synthesis

Read `skills/synthesis/SKILL.md` and follow it to:
- Read all accumulated notes
- Integrate findings with literature context
- Output: `drafts/discussion.md`, `drafts/abstract.md`

### Step 7: Assembly

Read `skills/assembler/SKILL.md` and follow it to:
- Combine all draft sections
- Apply word limit and formatting
- Generate reference list
- Output: `manuscript.md`

## References

- `references/section-guidelines.md` - Writing conventions per section
- `references/citation-format.md` - Citation formatting styles
- `references/source-note-template.md` - Template for paper notes
