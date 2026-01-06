# Writer

A Claude Code plugin that drafts biomedical research manuscripts from organized project folders. Orchestrates an 8-step workflow with specialist agents for statistical accuracy and publication readiness.

## Installation

```bash
/plugin marketplace add sxg/biomedical-science-writer
/plugin install writer@science
```

## Features

- **8-Step Workflow**: Context ingestion → Scoping → Literature review → Code analysis → Results interpretation → Synthesis → Academic review → Assembly
- **Specialist Agents**: Biostatistician for statistical validation, Academic reviewer for publication readiness
- **Subagent-Based Literature Review**: Process user-provided PDFs via isolated subagents (prevents context overflow)
- **IRB Document Support**: Auto-extract ethics statements, scope comparison checkpoints
- **No Silent Assumptions**: Every interpretation requires user confirmation

## Project Structure

Organize your research project as follows:

```
project/
├── papers/              # PDF files of relevant literature
│   ├── smith-2023.pdf
│   └── jones-2022.pdf
├── data/                # Raw data outputs (CSV, Excel)
│   ├── results.csv
│   └── demographics.csv
├── figures/             # Generated figures (PNG, JPG, SVG)
│   ├── figure1.png
│   └── figure2.png
├── irb/                 # OPTIONAL - IRB protocol documents
│   └── protocol.pdf
└── config.md            # Project configuration
```

### config.md Template

```markdown
# Project Configuration

## GitHub Repository
url: https://github.com/username/repo-name
branch: main
access: private

## Constraints
word_limit: 3500
target_journal: Radiology: Artificial Intelligence
citation_style: AMA

## Additional Notes
[Any other context for the manuscript]
```

## Commands

| Command | Description |
|---------|-------------|
| `/writer:draft` | Full workflow from scratch |
| `/writer:literature` | Start from literature review |
| `/writer:methods` | Analyze code only |
| `/writer:results` | Interpret data only |
| `/writer:synthesize` | Generate Discussion/Abstract |
| `/writer:assemble` | Final manuscript assembly |
| `/writer:review` | Academic review checkpoint |

## Workflow

```
[1. Context Ingestion]
│   - Scan project folder
│   - Clone GitHub repository
│   - Extract IRB content (if provided)
│
▼
[2. Scoping]
│   - Define research question
│   - Confirm key findings
│   - IRB scope comparison checkpoint
│
▼
[3. Literature Review]
│   - Process user PDFs (via isolated subagents)
│   - Generate condensed notes
│   - Synthesize findings
│   - Draft Introduction
│
▼
[4. Code Analysis]
│   - Analyze GitHub repository
│   - ★ Biostatistician review
│   - Draft Methods
│
▼
[5. Results Interpretation]
│   - Analyze CSV data
│   - Interpret figures
│   - ★ Biostatistician review
│   - Draft Results
│
▼
[6. Synthesis]
│   - Draft Discussion
│   - Draft Abstract
│
▼
[7. Academic Review]
│   - Verify claims against citations
│   - Validate hypothesis alignment
│   - Independent results interpretation
│
▼
[8. Assembly]
│   - Confirm reviewer approval
│   - Populate ethics statement
│   - Generate manuscript.md
```

## Output Structure

The plugin generates:

```
project/
├── notes/
│   ├── papers/*.md           # Literature notes (from user PDFs)
│   ├── papers-library/*.pdf  # Central PDF storage
│   ├── bibliography.md       # Master reference list
│   ├── irb-summary.md        # IRB extraction
│   ├── irb-scope-comparison.md
│   ├── statistical-review.md
│   └── reviewer-feedback.md
├── drafts/
│   ├── introduction.md
│   ├── methods.md
│   ├── results.md
│   ├── discussion.md
│   └── abstract.md
├── inventory.md
├── scope.md
└── manuscript.md             # Final output
```

## IRB Document Support

Place IRB documents in the `irb/` folder (PDF, Word, or Markdown). The plugin will:

1. **Extract comprehensive study information** → `notes/irb-summary.md`
2. **Compare IRB scope vs actual scope** during scoping step
3. **Cross-reference procedures** in Methods and Results
4. **Auto-populate ethics statement** in final manuscript

## Requirements

- Claude Code CLI
- GitHub CLI (`gh`) for private repository access
- Optional: `document-skills` plugin for Word document support

## License

MIT
