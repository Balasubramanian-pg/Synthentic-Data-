## Rule 18: Document Dataset Assumptions

### Rule Definition

Synthetic healthcare datasets must include **clear documentation of all modeling assumptions used during data generation**. These assumptions describe the structure, scale, and constraints of the dataset so that analysts understand how the data was created and how it should be interpreted.

Because synthetic datasets are not collected from real healthcare systems, they rely on **design decisions** such as population size, number of providers, claim frequency, and coverage patterns. Without documentation, users may incorrectly interpret patterns as real-world observations rather than simulated scenarios.

Documenting assumptions ensures transparency, reproducibility, and easier dataset maintenance.

---

## Example Assumption Documentation

Example dataset summary:

| Attribute                  | Assumption          |
| -------------------------- | ------------------- |
| Total Patients             | 50,000              |
| Number of Physicians       | 1,200               |
| Number of Insurance Payers | 5                   |
| Time Period                | Jan 2022 – Dec 2024 |
| Therapeutic Area           | Oncology            |

This information helps analysts understand the dataset’s scope.

---

## Nuances That Must Be Understood

### 1. Population Size Affects All Metrics

The total number of patients determines the scale of healthcare activity in the dataset.

Example assumption:

| Metric                     | Value  |
| -------------------------- | ------ |
| Total Patients             | 50,000 |
| Average Claims per Patient | 5      |

Digit-by-digit calculation:

50,000
× 5
= **250,000 claims**

If analysts are unaware of this assumption, they may misinterpret the scale of utilization.

---

### 2. Provider Count Influences Prescribing Distribution

The number of physicians affects treatment patterns.

Example assumption:

| Metric     | Value  |
| ---------- | ------ |
| Physicians | 1,200  |
| Patients   | 50,000 |

Average patients per physician calculation:

50,000
÷ 1,200

Step-by-step:

50,000 ÷ 1,200

1,200 × 40 = 48,000
Remaining = 2,000

2,000 ÷ 1,200 ≈ 1.67

Average patients per physician ≈ **41.67**

This assumption shapes provider utilization patterns.

---

### 3. Time Window Defines Analytical Scope

The dataset time period determines the types of analysis that can be performed.

Example time coverage:

| Start Date | End Date |
| ---------- | -------- |
| Jan 2022   | Dec 2024 |

Total duration calculation:

2022
2023
2024

Total = **3 years**

This affects metrics such as:

* Treatment persistence
* Market adoption trends
* Annual claim growth

---

### 4. Therapeutic Area Scope Must Be Defined

Synthetic datasets typically focus on specific disease areas.

Example therapeutic focus:

| Therapeutic Area | Included |
| ---------------- | -------- |
| Oncology         | Yes      |
| Cardiology       | No       |
| Diabetes         | No       |

Without this documentation, analysts might incorrectly assume the dataset represents the entire healthcare system.

---

### 5. Assumptions Enable Dataset Regeneration

Synthetic datasets are often regenerated or expanded.

Documented assumptions allow developers to reproduce the dataset.

Example generation parameters:

| Parameter                               | Value |
| --------------------------------------- | ----- |
| Claim probability per patient per month | 0.25  |
| Therapy switch probability              | 10%   |
| Missing data rate                       | 2%    |

These parameters allow future dataset versions to maintain consistency.

---

## Example Assumption Documentation Template

Dataset Overview

| Parameter      | Value                    |
| -------------- | ------------------------ |
| Dataset Name   | Pharma Claims Simulation |
| Total Patients | 50,000                   |
| Physicians     | 1,200                    |
| Payers         | 5                        |
| Products       | 8                        |
| Time Period    | 2022–2024                |

Data Generation Parameters

| Parameter                  | Value |
| -------------------------- | ----- |
| Average Claims per Patient | 5     |
| Therapy Switching Rate     | 10%   |
| Missing Data Rate          | 2%    |
| Formulary Restriction Rate | 30%   |

---

## Data Quality Validation Checks

Verify patient count assumption:

```sql
SELECT COUNT(DISTINCT patient_id)
FROM patient_dimension;
```

Verify provider count:

```sql
SELECT COUNT(*)
FROM physician_dimension;
```

Verify time coverage:

```sql
SELECT MIN(claim_date), MAX(claim_date)
FROM claims;
```

These checks ensure the dataset matches its documented assumptions.

---

## Key Takeaway

Synthetic healthcare datasets require **clear documentation of modeling assumptions**, including population size, provider count, therapeutic scope, and time coverage. These assumptions define how the dataset behaves and allow analysts to interpret results correctly and reproduce the dataset in the future.

---

## Points That May Require Verification or Are Context Dependent

Certain assumptions may vary depending on the intended dataset use case:

* Total population size
* Average claims per patient
* Therapy switching rates
* Coverage restriction frequency

These parameters should always be documented alongside the dataset to maintain transparency and analytical reliability.

