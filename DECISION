# Reconciliation & Analysis Decisions

## 1. Purpose

This document records the major data-cleaning, reconciliation, and analytical decisions made during the Brite Hackathon analysis.

The goal is to ensure that the analysis is **transparent, reproducible, and auditable**.

The original dataset was preserved unchanged. All transformations were performed on derived copies.

---

# 2. Data Cleaning Decisions

## 2.1 Raw Data Preservation

The original case-management export was not modified.

A separate working dataset was created for:

* Data cleaning
* Transformation
* Validation
* Analysis

This ensures that the original source data remains available for auditing.

---

## 2.2 Case ID Normalization

Case IDs were normalized before duplicate detection.

The normalization process:

1. Converted IDs to uppercase.
2. Removed non-alphanumeric characters.
3. Compared the resulting normalized IDs.

For example:

```text
ab-123 → AB123
AB 123 → AB123
```

This prevents formatting differences from hiding potential duplicate records.

---

## 2.3 Duplicate Handling

Two types of duplicates were investigated:

### Exact duplicate rows

Completely identical rows were removed from the working dataset.

### Duplicate normalized case IDs

Cases with the same normalized `case_id` were investigated.

Where duplicate records represented the same case, the most complete record was retained.

No records were silently merged without first checking the available fields.

---

# 3. Date Handling

`intake_date` and `closure_date` were converted to datetime values.

Invalid or unparseable dates were converted to missing values rather than being manually guessed or replaced.

Closure duration was calculated as:

```text
closure_days = closure_date - intake_date
```

---

## 3.1 Negative Closure Durations

Records where:

```text
closure_date < intake_date
```

were considered logically invalid.

These records were excluded from the main closure-time analysis.

They were not modified to artificially create valid durations.

---

# 4. Open and Closed Cases

Cases without a valid closure date were not used when calculating completed-case closure times.

Open cases were retained in the cleaned dataset but excluded from analyses requiring a completed closure duration.

This prevents incomplete cases from being incorrectly treated as completed cases.

---

# 5. Outlier Handling

Closure-time outliers were identified using the IQR method.

```text
IQR = Q3 - Q1

Upper Limit = Q3 + 1.5 × IQR
```

Potential outliers were **flagged rather than automatically removed**.

The reason is that a long closure duration may represent a genuine operational case rather than a data error.

---

# 6. Category Standardization

Category values were reviewed for inconsistent formatting and known variations.

Only values that could be confidently mapped to a controlled category were standardized.

Records that could not be confidently classified were labelled:

```text
Unmapped
```

They were not assigned a category based on assumptions.

### Reason

Artificially assigning categories could create misleading operational conclusions.

Therefore, category-level findings are treated with caution.

---

# 7. Priority Handling

Priority values were standardized for analysis.

Missing priority values were not inferred from other fields.

Instead, missing or unavailable priority information was retained as unknown where appropriate.

This is particularly important for the high-priority triage analysis.

---

# 8. Supplementary Export Reconciliation

A supplementary export extracted on **14 January 2026** was provided from the same source system.

The supplementary dataset used different field names.

The fields were mapped as follows:

| Supplementary Field | Standard Field  |
| ------------------- | --------------- |
| `reference`         | `case_id`       |
| `office`            | `district`      |
| `opened`            | `intake_date`   |
| `closed`            | `closure_date`  |
| `case_type`         | `category`      |
| `band`              | `priority`      |
| `worker`            | `caseworker_id` |

---

## 8.1 Matching Records

Case IDs were normalized before comparing the two exports.

The following were compared for overlapping records:

* District
* Intake date
* Closure date
* Category
* Priority
* Caseworker

---

## 8.2 Conflicting Values

The supplementary export was **not automatically treated as the correct version** simply because it was newer.

When overlapping records disagreed:

* The disagreement was retained.
* The original value was not silently overwritten.
* The conflict was flagged for investigation.

This avoids introducing unsupported assumptions into the analysis.

---

## 8.3 Supplementary-Only Cases

Cases that appeared only in the supplementary export were considered for inclusion.

They were added only after applying the same data-quality rules used for the original dataset.

This produced the reconciled analysis dataset.

---

# 9. Q1 Decision — Closure-Time Trend

For Question 1, closure time was calculated using cases with:

* Valid intake date
* Valid closure date
* Non-negative closure duration

The **median** was used as the primary measure because closure times can be strongly affected by unusually long-running cases.

The final reconciled result showed:

| Year | Median Closure Time |
| ---- | ------------------: |
| 2023 |             34 days |
| 2024 |             37 days |
| 2025 |             39 days |

Therefore:

```text
2023 → 2025
34 days → 39 days
Change = +5 days
Percentage change = +14.71%
```

### Decision

The data supports the conclusion that median closure time increased between 2023 and 2025.

---

# 10. Q2 Decision — Operational Drivers

Closure times were compared across:

* District
* Category
* Priority
* Year

The strongest district-level increase was observed in **Weybridge**.

```text
2023: 36 days
2025: 68 days
Increase: 32 days
```

### Decision

Weybridge was identified as the strongest operational signal requiring further investigation.

However, the analysis does **not** claim that Weybridge caused the increase.

The observed relationship is treated as an association rather than a causal effect.

---

# 11. Q3 Decision — 2024 High-Priority Triage

High-priority cases showed:

```text
2024 median: 36 days
2025 median: 40 days
Change: +4 days
```

However, the available data does not contain a reliable field identifying:

* Which cases received the triage intervention
* When the intervention occurred
* Which cases did not receive the intervention

A reliable 2023 high-priority baseline is also unavailable.

### Decision

The data **cannot reliably establish whether the 2024 triage process reduced closure times**.

The 2024–2025 difference is therefore not interpreted as a causal measure of triage effectiveness.

---

# 12. What We Did Not Do

The following decisions were deliberately avoided:

* We did not overwrite the raw dataset.
* We did not invent missing values.
* We did not automatically remove all statistical outliers.
* We did not assign unknown categories without evidence.
* We did not assume the newest export was always correct.
* We did not treat associations as causal relationships.
* We did not claim that the triage process caused changes in closure time.
* We did not use missing data as evidence that an intervention did not occur.

---

# 13. What We Would Do Differently

If designing the data pipeline from the beginning, we would include:

1. A source/version identifier for every export.
2. A unique immutable case identifier.
3. Automated duplicate detection.
4. Controlled category and priority values.
5. Mandatory priority capture.
6. Explicit triage participation indicators.
7. Triage timestamps.
8. Case complexity indicators.
9. Workload and staffing information.
10. Automated reconciliation between exports.

These improvements would make future operational analysis more reliable and would allow stronger causal evaluation of process changes.

---

# 14. Final Principle

The analysis follows one central principle:

> **When the data cannot reliably answer a question, the correct analytical decision is to say so rather than manufacture an answer.**

This principle is particularly important for the evaluation of the 2024 high-priority triage process.
