---
name: review
description: Review and critique a scientific manuscript, identifying critical issues with recommendations
arguments:
  - name: manuscript_path
    description: Path to the manuscript file (PDF or Markdown)
    required: true
---

# Review Manuscript

Review the scientific manuscript at the provided path.

## Usage

```
/reviewer:review path/to/manuscript.pdf
/reviewer:review path/to/manuscript.md
```

## What This Does

1. Reads the manuscript (PDF or Markdown)
2. Runs statistical review (methodology, statistics, results reporting)
3. Runs academic review (significance, validity, conclusions)
4. Combines findings into a ranked list of issues
5. Generates overall assessment with accept/revise/reject recommendation
6. Saves review as `{manuscript-name}-review.md`

## Invoke Skill

Use the `reviewer:review` skill with the manuscript path:

```
Skill: reviewer:review
Args: {manuscript_path}
```
