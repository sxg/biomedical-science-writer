# Writer

A Claude Code plugin that drafts scientific research manuscripts from organized project folders. Orchestrates an 8-step workflow with specialist agents for statistical accuracy and publication readiness.

## Installation

```bash
/plugin marketplace add sxg/science
/plugin install writer@science
```

## Features

- **8-Step Workflow**: Context ingestion → Scoping → Literature review → Code analysis → Results interpretation → Synthesis → Academic review → Assembly
- **Specialist Agents**: Statistical reviewer for quantitative validation, Academic reviewer for publication readiness
- **Subagent-Based Literature Review**: Process user-provided PDFs via isolated subagents (prevents context overflow)
- **Ethics Document Support**: Auto-extract ethics statements, scope comparison checkpoints
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
├── ethics/              # OPTIONAL - Ethics/governance documents
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
target_journal: [Target Journal]
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
│   - Extract ethics content (if provided)
│
▼
[2. Scoping]
│   - Define research question
│   - Confirm key findings
│   - Ethics scope comparison checkpoint
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
│   - ★ Statistical review
│   - Draft Methods
│
▼
[5. Results Interpretation]
│   - Analyze CSV data
│   - Interpret figures
│   - ★ Statistical review
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
│   ├── ethics-summary.md        # Ethics document extraction
│   ├── ethics-scope-comparison.md
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

## Ethics Document Support

Place ethics/governance documents in the `ethics/` folder (PDF, Word, or Markdown). Supports IRB protocols, IACUC approvals, ethics committee decisions, and data governance agreements. The plugin will:

1. **Extract comprehensive study information** → `notes/ethics-summary.md`
2. **Compare ethics scope vs actual scope** during scoping step
3. **Cross-reference procedures** in Methods and Results
4. **Auto-populate ethics statement** in final manuscript

## Requirements

- Claude Code CLI
- GitHub CLI (`gh`) for private repository access
- Optional: `document-skills` plugin for Word document support

## License

MIT
