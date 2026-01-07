# Reviewer Plugin

Review and critique scientific manuscripts with detailed feedback and actionable recommendations.

## Features

- **Dual-agent review**: Statistical reviewer for methodology and academic reviewer for significance
- **Ranked issues**: Problems ordered by severity (Critical → Major → Minor)
- **Detailed explanations**: Each issue explained so authors understand the problem
- **Actionable recommendations**: Specific guidance on how to fix each issue
- **Overall assessment**: Strengths, weaknesses, and accept/revise/reject recommendation

## Usage

```
/reviewer:review path/to/manuscript.pdf
/reviewer:review path/to/manuscript.md
```

## Output

Creates `{manuscript-name}-review.md` in the same directory as the input file.

### Output Structure

```markdown
# Manuscript Review

**Manuscript**: [title]
**Review Date**: [date]
**Recommendation**: Accept | Revise | Reject

## Overall Assessment
[Strengths, weaknesses, rationale]

## Issues

### 1. [Issue Title]
**Severity**: Critical | Major | Minor
**Category**: Statistical | Academic
**Problem**: [Detailed explanation]
**Recommendation**: [How to fix]

...
```

## Agents

### Statistical Reviewer

Evaluates:
- Appropriateness of statistical methods
- Correct execution (sample size, test selection, assumptions)
- Complete reporting (effect sizes, CIs, p-values)
- Validity of statistical conclusions
- Signs of p-hacking or selective reporting

### Academic Reviewer

Evaluates:
- Research question validity and clarity
- Study design alignment with question
- Conclusions supported by data
- Novelty and significance to the field
- Scholarly quality and ethics

## Recommendations

- **Accept**: No critical issues, few/no major issues (rare)
- **Revise**: Issues exist but appear fixable within ~6 weeks
- **Reject**: Fundamental flaws that cannot be reasonably fixed
