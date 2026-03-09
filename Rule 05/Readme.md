## Rule 5: Model Physician Consistency

### Rule Definition

Each **physician identifier (HCP_ID)** must consistently represent one healthcare provider across the dataset. All attributes describing that physician must remain stable and accurate for every record referencing that identifier.

Physicians act as key analytical entities in healthcare datasets because many analyses measure prescribing patterns, treatment adoption, referral networks, and provider performance. If a physician identifier changes specialties, locations, or other core attributes randomly, the dataset becomes analytically unreliable.

Maintaining physician consistency ensures that provider-level metrics correctly reflect real-world clinical practice patterns.

Provider master data management is a standard concept in healthcare data governance and analytics systems.
Citations:

* National Plan and Provider Enumeration System (NPPES) documentation for provider identifiers
* AHIMA Data Quality Management Model
* HIMSS Data Governance Guidelines
* Kimball & Ross, *The Data Warehouse Toolkit*

---

## Example

### HCP Dimension Table

| HCP_ID | Specialty     | Practice_Location |
| ------ | ------------- | ----------------- |
| H501   | Oncology      | Boston            |
| H502   | Endocrinology | Chicago           |

### Claims Table

| Claim_ID | Patient_ID | HCP_ID |
| -------- | ---------- | ------ |
| C3001    | P010       | H501   |
| C3002    | P020       | H501   |
| C3003    | P021       | H502   |

In this structure, **H501** always represents the same oncology physician.

Incorrect example:

| HCP_ID | Specialty   |
| ------ | ----------- |
| H501   | Oncology    |
| H501   | Dermatology |

This creates contradictory provider information.

---

## Nuances That Must Be Understood

### 1. Physician Identifiers Represent Unique Providers

Healthcare datasets rely on unique identifiers to represent physicians.

Examples of real-world identifiers include:

* National Provider Identifier (NPI) in the United States
* Medical license numbers
* Internal provider IDs used by payer systems

Each identifier must map to exactly **one provider profile**.

Example correct mapping:

| HCP_ID | Provider  |
| ------ | --------- |
| H501   | Dr. Smith |
| H502   | Dr. Patel |

Reusing identifiers for multiple providers breaks provider analytics.

---

### 2. Physician Specialty Should Be Stable

Physician specialties rarely change within short time horizons.

Typical specialties include:

* Oncology
* Cardiology
* Endocrinology
* Neurology
* General Practice

Example correct structure:

| HCP_ID | Specialty |
| ------ | --------- |
| H501   | Oncology  |

Incorrect synthetic example:

| Claim_ID | HCP_ID | Specialty   |
| -------- | ------ | ----------- |
| C100     | H501   | Oncology    |
| C200     | H501   | Orthopedics |

This implies the same physician practices multiple unrelated specialties.

---

### 3. Practice Location Should Be Consistent

Most physicians practice in **one primary geographic area**, although they may have multiple clinic locations within the same region.

Example consistent location data:

| HCP_ID | City   | State |
| ------ | ------ | ----- |
| H501   | Boston | MA    |

Incorrect example:

| HCP_ID | City   |
| ------ | ------ |
| H501   | Boston |
| H501   | Dallas |

Such changes suggest data errors unless modeled explicitly as multiple practice locations.

---

### 4. Provider Attributes Should Be Stored in a Dimension Table

Provider information should exist in a **single HCP dimension table**.

Correct structure:

HCP Table

| HCP_ID | Specialty | City   |
| ------ | --------- | ------ |
| H501   | Oncology  | Boston |

Claims Table

| Claim_ID | HCP_ID |
| -------- | ------ |
| C1001    | H501   |

This structure prevents attribute inconsistencies across claims.

---

### 5. Physician Consistency Enables Provider-Level Analytics

Many healthcare and pharmaceutical analyses depend on accurate physician data.

Examples include:

* Prescribing behavior analysis
* Drug adoption tracking
* Provider segmentation
* Referral network analysis
* Market access analytics

Example metric:

Total prescriptions by physician.

If the physician identifier changes attributes randomly, results become unreliable.

---

### 6. Synthetic Data Should Reflect Provider Distribution

Real healthcare systems include providers with varying patient volumes.

Example realistic distribution:

| Physician | Claims |
| --------- | ------ |
| H501      | 240    |
| H502      | 80     |
| H503      | 35     |

Uniform claim volumes across physicians are unrealistic.

---

## Data Quality Validation Checks

Check physicians with multiple specialties:

```sql id="r19p7q"
SELECT hcp_id
FROM hcp_dimension
GROUP BY hcp_id
HAVING COUNT(DISTINCT specialty) > 1;
```

Check inconsistent physician locations:

```sql id="3d7zpc"
SELECT hcp_id
FROM hcp_dimension
GROUP BY hcp_id
HAVING COUNT(DISTINCT city) > 1;
```

These checks help identify inconsistent provider records.

---

## Key Takeaway

Each physician identifier must represent **one consistent provider profile**. Stable attributes such as specialty, location, and provider identifier ensure that provider-level analytics accurately reflect real-world healthcare delivery patterns.

---

## Points That May Require Verification or Are Context Dependent

Some aspects of physician modeling may vary depending on dataset design:

* Physicians may legitimately practice at multiple locations within a health system
* Some physicians may hold dual specialties (for example internal medicine and cardiology)
* Provider identifiers differ across countries and healthcare systems

These variations should be explicitly modeled rather than randomly generated in synthetic datasets.
