# IRB Document Support Design

## Overview

Add support for user-supplied IRB documents (Word, PDF, or markdown) to provide context for writing Introduction and Methods sections. Includes explicit scoping checkpoint to handle discrepancies between IRB scope and actual research.

## Project Structure

```
project/
├── irb/                    # NEW - IRB and regulatory documents
│   ├── protocol.pdf        # or .docx, .md
│   └── amendments/         # Optional: protocol amendments
├── papers/
├── data/
├── figures/
└── config.md
```

The `irb/` folder is optional. Workflow proceeds without it but logs that no IRB document was provided.

## File Format Handling

| Format | Method |
|--------|--------|
| `.md` | Read directly with Read tool |
| `.pdf` | Claude's native PDF capability |
| `.docx` | Extract text using `document-skills:docx` plugin |

## Output Structure

```
notes/
├── irb-summary.md          # NEW - Extracted IRB content
├── irb-scope-comparison.md # NEW - IRB vs actual scope
├── papers/
└── ...
```

## IRB Content Extraction

Generate `notes/irb-summary.md` with comprehensive extraction:

```markdown
# IRB Document Summary

**Source**: [filename]
**Extracted**: [timestamp]

## Study Identification
- **Protocol Title**: [extracted]
- **IRB Approval Number**: [extracted]
- **Principal Investigator**: [extracted]
- **Approval Date**: [extracted]

## Study Design
- **Study Type**: [interventional/observational/retrospective/etc.]
- **Design**: [RCT, cohort, case-control, cross-sectional, etc.]
- **Duration**: [study period]

## Population
- **Target Population**: [description]
- **Inclusion Criteria**: [bulleted list]
- **Exclusion Criteria**: [bulleted list]
- **Sample Size**: [N with justification if provided]

## Procedures & Interventions
- [Procedure 1]
- [Procedure 2]
- ...

## Endpoints
- **Primary**: [endpoint]
- **Secondary**: [endpoints]

## Statistical Considerations
- **Power Analysis**: [if provided]
- **Planned Analyses**: [if provided]

## Notes
[Any additional relevant context, caveats, or unclear sections]
```

Fields marked `[not found]` if not present in the document.

## Scoping Checkpoint

During Step 2 (Scoping), after user describes their research question:

1. Load `notes/irb-summary.md`
2. Compare user's stated scope against IRB content
3. Present discrepancies and ask for confirmation

### Comparison Prompt

```markdown
## IRB vs Actual Scope Comparison

I've compared your stated research scope with the IRB document.

| Aspect | IRB Document | Your Stated Scope |
|--------|--------------|-------------------|
| Population | [from IRB] | [from user] |
| Sample size | [from IRB] | [from user] |
| Endpoints | [from IRB] | [from user] |
| Procedures | [from IRB] | [from user] |

**Please confirm:**
1. Are these differences intentional? (subset of approved protocol)
2. Any context for the narrower scope?
3. Anything I've misunderstood?
```

User responses captured in `notes/irb-scope-comparison.md` for audit trail.

## Workflow Integration

| Skill | Uses From IRB | Purpose |
|-------|---------------|---------|
| context-ingestion | Raw document | Generate `notes/irb-summary.md` |
| scoping | `irb-summary.md` | Scope comparison checkpoint |
| literature-review | Study objectives, population | Frame Introduction context |
| code-analyzer | Procedures, endpoints | Cross-reference with implemented methods |
| results-interpreter | Sample size, endpoints | Validate reported results align |
| assembler | IRB approval number | Insert ethics statement |

## Handling Missing IRB

- Workflow proceeds normally
- Scoping step notes: "No IRB document provided"
- Ethics statement prompts user for approval number manually

## Implementation Checklist

1. Update `skills/context-ingestion/SKILL.md` to scan `irb/` folder
2. Add IRB extraction logic and `notes/irb-summary.md` template
3. Update `skills/scoping/SKILL.md` with scope comparison checkpoint
4. Update `skills/literature-review/SKILL.md` to consume IRB context
5. Update `skills/code-analyzer/SKILL.md` to cross-reference IRB procedures
6. Update `skills/results-interpreter/SKILL.md` to validate against IRB
7. Update `skills/assembler/SKILL.md` to pull ethics statement
8. Update `skills/main/SKILL.md` with new project structure
