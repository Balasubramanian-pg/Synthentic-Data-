## Rule 1: Maintain Patient-Level Uniqueness

### Rule Definition

Each **Patient_ID** must represent exactly one individual across the entire dataset. A patient identifier must never refer to multiple individuals, and a single individual must not be represented by multiple identifiers.

Patient-level uniqueness is the foundation of healthcare analytics because almost every clinical, financial, and operational metric depends on accurate patient-level aggregation. When the same person appears under multiple identifiers, measures such as utilization, treatment outcomes, and readmission rates become unreliable.

Healthcare data models commonly enforce this rule through a **Patient Dimension** that acts as the single source of truth for demographic attributes and patient identity. This design pattern is widely used in healthcare data warehouses and dimensional models. (Kimball dimensional modeling approach)
Sources confirm that patient identity integrity is critical for accurate health data analysis and interoperability.
Citations:

* Kimball & Ross, *The Data Warehouse Toolkit* (Dimensional modeling practices)
* AHIMA Data Quality Management Model
* HIMSS Healthcare Data Governance Framework
* ISO/TS 22220 Health Informatics Identification of Subjects of Health Care

### Example

| Patient_ID | Gender | Birth_Year |
| ---------- | ------ | ---------- |
| P001       | Female | 1968       |

All claims, visits, prescriptions, and procedures referencing this patient must use **P001**.

Incorrect Example:

| Claim_ID | Patient_ID | Gender |
| -------- | ---------- | ------ |
| C100     | P001       | Female |
| C101     | P001       | Male   |

This violates patient-level uniqueness because demographic attributes change across records.

---

## Nuances That Must Be Understood

### 1. Patient Dimension vs Transaction Tables

Patient demographics should be stored only once in the **patient dimension table**.

Transactional tables should reference the patient using a **foreign key**.

Correct structure:

Patient Table

| Patient_ID | Gender | Birth_Year |
| ---------- | ------ | ---------- |
| P001       | Female | 1968       |

Claims Table

| Claim_ID | Patient_ID | Claim_Date |
| -------- | ---------- | ---------- |
| C100     | P001       | 2024-02-01 |

This prevents demographic duplication across millions of claims.

---

### 2. Demographics Should Be Stable Attributes

Certain attributes should remain constant throughout the dataset.

Typically stable attributes:

* Birth year or date of birth
* Biological sex (if stored as immutable clinical attribute)
* Original patient identifier
* Country of origin

Attributes that may legitimately change:

* Address
* Insurance provider
* Marital status
* Contact information

If changing attributes must be tracked historically, a **Slowly Changing Dimension (Type 2)** approach is commonly used in healthcare data warehouses.

Sources: Kimball Dimensional Modeling and Healthcare Data Warehousing practices.

---

### 3. Avoid Duplicate Patient Records

Duplicate patient records often occur when data is merged from multiple systems such as:

* Electronic Health Records (EHR)
* Pharmacy systems
* Insurance claims systems
* Laboratory systems

Without proper identity resolution, the same patient might appear as:

| Patient_ID | Birth_Year |
| ---------- | ---------- |
| P001       | 1968       |
| P982       | 1968       |

Both could represent the same individual.

Healthcare organizations typically implement **Master Patient Index (MPI)** systems to prevent this issue.

Sources:

* HIMSS Master Patient Index Guidelines
* AHIMA Data Quality Management Model

---

### 4. Synthetic Data Generation Must Respect Identity Integrity

When generating synthetic healthcare datasets for testing or analytics training:

* Every generated patient must receive a unique ID.
* That ID must remain consistent across all tables.
* Demographic attributes should not randomly change across records.

For example:

Correct synthetic generation pattern:

```
Generate Patient Table first
Assign unique patient IDs
Then generate claims referencing those IDs
```

Incorrect approach:

```
Generate claims first
Randomly assign patient IDs per claim
```

The incorrect approach produces duplicate or conflicting patient identities.

---

### 5. Patient Identity vs Encounter Identity

A common modeling mistake is confusing **patients** with **visits or encounters**.

Correct structure:

| Entity    | Description         |
| --------- | ------------------- |
| Patient   | Individual person   |
| Encounter | One hospital visit  |
| Claim     | Billing event       |
| Procedure | Treatment performed |

One patient can have many encounters.

Example:

| Patient_ID | Encounter_ID |
| ---------- | ------------ |
| P001       | E100         |
| P001       | E101         |
| P001       | E102         |

Breaking patient uniqueness corrupts longitudinal patient analysis.

---

### 6. Downstream Analytics That Depend on This Rule

Maintaining patient-level uniqueness directly affects:

* Patient cohort analysis
* Treatment effectiveness studies
* Readmission rate calculations
* Risk scoring models
* Population health analytics
* Healthcare cost analysis

If one patient appears multiple times with different identities, these analyses produce inflated counts and misleading conclusions.

---

## Data Quality Validation Checks

Typical validation queries used in healthcare data pipelines include:

Check duplicate patient identifiers:

```sql
SELECT Patient_ID, COUNT(*)
FROM Patient
GROUP BY Patient_ID
HAVING COUNT(*) > 1;
```

Check demographic inconsistencies:

```sql
SELECT Patient_ID
FROM Claims
GROUP BY Patient_ID
HAVING COUNT(DISTINCT Gender) > 1;
```

These checks are commonly included in **data quality frameworks for healthcare analytics pipelines**.

---

## Key Takeaway

Patient-level uniqueness ensures that each patient appears **once and only once** in the identity layer of the dataset. All healthcare transactions must reference that single identity, enabling reliable clinical, financial, and population-level analytics.

Without this rule, patient analytics becomes fundamentally unreliable.

---

## Points That May Require Further Verification

The conceptual frameworks referenced are widely accepted in healthcare data governance, but exact implementation standards may vary by organization:

* AHIMA Data Quality Management Model usage across organizations
* HIMSS MPI implementation practices
* ISO/TS 22220 adoption in specific healthcare systems

If you want, I can also help you build **10–15 additional pharma dataset integrity rules** similar to this one so your synthetic dataset remains analytically valid.
