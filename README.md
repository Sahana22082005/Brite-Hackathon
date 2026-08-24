# Brite Hackathon — Dirty Data, Real Decisions

## Project Overview

This project analyzes historical case-management data covering **2023–2025** to understand changes in case closure times and identify operational factors associated with those changes.

The organization observed that case closure times had changed, but the reason for the change was unclear. The analysis therefore focuses on producing **transparent, reproducible, and evidence-based conclusions** while clearly distinguishing between what the available data can and cannot establish.

A supplementary export extracted on **14 January 2026** was also provided. This export was reconciled with the original dataset before producing the final operational findings.

---

## Objectives

The analysis addresses three main operational questions:

### Q1 — Have closure times increased?

Determine whether case closure times increased between **2023 and 2025**, and quantify the change.

### Q2 — What is driving the change?

Identify operational groups associated with changes in closure time, including:

* District
* Case category
* Priority
* Year
* Caseworker-related patterns

### Q3 — Did the 2024 high-priority triage process reduce closure times?

Assess whether the available data can establish the impact of the high-priority triage process introduced during 2024.

---

## Dataset

The project uses two case-management exports:

* `case-export-2023-2025.csv` — Original historical export
* `case-export-supplementary.csv` — Supplementary export extracted on 14 January 2026

The original dataset is preserved unchanged. All cleaning and transformations are performed on derived copies.

### Main Fields

| Field           | Description                       |
| --------------- | --------------------------------- |
| `case_id`       | Unique case identifier            |
| `client_ref`    | Client reference                  |
| `district`      | District associated with the case |
| `intake_date`   | Date the case was received        |
| `closure_date`  | Date the case was closed          |
| `status`        | Current case status               |
| `category`      | Case category/type                |
| `priority`      | Assigned case priority            |
| `caseworker_id` | Assigned caseworker               |
| `contact_count` | Number of recorded contacts       |

---

# Methodology

The analysis follows a documented data-quality and reconciliation pipeline.

```text
Raw Data
   ↓
Initial Data Quality Assessment
   ↓
Duplicate Detection
   ↓
Date Validation
   ↓
Missing-Value Assessment
   ↓
Category / Priority Standardization
   ↓
Closure Duration Calculation
   ↓
Invalid Record Exclusion
   ↓
Supplementary Export Reconciliation
   ↓
Q1: Yearly Closure-Time Analysis
   ↓
Q2: Operational Driver Analysis
   ↓
Q3: High-Priority Triage Analysis
   ↓
Recommendations & Limitations
```

---

# Data Quality Assessment

Several data-quality issues were investigated before conducting the operational analysis.

### Duplicate Records

Case IDs were normalized before duplicate detection by:

* Converting IDs to uppercase
* Removing non-alphanumeric characters
* Comparing normalized identifiers

Exact duplicate rows were also checked separately.

### Date Validation

`intake_date` and `closure_date` were converted to datetime values.

Records with:

* Unparseable dates
* Missing dates required for duration calculation
* Negative closure durations

were excluded from the closure-time analysis where appropriate.

### Closure Duration

Closure duration was calculated as:

```text
Closure Duration = Closure Date − Intake Date
```

Only cases with valid intake and closure dates and non-negative durations were included in the main closure-time analysis.

### Outlier Assessment

Potential closure-time outliers were identified using the IQR method:

```text
Upper Limit = Q3 + 1.5 × IQR
```

Potential outliers were flagged for investigation rather than automatically removed.

This prevents legitimate long-running cases from being incorrectly discarded.

---

# Supplementary Export Reconciliation

The supplementary export used different field names, so its fields were mapped to the original structure.

Examples:

| Supplementary Field | Standardized Field |
| ------------------- | ------------------ |
| `reference`         | `case_id`          |
| `office`            | `district`         |
| `opened`            | `intake_date`      |
| `closed`            | `closure_date`     |
| `case_type`         | `category`         |
| `band`              | `priority`         |
| `worker`            | `caseworker_id`    |

Case IDs were normalized before matching records between the two exports.

### Reconciliation Rules

The supplementary export was treated as a **corroborating source**, rather than automatically replacing the original data.

For overlapping records:

* Matching values increase confidence in the record.
* Conflicting values are retained and flagged.
* Original values are not silently overwritten.
* Supplementary-only records are included only after applying the same data-quality rules.

This approach avoids introducing undocumented assumptions into the analysis.

---

# Q1 — Have Closure Times Increased?

After reconciliation, the median closure time showed a consistent upward trend:

| Year | Median Closure Time |
| ---- | ------------------: |
| 2023 |             34 days |
| 2024 |             37 days |
| 2025 |             39 days |

### Finding

Median case closure time increased from:

**34 days → 39 days**

between 2023 and 2025.

That represents an increase of:

* **5 days**
* **14.71%**

The increase was observed consistently across the yearly medians, indicating an upward trend in closure time.

### Conclusion

> **Case closure times increased between 2023 and 2025.**

The Q1 conclusion is considered **high confidence**, subject to the documented data-quality exclusions.

---

# Q2 — What Is Driving the Change?

The analysis compared closure times across operational dimensions including:

* District
* Category
* Priority
* Year

## District-Level Finding

The strongest district-level change was observed in **Weybridge**.

Median closure time increased from:

**36 days in 2023 → 68 days in 2025**

This represents an increase of:

**32 days**

Weybridge therefore represents the strongest signal for further operational investigation.

### Important Interpretation

The analysis identifies an **association**, not causation.

The data does not prove that being in Weybridge caused the increase in closure time.

Possible explanations that require further investigation include:

* Changes in workload
* Changes in case mix
* Staffing levels
* Process changes
* Operational capacity
* Changes in complexity of cases

---

## Category Analysis

Category-level analysis was less conclusive because a substantial number of records could not be confidently mapped to the controlled category vocabulary.

Rather than assigning unsupported categories, these records were labelled:

```text
Unmapped
```

This preserves transparency and prevents artificial conclusions from being introduced through aggressive data cleaning.

---

## Priority Analysis

Priority-level comparisons were also limited because some priority groups did not have sufficient observations across both 2023 and 2025.

Therefore, priority should not be treated as a definitive explanation for the overall increase in closure time.

---

# 🚦 Q3 — Did the 2024 High-Priority Triage Reduce Closure Times?

High-priority cases showed:

| Year | Median Closure Time |
| ---- | ------------------: |
| 2024 |             36 days |
| 2025 |             40 days |

This represents an increase of:

**4 days**

However, this comparison does **not** establish whether the 2024 triage process caused or reduced closure times.

### Why?

The dataset does not contain a reliable indicator identifying:

* Which cases actually received the triage intervention
* When the intervention occurred
* Which high-priority cases were not exposed to the intervention

Additionally, a reliable **2023 high-priority baseline** is not available.

Therefore:

> **The available data cannot reliably determine whether the 2024 high-priority triage process reduced closure times.**

The 2024–2025 difference should not be interpreted as a causal effect of the triage process.

---

# Confidence Summary

| Question | Finding                                                                                  | Confidence   |
| -------- | ---------------------------------------------------------------------------------------- | ------------ |
| Q1       | Median closure time increased from 34 days in 2023 to 39 days in 2025 (+5 days, +14.71%) | **High**     |
| Q2       | Weybridge had the largest district-level increase, from 36 to 68 days (+32 days)         | **Moderate** |
| Q3       | The effect of the 2024 triage process cannot be reliably determined                      | **High**     |

---

# Key Recommendations

## 1. Investigate Weybridge

Weybridge should be prioritized for operational investigation because it experienced the largest observed increase in median closure time.

The investigation should examine:

* Workload
* Staffing
* Case complexity
* Case mix
* Process changes
* Resource constraints

---

## 2. Improve Priority Data Quality

Priority should be captured as a:

* Mandatory field
* Controlled vocabulary
* Consistently maintained attribute

This will improve future high-priority performance analysis.

---

## 3. Strengthen Case and Category Controls

The case-management system should implement stronger controls around:

* Case IDs
* Duplicate detection
* Category values
* Missing values
* Field validation

This will reduce the amount of manual cleaning required for future analysis.

---

## 4. Record Triage Participation Explicitly

Future systems should record:

```text
triage_received
triage_timestamp
triage_type
```

This would allow analysts to distinguish between cases that received the intervention and those that did not.

It would also make future causal or quasi-experimental evaluation of the triage process possible.

---

# Limitations

The analysis has several important limitations.

### 1. Observational Data

The dataset is observational and does not support strong causal conclusions.

### 2. Missing Intervention Indicator

There is no reliable field identifying cases that received the 2024 triage intervention.

### 3. Missing Data

Missing priority and other fields reduce the reliability of some subgroup comparisons.

### 4. Category Quality

A substantial number of categories remain `Unmapped`, limiting category-level conclusions.

### 5. Historical Data

The available exports do not contain all potentially relevant operational variables, such as staffing, workload, or case complexity measures.

### 6. Association ≠ Causation

Observed relationships should be interpreted as operational signals requiring further investigation rather than proof of causal relationships.

---

# Suggested Repository Structure

```text
brite-hackathon/
│
├── README.md
│
├── data/
│   ├── case-export-2023-2025.csv
│   └── case-export-supplementary.csv
│
├── notebooks/
│   └── analysis.ipynb
│
├── outputs/
│   ├── charts/
│   ├── tables/
│   └── cleaning_log.csv
│
├── DECISIONS.md
│
└── requirements.txt
```

> **Note:** If the datasets contain confidential or sensitive information, do not commit the raw CSV files to a public GitHub repository. Add them to `.gitignore` and provide instructions for obtaining them separately.

---

# Technologies Used

* **Python**
* **Pandas**
* **NumPy**
* **Matplotlib**
* **Jupyter Notebook**
* **Git / GitHub**

---

# How to Run

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd brite-hackathon
```

### 2. Install dependencies

```bash
pip install pandas numpy matplotlib jupyter
```

Or, if a `requirements.txt` file is provided:

```bash
pip install -r requirements.txt
```

### 3. Add the datasets

Place the required CSV files in the appropriate `data/` directory.

### 4. Run the notebook

```bash
jupyter notebook
```

Open:

```text
notebooks/analysis.ipynb
```

and run the cells sequentially.

---

# Reproducibility

The analysis prioritizes reproducibility through:

* Preservation of the raw dataset
* Derived cleaning datasets
* Documented cleaning decisions
* Explicit reconciliation rules
* Normalized case-ID matching
* Explicit exclusion criteria
* Separate analysis of the supplementary export
* Transparent reporting of limitations

The project intentionally avoids silently correcting or overwriting source data.

---

# Final Conclusion

The analysis provides strong evidence that **case closure times increased between 2023 and 2025**, with median closure time increasing from **34 to 39 days**.

The strongest operational signal is the increase observed in **Weybridge**, where median closure time increased by **32 days** between 2023 and 2025.

However, the available data is insufficient to establish whether the **2024 high-priority triage process caused a reduction in closure times**. The absence of a reliable intervention indicator and an appropriate pre-intervention baseline prevents a causal conclusion.

The most important next step is therefore not simply to build a more complex predictive model, but to **improve operational data capture**, particularly around triage participation, priority, categories, and case-level process information.

> **The key lesson from this analysis is that better decisions require not only more data, but better-structured and more reliable data.**
