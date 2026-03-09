## Rule 13: Introduce Small Data Imperfections

### Rule Definition

Real-world healthcare datasets contain **small levels of data imperfections**, such as missing values, delayed records, or minor inconsistencies. Synthetic datasets should intentionally include a limited number of such imperfections to better simulate real operational data environments.

Healthcare data originates from multiple systems such as hospitals, insurance claims processors, pharmacies, and laboratories. Because of this complexity, data quality issues occasionally occur during data entry, system integration, or transmission. Controlled imperfections help ensure that analytics pipelines, dashboards, and validation processes are tested against realistic scenarios.

However, these imperfections must remain **rare and controlled**, typically affecting only a small portion of the dataset.

---

## Example Imperfections

Typical issues that may appear in small proportions:

| Issue Type                | Example                             |
| ------------------------- | ----------------------------------- |
| Missing ZIP Code          | Patient record without location     |
| Null physician specialty  | Specialty field missing             |
| Delayed claim record      | Claim submitted weeks after service |
| Reversed claim adjustment | Correction record reversing a claim |

Example dataset sample:

| Claim_ID | Patient_ID | ZIP   | HCP_Specialty |
| -------- | ---------- | ----- | ------------- |
| C901     | P101       | 02139 | Oncology      |
| C902     | P102       | NULL  | Cardiology    |
| C903     | P103       | 60611 | NULL          |

These issues reflect realistic data imperfections.

---

## Nuances That Must Be Understood

### 1. Healthcare Data Comes From Multiple Systems

Healthcare data pipelines often combine information from different operational systems.

Examples include:

* Electronic Health Records (EHR)
* Pharmacy dispensing systems
* Insurance claims platforms
* Laboratory information systems
* Provider credentialing databases

When datasets are integrated across these systems, small inconsistencies can occur.

---

### 2. Missing Values Are Common in Operational Data

Certain fields may occasionally be missing due to:

* Data entry omissions
* System transmission failures
* Incomplete source system data
* Privacy masking

Example fields where missing values sometimes occur:

| Field               | Example Issue             |
| ------------------- | ------------------------- |
| ZIP code            | Location unavailable      |
| Physician specialty | Provider metadata missing |
| Secondary diagnosis | Not recorded              |

However, **core identifiers must never be missing**, such as:

* Patient_ID
* Claim_ID
* Product_ID

These identifiers are required for relational integrity.

---

### 3. Claims Adjustments May Reverse Previous Records

Healthcare claims systems often include **adjustment records**.

Example claim correction:

| Claim_ID | Adjustment_Type |
| -------- | --------------- |
| C8001    | Original        |
| C8001    | Reversal        |
| C8001    | Corrected       |

These adjustments occur when billing errors are corrected or claims are resubmitted.

Synthetic datasets can include a small number of such events.

---

### 4. Imperfections Should Remain Rare

Controlled imperfections should affect only a small percentage of records.

Typical acceptable ranges:

| Issue Type              | Suggested Rate |
| ----------------------- | -------------- |
| Missing optional fields | 1–3%           |
| Minor inconsistencies   | 1–2%           |
| Claim adjustments       | 1–5%           |

Digit-by-digit example:

1%
+2%
+2%
= **5% maximum affected records**

If imperfections exceed this level, the dataset becomes unreliable.

---

### 5. Imperfections Help Test Data Quality Pipelines

Including controlled imperfections enables validation of:

* Data quality monitoring systems
* ETL validation rules
* Error detection dashboards
* Data cleansing procedures

Example test scenario:

A dashboard should identify missing ZIP codes.

Example query:

```sql
SELECT COUNT(*)
FROM patient_dimension
WHERE zip_code IS NULL;
```

Without imperfections, these checks cannot be validated.

---

## Example Synthetic Imperfection Table

Example distribution of data quality issues:

| Issue Type        | Records | Percentage |
| ----------------- | ------- | ---------- |
| Missing ZIP       | 120     | 1.2%       |
| Missing specialty | 90      | 0.9%       |
| Claim reversals   | 300     | 3.0%       |

Digit-by-digit verification:

1.2
+0.9
+3.0
= **5.1%**

This represents a controlled imperfection rate.

---

## Data Quality Validation Checks

Detect missing ZIP codes:

```sql
SELECT patient_id
FROM patient_dimension
WHERE zip_code IS NULL;
```

Detect missing physician specialties:

```sql
SELECT hcp_id
FROM hcp_dimension
WHERE specialty IS NULL;
```

Detect claim reversal records:

```sql
SELECT claim_id, COUNT(*)
FROM claims
GROUP BY claim_id
HAVING COUNT(*) > 1;
```

These queries help validate expected imperfections.

---

## Key Takeaway

Real healthcare datasets are rarely perfectly clean. Introducing **small, controlled imperfections** such as missing optional fields or occasional claim adjustments makes synthetic datasets more realistic and allows data quality validation systems to be tested effectively.

---

## Points That May Require Verification or Are Context Dependent

Some parameters may vary depending on dataset design:

* Acceptable levels of missing data
* Frequency of claim adjustment records
* Fields allowed to contain null values

These thresholds should be defined as part of the dataset’s data quality standards.

