---
name: code-analyzer
description: Analyze GitHub repository to extract methodology and generate the Methods section. Fourth step of biomedical-science-writer workflow. Requires scope.md and cloned code/ repository.
---

# Code Analyzer

Analyzes the cloned GitHub repository to understand the computational methodology, then generates structured notes and drafts the Methods section.

## Critical Principle: No Silent Assumptions

**NEVER assume what the code does without user confirmation.**

Code can be complex and context-dependent. When uncertain about methodology:

### When to Pause and Ask

1. **Unclear analysis purpose**
   - What is this script/notebook trying to accomplish?
   - Is this the main analysis or a side experiment?

2. **Ambiguous parameters**
   - Hardcoded values without explanation (what does `threshold = 0.7` mean?)
   - Configuration options that affect results

3. **Multiple analysis paths**
   - Which branch/version of the code was used for final results?
   - Are there deprecated scripts that shouldn't be documented?

4. **Statistical method uncertainty**
   - Is this the correct interpretation of the test being performed?
   - What assumptions were made (normality, independence, etc.)?

5. **Missing context**
   - What does variable `X` represent in domain terms?
   - Why was this specific approach chosen over alternatives?

### How to Ask

```
I have questions about the code before writing the Methods section:

**File**: analysis.ipynb, Cell 15

**Question**: I see `model = RandomForestClassifier(n_estimators=100, max_depth=5)`.
- Were these hyperparameters tuned, or are they defaults?
- If tuned, what was the tuning method (grid search, random search)?
- Should I report these specific values in the Methods?

**Why this matters**: Reviewers often ask about hyperparameter selection.
```

### Document All Clarifications

Log clarifications in `notes/code-analysis.md`:

```markdown
## Clarifications Received

| File | Question | User Response |
|------|----------|---------------|
| analysis.ipynb | Hyperparameter tuning? | Grid search with 5-fold CV |
| preprocess.py | Why z-score normalization? | Standard for this imaging modality |
```

## Prerequisites

- `scope.md` must exist
- `notes/irb-summary.md` may exist (provides approved procedures and endpoints for cross-reference)
- `notes/irb-scope-comparison.md` may exist (clarifies what was actually implemented vs. approved)
- `code/` directory with cloned repository (from context-ingestion)
- If code/ doesn't exist, this step produces limited output based on scope.md alone

## Workflow

```
[Read scope.md and notes/irb-summary.md for context]
     │
     ▼
[Scan Repository Structure]
     │
     ▼
[Identify Key Files] ─── Notebooks, scripts, configs
     │
     ▼
[CHECKPOINT: Clarify Code Purpose] ─── Ask about unclear scripts/params
     │
     ▼
[Analyze Code Flow] ─── Data loading → Processing → Analysis → Output
     │
     ▼
[CHECKPOINT: Verify Methodology] ─── Confirm interpretation with user
     │
     ▼
[IRB CROSS-REFERENCE] ─── Compare code procedures vs. IRB-approved procedures
     │
     ▼
[Extract Methodology] ─── Generate notes/code-analysis.md
     │
     ▼
[BIOSTATISTICIAN REVIEW] ─── Validate statistical methods
     │                        └── agents/biostatistician.md
     ▼
[Draft Methods] ─── drafts/methods.md (with statistical sign-off)
```

## Step 1: Scan Repository Structure

```bash
# Get repository overview
find code/ -type f -name "*.py" -o -name "*.ipynb" -o -name "*.R" | head -30

# Check for dependency files
ls code/requirements.txt code/environment.yml code/setup.py 2>/dev/null

# Check for README
cat code/README.md 2>/dev/null | head -50
```

Identify:
- Primary language (Python, R, MATLAB, etc.)
- Project structure (flat, src/, notebooks/, etc.)
- Entry points (main scripts, notebooks)
- Configuration files

## Step 2: Identify Key Files

Prioritize analysis of:

### Jupyter Notebooks (.ipynb)
Most important - usually contain the full analysis workflow.

```bash
ls code/*.ipynb code/**/*.ipynb 2>/dev/null
```

### Main Analysis Scripts
```bash
ls code/main.py code/analysis.py code/run*.py 2>/dev/null
```

### Data Processing
```bash
ls code/*preprocess* code/*clean* code/*load* 2>/dev/null
```

### Model/Statistical Files
```bash
ls code/*model* code/*train* code/*stat* 2>/dev/null
```

## Step 3: Analyze Code Flow

For each key file, trace the methodology:

### 3a. Data Loading

Look for patterns:
```python
# Python patterns
pd.read_csv(...)
pd.read_excel(...)
nibabel.load(...)      # Neuroimaging
pydicom.dcmread(...)   # DICOM
SimpleITK.ReadImage(...)
```

Extract:
- Data sources (file types, databases)
- Data formats
- Initial data shape/size

### 3b. Preprocessing

Look for patterns:
```python
# Cleaning
df.dropna(...)
df.fillna(...)

# Transformation
StandardScaler()
normalize(...)
resample(...)

# Feature engineering
df['new_col'] = ...
```

Extract:
- Missing data handling
- Normalization/standardization
- Feature creation
- Exclusion criteria applied in code

### 3c. Statistical Analysis

Look for patterns:
```python
# Hypothesis tests
scipy.stats.ttest_ind(...)
scipy.stats.mannwhitneyu(...)
scipy.stats.pearsonr(...)
scipy.stats.spearmanr(...)

# Regression
statsmodels.api.OLS(...)
statsmodels.api.Logit(...)

# Multiple comparison correction
statsmodels.stats.multitest.multipletests(...)
```

Extract:
- Statistical tests used
- Significance thresholds
- Multiple comparison corrections

### 3d. Machine Learning (if applicable)

Look for patterns:
```python
# Splitting
train_test_split(..., test_size=0.2, random_state=42)
cross_val_score(...)
StratifiedKFold(...)

# Models
RandomForestClassifier(...)
LogisticRegression(...)
XGBClassifier(...)

# Evaluation
accuracy_score(...)
roc_auc_score(...)
confusion_matrix(...)
```

Extract:
- Model type and parameters
- Train/test split ratios
- Cross-validation strategy
- Performance metrics

### 3e. Dependencies and Versions

```bash
cat code/requirements.txt 2>/dev/null
```

Or extract from imports:
```python
import pandas as pd
print(pd.__version__)
```

## Step 4: Generate Code Analysis Notes

Create `notes/code-analysis.md`:

```markdown
# Code Analysis

**Repository**: [GitHub URL]
**Analyzed**: [timestamp]
**Primary Language**: Python [version]

## Repository Structure

```
code/
├── analysis.ipynb      # Main analysis
├── preprocessing.py    # Data cleaning
├── models.py          # ML models
└── requirements.txt   # Dependencies
```

## Data Pipeline

### 1. Data Loading
- **Source**: CSV files from [source]
- **Format**: Tabular data with [n] columns
- **Initial Size**: [n] rows

### 2. Preprocessing
- Missing data: [handling approach]
- Normalization: [method]
- Exclusions: [criteria]

### 3. Analysis Approach

#### Statistical Tests
| Test | Purpose | Parameters |
|------|---------|------------|
| Independent t-test | Group comparison | α = 0.05 |
| Pearson correlation | Association | |
| Mann-Whitney U | Non-parametric comparison | |

#### Machine Learning (if applicable)
- **Model**: [type]
- **Split**: [ratio] train/test
- **Cross-validation**: [k]-fold
- **Hyperparameters**: [key params]
- **Metrics**: [accuracy, AUC, etc.]

### 4. Output Generation
- Figures saved to: [location]
- Results saved to: [location]

## Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| pandas | 2.0.3 | Data manipulation |
| scikit-learn | 1.3.0 | ML models |
| scipy | 1.11.1 | Statistical tests |
| matplotlib | 3.7.2 | Visualization |

## Key Code Snippets

### Statistical Test Implementation
```python
[relevant code snippet]
```

### Model Training
```python
[relevant code snippet]
```

## Notes for Methods Section

- [Key methodological detail 1]
- [Key methodological detail 2]
- [Any unusual or noteworthy approaches]
```

## Step 4b: IRB Cross-Reference (If IRB Exists)

**Skip this step if `notes/irb-summary.md` does not exist.**

Compare the procedures identified in code with the IRB-approved procedures to ensure consistency and identify any discrepancies.

### Cross-Reference Table

Create a comparison in `notes/code-analysis.md`:

```markdown
## IRB Procedure Cross-Reference

| IRB-Approved Procedure | Implemented in Code? | Code Location | Notes |
|------------------------|---------------------|---------------|-------|
| [procedure from IRB] | ✓/✗ | analysis.ipynb | [matches/differs] |
| [procedure from IRB] | ✓/✗ | [file] | [notes] |

| Code Procedure | In IRB? | Notes |
|----------------|---------|-------|
| [procedure from code] | ✓/✗ | [expected/unexpected] |
```

### What to Check

1. **Endpoints match**: Are the outcomes analyzed in code the same as IRB endpoints?
2. **Population criteria**: Does data filtering match IRB inclusion/exclusion?
3. **Statistical methods**: Are the planned analyses from IRB reflected in code?
4. **Sample size**: Does actual N align with IRB-justified sample size?

### If Discrepancies Found

Reference `notes/irb-scope-comparison.md` to understand if differences were already documented during scoping. If not:

1. **Document the discrepancy** in code-analysis.md
2. **Ask user for clarification** before proceeding
3. **Note implications** for Methods section wording

```
I found a discrepancy between the IRB and implemented code:

**IRB**: Primary endpoint is change in HbA1c at 6 months
**Code**: Analyzes HbA1c at 3 months and 6 months

Was this intentional? Should the Methods describe both timepoints,
or is one the primary and one exploratory?
```

## Step 5: Biostatistician Review

**Before drafting Methods, invoke the biostatistician agent for statistical review.**

Read `agents/biostatistician.md` and execute Phase 1 (Code Analysis Review):

1. **Create statistical methods inventory** from code analysis
2. **Check assumptions** for each test identified
3. **Verify test appropriateness** for data types
4. **Assess multiple comparisons** handling
5. **Evaluate sample size** adequacy

### Biostatistician Checkpoint

The biostatistician must confirm:

```markdown
## Statistical Methods Review

| Method | Appropriate? | Assumptions Checked? | Recommendation |
|--------|--------------|---------------------|----------------|
| [test 1] | ✓/✗ | ✓/✗ | [approve/revise] |
| [test 2] | ✓/✗ | ✓/✗ | [approve/revise] |

**Statistical Sign-Off**: [ ] Approved for Methods drafting
```

**Do NOT proceed to Methods draft until biostatistician approves.**

If issues identified:
1. Document concerns in `notes/code-analysis.md`
2. Flag for user attention
3. Suggest alternative methods if appropriate

---

## Step 6: Draft Methods Section

**Only proceed after biostatistician sign-off from Step 5.**

Create `drafts/methods.md`:

```markdown
# Methods

## Study Design and Population

[From scope.md - describe study design]

[From code analysis - describe data source and selection]

## Data Acquisition

[If applicable - describe how raw data was obtained]

## Data Preprocessing

[From code analysis - describe cleaning, normalization, feature engineering]

Data preprocessing was performed using Python [version]. [Describe steps in scientific prose.]

## Statistical Analysis

[From code analysis - describe all statistical approaches]

[Test] was used to [purpose]. Statistical significance was set at p < [threshold]. [Multiple comparison correction] was applied for [reason].

## Machine Learning Analysis (if applicable)

[From code analysis - describe ML pipeline]

A [model type] was trained using [split] of the data. [Cross-validation strategy] was employed to assess model generalization. Model performance was evaluated using [metrics].

## Software

Statistical analysis was performed using Python [version] with [packages]. [Additional software if relevant.]

---

## Methods Checklist

- [ ] Study design clearly stated
- [ ] Population/sample described
- [ ] All preprocessing steps documented
- [ ] Statistical tests specified with parameters
- [ ] Significance threshold stated
- [ ] Software and versions listed
- [ ] Reproducibility considerations addressed
```

## Output

Save to:
- `notes/code-analysis.md` - Detailed analysis (includes statistical review)
- `drafts/methods.md` - Methods section draft (with biostatistician approval)

Return to parent skill with summary:
- Files analyzed: [n]
- Statistical tests identified: [list]
- ML approach: [yes/no, type]
- Biostatistician status: [approved / revisions needed]
- Statistical issues: [list if any]
- Methods word count: [n]
