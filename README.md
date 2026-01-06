# Biomedical Science Writer

A Claude Code plugin that drafts biomedical research manuscripts from organized project folders. Orchestrates an 8-step workflow with specialist agents for statistical accuracy and publication readiness.

## Installation

### From GitHub Marketplace

```bash
/plugin marketplace add satyam/biomedical-science-writer
/plugin install biomedical-science-writer
```

### Manual Installation

Clone and install locally:

```bash
git clone https://github.com/satyam/biomedical-science-writer.git
/plugin install ./biomedical-science-writer
```

## Features

- **8-Step Workflow**: Context ingestion → Scoping → Literature review → Code analysis → Results interpretation → Synthesis → Academic review → Assembly
- **Specialist Agents**: Biostatistician for statistical validation, Academic reviewer for publication readiness
- **Three-Source Literature Review**: User PDFs, web search (PubMed), and reference chaining
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
| `/biomedical-science-writer:draft` | Full workflow from scratch |
| `/biomedical-science-writer:literature` | Start from literature review |
| `/biomedical-science-writer:methods` | Analyze code only |
| `/biomedical-science-writer:results` | Interpret data only |
| `/biomedical-science-writer:synthesize` | Generate Discussion/Abstract |
| `/biomedical-science-writer:assemble` | Final manuscript assembly |
| `/biomedical-science-writer:review` | Academic review checkpoint |

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
│   - Process user PDFs
│   - Web search (PubMed)
│   - Reference chaining
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
│   ├── papers/*.md           # Literature notes
│   ├── search/*.md           # Web search results
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
