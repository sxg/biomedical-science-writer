---
name: code-analyzer
description: Analyze GitHub repository to extract methodology and generate the Methods section. Fourth step of biomedical-science-writer workflow. Requires scope.md and cloned code/ repository.
---

# Code Analyzer

Analyzes the cloned GitHub repository to understand the computational methodology, then generates structured notes and drafts the Methods section.

## Prerequisites

- `scope.md` must exist
- `code/` directory with cloned repository (from context-ingestion)
- If code/ doesn't exist, this step produces limited output based on scope.md alone

## Workflow

```
[Read scope.md for context]
     │
     ▼
[Scan Repository Structure]
     │
     ▼
[Identify Key Files] ─── Notebooks, scripts, configs
     │
     ▼
[Analyze Code Flow] ─── Data loading → Processing → Analysis → Output
     │
     ▼
[Extract Methodology] ─── Generate notes/code-analysis.md
     │
     ▼
[Draft Methods] ─── drafts/methods.md
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

## Step 5: Draft Methods Section

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
- `notes/code-analysis.md` - Detailed analysis
- `drafts/methods.md` - Methods section draft

Return to parent skill with summary:
- Files analyzed: [n]
- Statistical tests identified: [list]
- ML approach: [yes/no, type]
- Methods word count: [n]
