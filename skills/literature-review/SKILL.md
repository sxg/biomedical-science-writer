---
name: literature-review
description: Process PDF papers and conduct web searches to generate structured notes and draft the Introduction section. Third step of biomedical-science-writer workflow. Requires scope.md and papers/ folder.
---

# Literature Review

Processes PDF papers from the project folder, conducts supplementary web searches, generates structured notes for each source, and drafts the Introduction section.

## Prerequisites

- `scope.md` must exist (from scoping step)
- `papers/` folder with PDF files
- `notes/papers/` and `notes/search/` directories created

## Workflow

```
[Read scope.md for context]
     │
     ▼
[Process PDFs] ─── For each PDF in papers/
     │              └── Generate notes/papers/{name}.md
     ▼
[Identify Gaps] ─── What's missing from provided papers?
     │
     ▼
[Web Search] ─── Search for additional sources
     │           └── Generate notes/search/*.md
     ▼
[Draft Introduction] ─── Synthesize all notes
     │
     ▼
[Output] ─── drafts/introduction.md
```

## Step 1: Read Scope Context

From `scope.md`, extract:
- Research question (frames the literature review)
- Key findings (need literature context for these)
- Target journal (determines depth/breadth)
- Any specific papers user wants to cite

## Step 2: Process PDF Papers

For each PDF in `papers/`:

### 2a. Extract Text

Use the pdf skill to read content:

```bash
# List available PDFs
ls papers/*.pdf
```

For each PDF, extract and analyze the content.

### 2b. Generate Paper Note

Create `notes/papers/{filename}.md` using this structure:

```markdown
# [First Author] et al., [Year]

**Source**: papers/[filename].pdf
**Processed**: [timestamp]

## Citation

[Author AA, Author BB, et al. Title. Journal Year;Vol:Pages. doi:XX]

## Summary

[2-3 sentence summary of the paper's main contribution]

## Key Claims

1. [Main finding relevant to our research question]
2. [Secondary finding]
3. [Methodological contribution if relevant]

## Methods

- **Study Design**: [design type]
- **Population**: [n, characteristics]
- **Key Technique**: [main methodology]

## Results Relevant to Our Study

| Finding | Statistic | Relevance |
|---------|-----------|-----------|
| [finding] | [stat] | [how it relates to our work] |

## Quotes

> "[Key quote that might be useful]" (p. X)

## Relationship to Our Work

**Type**: [Supports | Contradicts | Provides Context | Methodological Reference]

[2-3 sentences explaining how this paper relates to our research question and findings]

## Use In Manuscript

- [ ] Introduction - background context
- [ ] Introduction - gap identification  
- [ ] Methods - methodological justification
- [ ] Discussion - comparison of findings
- [ ] Discussion - mechanistic interpretation

## Tags

#[topic1] #[topic2] #[methodology]
```

### 2c. Categorize Papers

As you process, mentally categorize:
- **Foundational**: Seminal papers that established the field
- **Recent**: Papers from last 2-3 years showing current state
- **Methodological**: Papers that justify the methods used
- **Comparative**: Papers with findings to compare against

## Step 3: Identify Gaps

After processing all PDFs, assess what's missing:

### Literature Gap Analysis

Based on scope.md research question, we need sources that:
1. [ ] Establish the clinical/scientific problem
2. [ ] Show current state of knowledge
3. [ ] Identify the gap our study addresses
4. [ ] Provide methodological precedent
5. [ ] Offer comparison points for discussion

Missing categories: [list what's not covered by provided PDFs]

## Step 4: Web Search for Gaps

Conduct targeted searches to fill identified gaps:

### Search Strategy

```
# Search 1: Background/problem
web_search: "[disease/condition] clinical challenge site:pubmed.ncbi.nlm.nih.gov"

# Search 2: Current state
web_search: "[technique] [application] review recent

# Search 3: Methodological
web_search: "[method] validation [field]"
```

### Process Search Results

For each useful result found:

1. Fetch the abstract/full text if available:
   ```
   web_fetch: [pubmed or journal URL]
   ```

2. Create note in `notes/search/`:

```markdown
# [Author] et al., [Year] (Web Search)

**Source**: [URL]
**Search Query**: [what search found this]
**Processed**: [timestamp]

## Citation

[Formatted citation]

## Relevance

[Why this was retrieved - what gap it fills]

## Key Points

1. [Main relevant point]
2. [Secondary point]

## Use In Manuscript

- [ ] Introduction - [specific use]
```

## Step 5: Draft Introduction

Synthesize all notes into Introduction following this structure:

### Introduction Structure (4 paragraphs)

**Paragraph 1: The Problem**
- Broad significance
- Clinical/scientific importance
- Draw from: foundational papers, review articles

**Paragraph 2: Current Knowledge**
- What is known
- Existing approaches and their successes
- Draw from: recent papers, methodological papers

**Paragraph 3: The Gap**
- What remains unknown or problematic
- Limitations of current approaches
- Draw from: comparative papers, recent papers

**Paragraph 4: This Study**
- Purpose and approach
- Brief methods overview
- What this paper contributes
- Draw from: scope.md

### Writing Guidelines

- Present tense for established facts
- Past tense for specific study findings
- Every claim needs a citation [1]
- Use numbered citations in order of appearance
- Keep paragraphs focused (one main idea each)

### Draft Format

```markdown
# Introduction

[Paragraph 1 - Problem]

[Paragraph 2 - Current Knowledge]  

[Paragraph 3 - Gap]

[Paragraph 4 - This Study]

---

## References Used

1. [Citation 1] - notes/papers/smith-2023.md
2. [Citation 2] - notes/search/pubmed-001.md
...
```

## Output

Save to:
- `notes/papers/*.md` - One note per PDF
- `notes/search/*.md` - One note per web search result
- `drafts/introduction.md` - Introduction draft

Return to parent skill with summary:
- Papers processed: [n]
- Web sources added: [n]
- Total references: [n]
- Introduction word count: [n]
