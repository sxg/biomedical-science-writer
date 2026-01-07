---
name: review
description: Review and critique a scientific manuscript, identifying critical issues with detailed explanations and recommendations. Produces a structured review with accept/revise/reject recommendation.
---

# Manuscript Review

Reviews a scientific manuscript using specialized subagents for statistical and academic evaluation, then produces a combined critique with ranked issues and an overall recommendation.

## Input

- `manuscript_path`: Path to the manuscript (PDF or Markdown)

## Workflow

```
[Read manuscript]
     │
     ▼
[Spawn subagents in parallel]
     ├──► Statistical Reviewer
     └──► Academic Reviewer
     │
     ▼
[Collect and merge issues]
     │
     ▼
[Rank issues by severity]
     │
     ▼
[Generate overall assessment]
     │
     ▼
[Determine recommendation]
     │
     ▼
[Write review file]
```

## Step 1: Read Manuscript

Detect format and read the manuscript content.

```
if manuscript_path ends with .pdf:
    Read PDF using Read tool
else if manuscript_path ends with .md:
    Read Markdown using Read tool
else:
    Error: Unsupported format
```

Extract:
- Title (if identifiable)
- Full manuscript content for subagents

## Step 2: Spawn Subagents

Launch both reviewers in parallel using the Task tool.

**IMPORTANT**: Run both subagents simultaneously for efficiency.

### Statistical Reviewer

```
Task(
  subagent_type: "reviewer:statistical-reviewer",
  prompt: """
  Review this scientific manuscript for statistical and methodological rigor.

  ## Manuscript Content

  {full_manuscript_content}

  ## Your Task

  Evaluate:
  1. Are statistical methods appropriate for the research question?
  2. Are methods executed correctly (sample size, test selection, assumptions)?
  3. Are results reported completely (effect sizes, CIs, p-values)?
  4. Are statistical conclusions supported by the reported numbers?
  5. Any signs of p-hacking, selective reporting, or missing analyses?

  ## Output Format

  Return issues as a structured list:

  ISSUE: [title]
  SEVERITY: [Critical/Major/Minor]
  PROBLEM: [detailed explanation so authors understand what's wrong]
  RECOMMENDATION: [specific guidance on how to fix, if applicable]
  ---

  Order issues from most to least severe.
  """
)
```

### Academic Reviewer

```
Task(
  subagent_type: "reviewer:academic-reviewer",
  prompt: """
  Review this scientific manuscript for academic rigor and significance.

  ## Manuscript Content

  {full_manuscript_content}

  ## Your Task

  Evaluate:
  1. Is the research question valid and well-defined?
  2. Does the study design actually answer the stated question?
  3. Are conclusions supported by the results (not overreaching)?
  4. Is the work novel and significant to the field?
  5. Are limitations acknowledged appropriately?
  6. Would readers in this field find this valuable?

  ## Output Format

  Return issues as a structured list:

  ISSUE: [title]
  SEVERITY: [Critical/Major/Minor]
  PROBLEM: [detailed explanation so authors understand what's wrong]
  RECOMMENDATION: [specific guidance on how to fix, if applicable]
  ---

  Order issues from most to least severe.
  """
)
```

## Step 3: Merge and Rank Issues

Collect issues from both subagents and merge into a single list.

### Severity Ranking

1. **Critical**: Fundamental flaws that invalidate the work
   - Incorrect statistical method that changes conclusions
   - Claims not supported by evidence
   - Major methodological errors
   - Ethical concerns

2. **Major**: Significant problems that must be addressed
   - Missing important analyses
   - Incomplete reporting
   - Overreaching conclusions
   - Unclear methodology

3. **Minor**: Issues that should be fixed but don't undermine the work
   - Presentation improvements
   - Minor clarifications needed
   - Additional context helpful

### Merge Algorithm

```
all_issues = statistical_issues + academic_issues
sorted_issues = sort by severity (Critical → Major → Minor)
within same severity, maintain original order
tag each issue with category (Statistical | Academic)
```

## Step 4: Generate Overall Assessment

Write a short paragraph (3-5 sentences) covering:

1. **Greatest strengths** (2-3): What does this manuscript do well?
2. **Key weaknesses** (2-3): What are the most significant problems?
3. **Recommendation rationale**: Why accept/revise/reject?

Keep this concise — the detailed issues speak for themselves.

## Step 5: Determine Recommendation

Based on the aggregated issues:

### Accept
- No critical issues
- Few or no major issues
- **This is rare** — most manuscripts need revision

### Revise (Reject with Recommendation for Revision)
- Critical or major issues exist
- Issues appear fixable within approximately 6 weeks
- The fundamental research is sound

### Reject
- Fundamental flaws that cannot be reasonably fixed
- Research question is invalid
- Methodology is fundamentally inappropriate
- Conclusions are not supported and cannot be salvaged

## Step 6: Write Review File

Generate the review markdown file.

### Filename

```
manuscript_path = "path/to/study-results.pdf"
review_path = "path/to/study-results-review.md"
```

Strip the extension and append `-review.md`.

### Output Template

```markdown
# Manuscript Review

**Manuscript**: [title or filename]
**Review Date**: [YYYY-MM-DD]
**Recommendation**: [Accept | Revise | Reject]

---

## Overall Assessment

[3-5 sentence paragraph: 2-3 strengths, 2-3 weaknesses, rationale for recommendation]

---

## Issues

### 1. [Issue Title]

**Severity**: Critical | Major | Minor
**Category**: Statistical | Academic

**Problem**:
[Detailed explanation of the issue so authors understand what's wrong and why it matters]

**Recommendation**:
[Specific actionable guidance on how to address this issue, if applicable. If the issue cannot be fixed, explain why.]

---

### 2. [Next Issue]

[Continue for all issues...]

---

## Summary

| Severity | Count |
|----------|-------|
| Critical | [n] |
| Major | [n] |
| Minor | [n] |
| **Total** | [n] |
```

## Output

- Save review to `{manuscript-name}-review.md` in same directory as input
- Return summary to user:
  - Recommendation: [Accept/Revise/Reject]
  - Critical issues: [n]
  - Major issues: [n]
  - Minor issues: [n]
  - Review saved to: [path]
