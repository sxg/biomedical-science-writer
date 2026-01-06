---
name: paper-processor
description: Process a single PDF paper and generate condensed notes. Designed to run as a subagent with isolated context. Receives paper path and scope context, outputs structured markdown notes.
---

# Paper Processor (Subagent)

Processes a single PDF and generates condensed notes. Runs as an isolated subagent to prevent context overflow when processing many papers.

## Input

The orchestrator provides:
- `paper_path`: Path to the PDF file (e.g., `papers/smith-2023.pdf`)
- `scope_context`: Brief summary of research question and key findings from scope.md
- `output_path`: Where to write notes (e.g., `notes/papers/smith-2023.md`)

## Workflow

```
[Read the PDF]
     │
     ▼
[Extract key information]
     │
     ▼
[Generate condensed notes]
     │
     ▼
[Write to output_path]
     │
     ▼
[Return summary to orchestrator]
```

## Step 1: Read the PDF

Use the Read tool to read the PDF file. Claude can natively read PDF content.

```
Read: {paper_path}
```

## Step 2: Extract Key Information

Focus on extracting:

1. **Citation metadata**
   - Authors, title, journal, year, volume, pages
   - DOI and PMID if visible
   - Extract from header/footer or first page

2. **Core content**
   - Main research question/objective
   - Study design and population
   - Key methods
   - Primary results with statistics
   - Main conclusions

3. **Relevance to our study**
   - How does this paper relate to the scope context?
   - Does it support, contradict, or provide context?
   - What can we cite it for?

## Step 3: Generate Condensed Notes

Create markdown following this template:

```markdown
# [First Author] et al., [Year]

**Processed**: [timestamp]
**Source**: [paper_path]

## Citation

**Authors**: [full author list]
**Title**: [title]
**Journal**: [journal name]
**Year**: [year]
**Volume/Pages**: [vol]:[pages]
**DOI**: [doi or "not found"]
**PMID**: [pmid or "not found"]

## Summary

[2-3 sentence summary of the paper's main contribution]

## Study Design

- **Type**: [RCT, cohort, case-control, cross-sectional, etc.]
- **Population**: [N, characteristics]
- **Setting**: [where/when]

## Key Methods

- [Method 1]
- [Method 2]
- [Method 3]

## Key Results

| Finding | Statistic | Interpretation |
|---------|-----------|----------------|
| [finding 1] | [p-value, CI, effect size] | [what it means] |
| [finding 2] | [statistic] | [interpretation] |

## Relevance to Our Study

**Relationship**: [Supports | Contradicts | Provides Context | Methodological Reference]

[2-3 sentences explaining how this paper relates to our research question]

## Quotable Passages

> "[Direct quote that might be useful for Introduction or Discussion]"

## Use In Manuscript

- [ ] Introduction - background/context
- [ ] Introduction - gap identification
- [ ] Methods - methodological justification
- [ ] Discussion - comparison of findings
- [ ] Discussion - interpretation

## Tags

#[topic1] #[topic2] #[methodology]
```

## Step 4: Write Output

Write the condensed notes to `{output_path}`.

## Step 5: Return Summary

Return a brief summary to the orchestrator:

```
Processed: {paper_path}
Citation: [First Author] et al., [Year]. [Journal].
Relevance: [Supports/Contradicts/Context] - [one sentence]
Output: {output_path}
```

## Error Handling

If the PDF cannot be read or is corrupted:

```markdown
# [filename]

**Status**: ERROR - Could not process
**Reason**: [PDF unreadable / encrypted / corrupted / etc.]

**Action Required**: User should provide readable PDF or remove from papers/ folder.
```

## Context Efficiency

This skill is designed for minimal context usage:
- Read ONE paper
- Generate ONE notes file
- Exit immediately

Do NOT:
- Read multiple papers
- Read other notes files
- Attempt synthesis
- Make comparisons to other papers

The orchestrator handles synthesis after all papers are processed.
