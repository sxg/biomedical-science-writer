---
name: results-interpreter
description: Analyze CSV data files and interpret figures to generate the Results section. Fifth step of biomedical-science-writer workflow. Requires scope.md, data/ folder with CSVs, and figures/ folder with images.
---

# Results Interpreter

Analyzes data files and interprets figures to generate structured notes and draft the Results section with proper statistical reporting.

## Prerequisites

- `scope.md` must exist
- `notes/code-analysis.md` should exist (provides context on what analyses were run)
- `data/` folder with CSV/Excel files
- `figures/` folder with PNG/JPG images

## Workflow

```
[Read scope.md and code-analysis.md]
     │
     ▼
[Analyze Data Files] ─── Parse CSVs, extract statistics
     │
     ▼
[Interpret Figures] ─── View and describe each figure
     │
     ▼
[Cross-Reference] ─── Match data to figures to findings
     │
     ▼
[Generate notes/data-analysis.md]
     │
     ▼
[Draft Results] ─── drafts/results.md
```

## Step 1: Read Context

From `scope.md`:
- Key findings (what results are expected)
- Research question (what to emphasize)

From `notes/code-analysis.md`:
- Statistical tests used (how to interpret p-values)
- Variables analyzed (what columns to look for)

## Step 2: Analyze Data Files

### 2a. Inventory Data Files

```bash
ls -la data/*.csv data/*.xlsx 2>/dev/null
```

### 2b. Parse Each Data File

For each CSV:

```python
import pandas as pd
df = pd.read_csv('data/results.csv')
print(df.shape)
print(df.columns.tolist())
print(df.describe())
print(df.head())
```

### 2c. Identify Result Types

Scan columns for statistical output patterns:

| Column Pattern | Result Type | How to Report |
|----------------|-------------|---------------|
| `mean`, `sd`, `se` | Descriptive | mean ± SD |
| `p`, `pval`, `p_value` | Significance | p = 0.XXX |
| `ci_lower`, `ci_upper`, `ci_95_*` | Confidence interval | (95% CI: X–Y) |
| `auc`, `auroc` | Discrimination | AUC = 0.XX |
| `accuracy`, `precision`, `recall`, `f1` | Classification | metric = 0.XX |
| `coef`, `beta`, `or`, `hr` | Effect size | OR = X.X, β = X.X |
| `n`, `count` | Sample size | (n = X) |
| `r`, `rho`, `correlation` | Correlation | r = 0.XX |
| `t`, `t_stat` | T-statistic | t = X.XX |
| `chi2`, `chi_square` | Chi-square | χ² = X.XX |

### 2d. Extract Key Statistics

For each data file, extract:

**Demographics/Baseline (if present):**
- Sample sizes per group
- Age, sex distributions
- Key baseline characteristics

**Primary Outcomes:**
- Main comparison statistics
- Effect sizes
- P-values and CIs

**Secondary Outcomes:**
- Supporting statistics
- Subgroup analyses

## Step 3: Interpret Figures

### 3a. View Each Figure

```bash
ls figures/*.png figures/*.jpg figures/*.svg
```

For each figure, examine the image and identify:

### 3b. Figure Type Recognition

| Figure Type | Visual Cues | What to Report |
|-------------|-------------|----------------|
| Box plot | Boxes with whiskers | Medians, IQRs, group differences |
| Violin plot | Symmetric distributions | Distribution shape, medians |
| Bar chart | Rectangular bars | Proportions, counts |
| Scatter plot | Points in 2D | Correlation, trend, outliers |
| ROC curve | Curved line with diagonal | AUC, sensitivity/specificity |
| Kaplan-Meier | Step function | Survival rates, median survival |
| Heatmap | Color grid | Correlation patterns, clusters |
| Forest plot | Points with CIs | Effect sizes across subgroups |
| Confusion matrix | 2x2 or NxN grid | TP, FP, TN, FN rates |

### 3c. Match Figures to Data

Cross-reference figures with data files:
- Which CSV produced which figure?
- Do the statistics match?
- Any discrepancies to flag?

## Step 4: Generate Data Analysis Notes

Create `notes/data-analysis.md`:

```markdown
# Data Analysis Notes

**Analyzed**: [timestamp]

## Data Files Processed

| File | Rows | Columns | Contents |
|------|------|---------|----------|
| results.csv | 156 | 12 | Main statistical results |
| demographics.csv | 156 | 8 | Patient characteristics |

## Study Population

- **Total enrolled**: [n]
- **Excluded**: [n] ([reasons])
- **Final cohort**: [n]

### Demographics

| Characteristic | Overall (n=X) | Group A (n=X) | Group B (n=X) | p-value |
|----------------|---------------|---------------|---------------|---------|
| Age, mean ± SD | XX ± XX | XX ± XX | XX ± XX | X.XXX |
| Male, n (%) | XX (XX%) | XX (XX%) | XX (XX%) | X.XXX |

## Primary Outcome

**Finding**: [description]

| Metric | Group A | Group B | Difference | p-value |
|--------|---------|---------|------------|---------|
| [outcome] | XX ± XX | XX ± XX | XX (95% CI: XX–XX) | X.XXX |

**Interpretation**: [what this means]

## Secondary Outcomes

### [Outcome 2]
[Statistics and interpretation]

### [Outcome 3]
[Statistics and interpretation]

## Figures

### Figure 1: [Title]
- **File**: figures/figure1.png
- **Type**: [box plot, ROC curve, etc.]
- **Shows**: [description]
- **Key observation**: [main takeaway]

### Figure 2: [Title]
- **File**: figures/figure2.png
- **Type**: [type]
- **Shows**: [description]
- **Key observation**: [takeaway]

## Statistical Summary

| Analysis | Test | Statistic | p-value | Interpretation |
|----------|------|-----------|---------|----------------|
| Primary | t-test | t = X.XX | 0.XXX | Significant/NS |
| Secondary | Mann-Whitney | U = XXX | 0.XXX | Significant/NS |

## Consistency Check

- [x] Demographics match reported sample size
- [x] Statistics align with scope.md key findings
- [x] Figures match data file values
- [ ] [Any discrepancies noted]
```

## Step 5: Draft Results Section

Create `drafts/results.md`:

```markdown
# Results

## Study Population

[First paragraph ALWAYS describes the cohort]

A total of [n] patients were included in the analysis. [Exclusions if any]. The final cohort consisted of [n] patients (mean age XX ± XX years, XX% male). Baseline characteristics are summarized in Table 1.

## Primary Outcome

[Main finding with full statistics]

[Outcome] was significantly [higher/lower] in [Group A] compared to [Group B] (XX ± XX vs XX ± XX, p = X.XXX; Figure 1). The mean difference was XX (95% CI: XX–XX).

## Secondary Outcomes

### [Outcome 2]

[Description with statistics]

### [Outcome 3]

[Description with statistics]

## Model Performance (if applicable)

The [model type] achieved an AUC of X.XX (95% CI: X.XX–X.XX) for [classification task] (Figure X). At the optimal threshold, sensitivity was XX% and specificity was XX%.

## Subgroup Analyses (if applicable)

[Subgroup findings]

---

## Figure Legends

**Figure 1.** [Title]. [Description of what is shown]. [Statistical annotation explanation if needed]. Abbreviations: [list].

**Figure 2.** [Title]. [Description].

## Tables

**Table 1.** Baseline characteristics of the study population.

| Characteristic | Overall (n=X) | Group A (n=X) | Group B (n=X) | p-value |
|----------------|---------------|---------------|---------------|---------|
| Age, years | | | | |
| Male sex, n (%) | | | | |

---

## Results Checklist

- [ ] Study population described first
- [ ] Primary outcome reported with full statistics
- [ ] All p-values formatted correctly (p = 0.XXX or p < 0.001)
- [ ] Confidence intervals included for key findings
- [ ] All figures referenced in text
- [ ] All tables referenced in text
- [ ] No interpretation (saved for Discussion)
```

## Statistical Reporting Guidelines

### P-values
- p = 0.043 (not p = .043)
- p < 0.001 (not p = 0.000)
- p = 0.12 (report exact, even if NS)

### Confidence Intervals
- 95% CI: 1.23–4.56 (en-dash, not hyphen)
- Include for all key effect sizes

### Descriptive Statistics
- Normal: mean ± SD
- Non-normal: median (IQR) or median (Q1–Q3)
- Categorical: n (%)

### Decimal Places
- Percentages: 1 decimal (62.5%)
- P-values: 3 decimals or < 0.001
- Correlations: 2 decimals (r = 0.45)
- AUC: 2 decimals (0.87)

## Output

Save to:
- `notes/data-analysis.md` - Detailed analysis
- `drafts/results.md` - Results section draft

Return to parent skill with summary:
- Data files analyzed: [n]
- Figures interpreted: [n]
- Primary finding confirmed: [yes/no]
- Results word count: [n]
