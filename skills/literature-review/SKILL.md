---
name: literature-review
description: Discover and process scientific papers from three sources (user-provided PDFs, web search, and reference chaining), generate structured notes, and draft the Introduction section. Third step of biomedical-science-writer workflow. Requires scope.md.
---

# Literature Review

Systematically discovers relevant scientific literature through three complementary methods, generates structured notes for each source, and drafts the Introduction section.

## Three Sources of Papers

This skill finds papers through three methods, applied iteratively:

```
┌─────────────────────────────────────────────────────────────────┐
│                     PAPER DISCOVERY LOOP                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐                                               │
│  │ 1. PROVIDED  │  User-supplied PDFs in papers/ folder        │
│  │    PAPERS    │  → Process first, highest priority            │
│  └──────┬───────┘                                               │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────┐                                               │
│  │ 2. WEB       │  Search PubMed, Google Scholar, journals     │
│  │    SEARCH    │  → Fill gaps not covered by provided papers   │
│  └──────┬───────┘                                               │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────┐                                               │
│  │ 3. REFERENCE │  Extract citations from papers found above   │
│  │    CHAINING  │  → "Snowball" to find seminal/related work   │
│  └──────┬───────┘                                               │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────┐                                               │
│  │   ITERATE    │  If gaps remain, repeat steps 2-3            │
│  └──────────────┘                                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Critical Requirement: Local Paper Storage

**ALL papers referenced in the manuscript MUST be saved locally.**

Every paper discovered through any source must have:
1. **Local PDF copy** saved in `notes/papers-library/`
2. **Complete citation** in the note file
3. **Permanent links** (DOI and/or PMID and/or URL)

### Paper Storage Structure

```
notes/
├── papers-library/           # ALL PDFs stored here
│   ├── smith-2023.pdf       # From user-provided
│   ├── jones-2022.pdf       # Downloaded from web search
│   └── wilson-2019.pdf      # Downloaded via reference chain
├── papers/                   # Notes for user-provided papers
├── search/                   # Notes for web search papers
└── references/               # Notes for reference-chained papers
```

### Required Fields for Every Paper

Every paper note MUST include:

| Field | Required? | Example |
|-------|-----------|---------|
| Local PDF path | **YES** | `notes/papers-library/smith-2023.pdf` |
| Full citation | **YES** | Smith JA, Jones BB. Title. Journal. 2023;1:1-10. |
| DOI | Yes (if exists) | `10.1000/example.123` |
| PMID | Yes (if exists) | `12345678` |
| URL | **YES** | `https://pubmed.ncbi.nlm.nih.gov/12345678/` |

**A paper without a local copy and proper links CANNOT be cited in the manuscript.**

## Prerequisites

- `scope.md` must exist (from scoping step)
- `notes/irb-summary.md` may exist (provides study objectives and population context)
- `notes/irb-scope-comparison.md` may exist (clarifies scope differences)
- `papers/` folder (may be empty if user has no PDFs yet)
- Create directories: `notes/papers/`, `notes/search/`, `notes/references/`, `notes/papers-library/`

## Workflow Overview

```
[Read scope.md for context]
     │
     ▼
[SOURCE 1: Process user-provided PDFs] ─── papers/*.pdf
     │                                      └── notes/papers/*.md
     ▼
[Gap Analysis] ─── What literature categories are missing?
     │
     ▼
[SOURCE 2: Web Search] ─── PubMed, Google Scholar
     │                     └── notes/search/*.md
     ▼
[SOURCE 3: Reference Chaining] ─── Extract refs from processed papers
     │                             └── notes/references/*.md
     ▼
[Iterate if needed] ─── Repeat sources 2-3 until coverage adequate
     │
     ▼
[SYNTHESIZE LITERATURE] ─── Aggregate findings across all papers
     │                       └── notes/literature-synthesis.md
     ▼
[Draft Introduction] ─── Use synthesis to write Introduction
     │
     ▼
[Output] ─── drafts/introduction.md
```

---

## Step 1: Read Scope and IRB Context

From `scope.md`, extract:
- Research question (frames what literature is relevant)
- Key findings (need literature context for comparison)
- Target journal (determines citation depth)
- Specific papers user mentioned wanting to cite

If `notes/irb-summary.md` exists, also extract:
- **Study objectives** - Use to frame Introduction paragraph 4 (study purpose)
- **Target population** - Guides search for comparable studies
- **Procedures and endpoints** - Identifies methodological literature to find
- **Sample size justification** - May reference prior studies to cite

If `notes/irb-scope-comparison.md` exists:
- Note any scope differences - manuscript should reflect actual scope, not full IRB scope
- Use IRB objectives for framing, but findings should match actual research

---

## Step 2: Source 1 — User-Provided Papers

Process PDFs the user has placed in `papers/`. These are highest priority as the user has specifically selected them.

### 2a. Inventory Provided Papers

```bash
ls papers/*.pdf
```

### 2b. Process Each PDF

For each PDF, use the pdf skill to read and analyze content.

### 2c. Copy PDF to Library

Copy the user-provided PDF to the central library:

```bash
cp papers/[filename].pdf notes/papers-library/[filename].pdf
```

### 2d. Generate Paper Note

Create `notes/papers/{filename}.md`:

```markdown
# [First Author] et al., [Year]

**Discovery Method**: User-provided
**Processed**: [timestamp]

## Local Copy

**PDF**: `notes/papers-library/[filename].pdf`
**Original Location**: `papers/[filename].pdf`

## Links (REQUIRED)

| Type | Value |
|------|-------|
| DOI | `10.xxxx/xxxxx` |
| PMID | `12345678` |
| PubMed URL | `https://pubmed.ncbi.nlm.nih.gov/12345678/` |
| Full Text URL | `[journal URL if available]` |

## Citation (REQUIRED)

**Formatted Citation**:
> Author AA, Author BB, et al. Title of the article. Journal Name. Year;Volume(Issue):Pages. doi:10.xxxx/xxxxx

**BibTeX** (for reference manager):
```bibtex
@article{firstauthor2023,
  author = {Author, First A and Author, Second B},
  title = {Title of the Article},
  journal = {Journal Name},
  year = {2023},
  volume = {1},
  pages = {1-10},
  doi = {10.xxxx/xxxxx},
  pmid = {12345678}
}
```

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

## Key References to Chase

List important papers from this paper's references section that should be retrieved:

1. [Author et al. - "Title" - Why important]
2. [Author et al. - "Title" - Why important]

## Use In Manuscript

- [ ] Introduction - background context
- [ ] Introduction - gap identification
- [ ] Methods - methodological justification
- [ ] Discussion - comparison of findings
- [ ] Discussion - mechanistic interpretation

## Tags

#[topic1] #[topic2] #[methodology] #user-provided
```

### 2d. Categorize Papers

As you process, track coverage across categories:
- **Foundational**: Seminal papers that established the field
- **Recent**: Papers from last 2-3 years showing current state
- **Methodological**: Papers that justify the methods used
- **Comparative**: Papers with findings to compare against

---

## Step 3: Gap Analysis

After processing user-provided papers, assess coverage:

### Literature Coverage Checklist

| Category | Needed For | Covered? | Gap Description |
|----------|------------|----------|-----------------|
| Problem significance | Intro ¶1 | [ ] | |
| Current approaches | Intro ¶2 | [ ] | |
| Limitations/gap | Intro ¶3 | [ ] | |
| Methodological precedent | Methods | [ ] | |
| Comparison findings | Discussion | [ ] | |
| Mechanistic context | Discussion | [ ] | |

### Identify Specific Gaps

Based on scope.md and processed papers:

1. **Missing topics**: [list topics not yet covered]
2. **Missing time periods**: [need more recent? need foundational?]
3. **Missing perspectives**: [need opposing viewpoints? different methods?]

---

## Step 4: Source 2 — Web Search

Conduct targeted searches to fill identified gaps. Primary sources:

### 4a. PubMed Search (Primary)

PubMed is the authoritative source for biomedical literature.

**Search strategies:**

```
# Background/epidemiology
web_search: "[condition] epidemiology prevalence site:pubmed.ncbi.nlm.nih.gov"

# Recent advances
web_search: "[technique] [application] site:pubmed.ncbi.nlm.nih.gov" (filter: last 3 years)

# Methodological validation
web_search: "[method] validation accuracy site:pubmed.ncbi.nlm.nih.gov"

# Review articles for broad context
web_search: "[topic] systematic review OR meta-analysis site:pubmed.ncbi.nlm.nih.gov"

# Specific known gaps
web_search: "[specific gap topic] site:pubmed.ncbi.nlm.nih.gov"
```

### 4b. Google Scholar (Secondary)

For broader coverage, preprints, and highly-cited work:

```
web_search: "[topic] [method] site:scholar.google.com"
```

### 4c. Process Search Results

For each relevant result:

1. **Fetch the abstract/content:**
   ```
   web_fetch: [PubMed URL or DOI link]
   ```

2. **Download PDF to library (REQUIRED):**

   Attempt to download the full PDF:
   - Check if open access via PubMed Central
   - Check publisher site for PDF link
   - Use Unpaywall or similar if available

   Save to: `notes/papers-library/[firstauthor]-[year].pdf`

   **If PDF unavailable:**
   - Document why (paywalled, not available, etc.)
   - Save abstract/available content as backup
   - Flag for user to provide access

3. **Create note in `notes/search/`:**

```markdown
# [Author] et al., [Year]

**Discovery Method**: Web search
**Search Query**: "[query used]"
**Processed**: [timestamp]

## Local Copy (REQUIRED)

**PDF**: `notes/papers-library/[firstauthor]-[year].pdf`
**PDF Status**: [Downloaded | Open Access | Paywalled - User Access Needed | Abstract Only]

## Links (REQUIRED)

| Type | Value |
|------|-------|
| DOI | `10.xxxx/xxxxx` |
| PMID | `12345678` |
| PubMed URL | `https://pubmed.ncbi.nlm.nih.gov/12345678/` |
| PMC URL | `https://www.ncbi.nlm.nih.gov/pmc/articles/PMCxxxxxx/` (if open access) |
| Publisher URL | `[direct link to paper]` |

## Citation (REQUIRED)

**Formatted Citation**:
> Author AA, Author BB, et al. Title of the article. Journal Name. Year;Volume(Issue):Pages. doi:10.xxxx/xxxxx

**BibTeX**:
```bibtex
@article{firstauthor2023,
  author = {Author, First A and Author, Second B},
  title = {Title of the Article},
  journal = {Journal Name},
  year = {2023},
  volume = {1},
  pages = {1-10},
  doi = {10.xxxx/xxxxx},
  pmid = {12345678}
}
```

## Abstract

[Full abstract text - always available from PubMed]

## Relevance

**Gap Filled**: [which gap from Step 3 this addresses]

[Why this paper was retrieved and how it helps]

## Key Points

1. [Main relevant point]
2. [Secondary point]
3. [Tertiary point]

## Key References to Chase

1. [Author et al. - "Title" - Why important]
2. [Author et al. - "Title" - Why important]

## Use In Manuscript

- [ ] Introduction - [specific use]
- [ ] Discussion - [specific use]

## Tags

#[topic] #web-search #[gap-category]
```

### 4d. Handle Paywalled Papers

If a paper is behind a paywall:

1. **Check for open access versions:**
   - PubMed Central (PMC)
   - Author's personal/institutional page
   - Preprint servers (bioRxiv, medRxiv)

2. **If no open access available:**
   ```markdown
   ## PDF Access Issue

   **Status**: Paywalled
   **Publisher**: [publisher name]
   **Options**:
   - [ ] User has institutional access
   - [ ] User can request from author
   - [ ] Available via interlibrary loan

   **Action Required**: User must provide PDF or confirm access method
   ```

3. **Ask user**: "I found [paper title] but it's paywalled. Do you have access through your institution, or should I skip this paper?"

---

## Step 5: Source 3 — Reference Chaining

Extract and pursue important citations from papers already processed. This "snowball" method finds seminal work and related studies.

### 5a. Collect References to Chase

Review all processed notes for "Key References to Chase" sections:

```bash
grep -h "Key References to Chase" -A 5 notes/papers/*.md notes/search/*.md
```

### 5b. Prioritize References

Rank references by:
1. **Frequency**: Cited by multiple processed papers
2. **Recency**: Recent papers for current state
3. **Seminal**: Older papers cited as foundational
4. **Relevance**: Directly addresses our research question

### 5c. Retrieve Priority References

For each high-priority reference:

1. **Search for the paper:**
   ```
   web_search: "[Author] [Year] [Key title words] site:pubmed.ncbi.nlm.nih.gov"
   ```

2. **Fetch and process:**
   ```
   web_fetch: [PubMed or DOI URL]
   ```

3. **Download PDF to library (REQUIRED):**

   Same process as web search - attempt to download, flag if paywalled.

   Save to: `notes/papers-library/[firstauthor]-[year].pdf`

4. **Create note in `notes/references/`:**

```markdown
# [Author] et al., [Year]

**Discovery Method**: Reference chaining
**Cited By**: [list which processed papers cited this]
**Processed**: [timestamp]

## Local Copy (REQUIRED)

**PDF**: `notes/papers-library/[firstauthor]-[year].pdf`
**PDF Status**: [Downloaded | Open Access | Paywalled - User Access Needed | Abstract Only]

## Links (REQUIRED)

| Type | Value |
|------|-------|
| DOI | `10.xxxx/xxxxx` |
| PMID | `12345678` |
| PubMed URL | `https://pubmed.ncbi.nlm.nih.gov/12345678/` |
| PMC URL | `https://www.ncbi.nlm.nih.gov/pmc/articles/PMCxxxxxx/` (if open access) |
| Publisher URL | `[direct link to paper]` |

## Citation (REQUIRED)

**Formatted Citation**:
> Author AA, Author BB, et al. Title of the article. Journal Name. Year;Volume(Issue):Pages. doi:10.xxxx/xxxxx

**BibTeX**:
```bibtex
@article{firstauthor2023,
  author = {Author, First A and Author, Second B},
  title = {Title of the Article},
  journal = {Journal Name},
  year = {2023},
  volume = {1},
  pages = {1-10},
  doi = {10.xxxx/xxxxx},
  pmid = {12345678}
}
```

## Why This Paper

[Explain why this was chased - what papers cited it and why it seemed important]

## Summary

[2-3 sentence summary]

## Key Claims

1. [Main claim]
2. [Secondary claim]

## Relationship to Our Work

**Type**: [Foundational | Supports | Contradicts | Methodological]

[How this connects to our research]

## Further References to Chase

[Only if this paper reveals additional important citations not yet captured]

## Use In Manuscript

- [ ] Introduction - [specific use]
- [ ] Discussion - [specific use]

## Tags

#[topic] #reference-chain #[category]
```

---

## Step 6: Iterate if Needed

After completing sources 1-3, reassess coverage:

### Coverage Re-check

| Category | Sources Found | Adequate? |
|----------|---------------|-----------|
| Problem significance | [n] papers | ✓ / ✗ |
| Current approaches | [n] papers | ✓ / ✗ |
| Limitations/gap | [n] papers | ✓ / ✗ |
| Methodological precedent | [n] papers | ✓ / ✗ |
| Comparison findings | [n] papers | ✓ / ✗ |

### If Gaps Remain

Repeat Steps 4-5 with refined searches targeting remaining gaps.

### Stopping Criteria

Stop when:
- All Introduction paragraph types have adequate support
- Discussion comparison points are covered
- Key methodological citations exist
- Diminishing returns on new searches

---

## Step 7: Validate Paper Library

**Before synthesizing, verify ALL papers have required information.**

### 7a. Run Library Audit

Check that every paper note has:

```markdown
## Paper Library Audit

| Paper | PDF Saved? | DOI? | PMID? | URL? | Citation? | Status |
|-------|------------|------|-------|------|-----------|--------|
| Smith 2023 | ✓ | ✓ | ✓ | ✓ | ✓ | Complete |
| Jones 2022 | ✓ | ✓ | — | ✓ | ✓ | Complete (no PMID) |
| Wilson 2019 | ✗ | ✓ | ✓ | ✓ | ✓ | **NEEDS PDF** |
| Brown 2020 | ✓ | ✗ | ✗ | ✓ | ✗ | **INCOMPLETE** |
```

### 7b. Resolve Missing Items

For each incomplete paper:

1. **Missing PDF**: Ask user for access or find alternative source
2. **Missing DOI**: Search CrossRef or publisher site
3. **Missing PMID**: Search PubMed by title
4. **Missing citation**: Construct from available metadata

### 7c. Confirm Before Proceeding

Present summary to user:

```
## Paper Library Summary

**Total papers**: 15
**Complete**: 12
**Needs attention**: 3

### Papers Needing Action:

1. **Wilson 2019** - PDF needed (paywalled)
   - Do you have institutional access?

2. **Brown 2020** - Missing DOI and citation
   - Is this the correct paper: [title]?

3. **Garcia 2021** - Could not find PMID
   - This appears to be a preprint. Is that correct?

**Please resolve these before I proceed with synthesis.**
```

### 7d. Generate Master Bibliography

Create `notes/bibliography.md`:

```markdown
# Master Bibliography

**Generated**: [timestamp]
**Total References**: [n]

## Complete References (Ready to Cite)

| # | Citation | DOI | PMID | PDF |
|---|----------|-----|------|-----|
| 1 | Smith JA, et al. Title... | 10.xxx | 123456 | ✓ |
| 2 | Jones BB, et al. Title... | 10.xxx | 234567 | ✓ |

## References Needing Attention

| Paper | Issue | Action Required |
|-------|-------|-----------------|
| Wilson 2019 | No PDF | User to provide |

## BibTeX Export

[All BibTeX entries concatenated for reference manager import]
```

---

## Step 8: Synthesize Literature

Before drafting the Introduction, create a synthesis document that aggregates findings across all processed papers. This provides transparency into the literature review process and serves as a reference for the Discussion section.

### Generate `notes/literature-synthesis.md`

```markdown
# Literature Synthesis

**Generated**: [timestamp]
**Research Question**: [from scope.md]
**Total Sources Reviewed**: [n]

---

## Source Inventory

| # | Citation | Discovery Method | Type | Relationship |
|---|----------|------------------|------|--------------|
| 1 | Smith et al., 2023 | User-provided | Original Research | Supports |
| 2 | Jones et al., 2022 | Web search | Review | Context |
| 3 | Wilson et al., 2019 | Reference chain | Original Research | Contradicts |
| ... | ... | ... | ... | ... |

### Sources by Discovery Method

| Method | Count | Papers |
|--------|-------|--------|
| User-provided | [n] | [list] |
| Web search | [n] | [list] |
| Reference chaining | [n] | [list] |

### Sources by Relationship to Our Work

| Relationship | Count | Papers |
|--------------|-------|--------|
| Supports findings | [n] | [list] |
| Contradicts findings | [n] | [list] |
| Provides context | [n] | [list] |
| Methodological reference | [n] | [list] |

---

## Key Themes Identified

### Theme 1: [Theme Name]

**Summary**: [2-3 sentence description of this theme]

**Supporting Sources**:
- Smith et al., 2023: "[key finding]"
- Jones et al., 2022: "[key finding]"

**Relevance to Our Study**: [How this theme connects to our research question]

### Theme 2: [Theme Name]

**Summary**: [description]

**Supporting Sources**:
- [sources]

**Relevance to Our Study**: [connection]

### Theme 3: [Theme Name]

[repeat pattern]

---

## Findings That Support Our Hypothesis

| Finding | Source | Statistic | How It Supports |
|---------|--------|-----------|-----------------|
| [finding] | Smith 2023 | p < 0.001 | [explanation] |
| [finding] | Jones 2022 | OR = 2.3 | [explanation] |

**Synthesis**: [2-3 sentences summarizing how existing literature supports our expected findings]

---

## Contradictory or Conflicting Findings

| Finding | Source | Statistic | Why It Differs |
|---------|--------|-----------|----------------|
| [finding] | Wilson 2021 | NS | [possible explanation] |
| [finding] | Brown 2020 | opposite direction | [possible explanation] |

**Synthesis**: [2-3 sentences explaining contradictions and how our study may resolve them]

---

## Methodological Patterns

### Common Approaches in the Literature

| Method | Used By | Strengths | Limitations |
|--------|---------|-----------|-------------|
| [method 1] | Smith, Jones | [strengths] | [limitations] |
| [method 2] | Wilson, Brown | [strengths] | [limitations] |

### How Our Methods Compare

[2-3 sentences on how our methodology relates to existing approaches - what we adopted, what we improved]

---

## Gaps Identified in Literature

| Gap | Evidence | How Our Study Addresses |
|-----|----------|------------------------|
| [gap 1] | No studies have examined... | We directly test... |
| [gap 2] | Previous work limited to... | We extend to... |
| [gap 3] | Methodological limitation in... | We use improved... |

---

## Citation Map

### Foundational Papers (cited by many)

| Paper | Cited By | Key Contribution |
|-------|----------|------------------|
| [seminal paper] | 5 other sources | Established [concept] |
| [seminal paper] | 3 other sources | First to show [finding] |

### Citation Clusters

[Identify groups of papers that cite each other - indicates research communities/debates]

- **Cluster A**: Smith, Jones, Lee - focus on [topic]
- **Cluster B**: Wilson, Brown, Davis - focus on [topic]

---

## Implications for Manuscript Sections

### For Introduction

**Paragraph 1 (Problem)**: Use [sources] to establish significance
**Paragraph 2 (Current Knowledge)**: Draw from Theme [X] and [Y]
**Paragraph 3 (Gap)**: Cite gap evidence from [sources]
**Paragraph 4 (This Study)**: Position against [sources]

### For Discussion

**Comparison points**: [list key papers for comparing our results]
**Mechanistic interpretation**: [sources that explain mechanisms]
**Limitations context**: [sources that faced similar limitations]
**Future directions**: [sources suggesting next steps]

---

## Outstanding Questions

- [ ] [Any unresolved questions from the literature]
- [ ] [Ambiguities that need user clarification]
- [ ] [Potential additional searches needed]
```

### Synthesis Quality Checklist

Before proceeding to draft Introduction, verify:

- [ ] All processed papers are listed in inventory
- [ ] Key themes capture the main ideas across sources
- [ ] Supporting and contradicting findings are balanced
- [ ] Gaps are clearly articulated with evidence
- [ ] Citation map identifies foundational work
- [ ] Implications for each manuscript section are clear

---

## Step 9: Draft Introduction

Using `notes/literature-synthesis.md` as the primary guide, draft the Introduction. The synthesis document provides the themes, sources, and structure to follow.

**Only cite papers that have complete entries in `notes/bibliography.md`.**

### Introduction Structure (4 paragraphs)

**Paragraph 1: The Problem**
- Broad significance of the clinical/scientific problem
- Epidemiology, impact, importance
- Draw from: foundational papers, review articles, reference-chained seminal work

**Paragraph 2: Current Knowledge**
- What is currently known
- Existing approaches and their successes
- Draw from: recent papers, methodological papers, web search results

**Paragraph 3: The Gap**
- What remains unknown or problematic
- Limitations of current approaches
- Draw from: comparative papers, recent work identifying limitations

**Paragraph 4: This Study**
- Purpose and approach
- Brief methods overview
- What this paper contributes
- Draw from: scope.md

### Writing Guidelines

- Present tense for established facts ("MRI provides...")
- Past tense for specific study findings ("Smith et al. found...")
- Every claim needs a citation [1]
- Use numbered citations in order of appearance
- Keep paragraphs focused (one main idea each)
- Aim for ~500-800 words total

### Draft Format

```markdown
# Introduction

[Paragraph 1 - Problem and significance]

[Paragraph 2 - Current knowledge and approaches]

[Paragraph 3 - Gap and limitations]

[Paragraph 4 - This study's purpose]

---

## References Used

1. [Citation] - notes/papers/smith-2023.md (user-provided)
2. [Citation] - notes/search/pubmed-001.md (web search)
3. [Citation] - notes/references/jones-2019.md (reference chain)
...

## Source Summary

| Discovery Method | Count |
|------------------|-------|
| User-provided | [n] |
| Web search | [n] |
| Reference chaining | [n] |
| **Total** | [n] |
```

---

## Output

Save to:
- `notes/papers-library/*.pdf` - ALL paper PDFs (central library)
- `notes/papers/*.md` - Notes from user-provided PDFs
- `notes/search/*.md` - Notes from web search results
- `notes/references/*.md` - Notes from reference chaining
- `notes/bibliography.md` - Master bibliography with all citations and links
- `notes/literature-synthesis.md` - Aggregated synthesis across all sources
- `drafts/introduction.md` - Introduction draft

Return to parent skill with summary:
- User-provided papers processed: [n]
- Web search sources added: [n]
- Reference chain sources added: [n]
- Total unique references: [n]
- PDFs in library: [n]
- Papers needing user action: [n] (list if any)
- Key themes identified: [n]
- Supporting findings: [n]
- Contradictory findings: [n]
- Introduction word count: [n]
- Coverage status: [complete / gaps remaining]
