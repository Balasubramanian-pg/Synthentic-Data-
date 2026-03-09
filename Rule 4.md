## Rule 4: Simulate Multiple Claims Per Patient

### Rule Definition

Each patient in a healthcare dataset should typically generate **multiple claims over time**, reflecting ongoing medical interactions such as doctor visits, prescriptions, laboratory tests, or procedures.

Healthcare utilization rarely consists of a single event. Patients often interact with the healthcare system repeatedly, especially when managing chronic conditions or undergoing treatment regimens. Therefore, synthetic datasets must simulate **longitudinal patient activity**, where each patient has a sequence of claims occurring across time.

Modeling multiple claims per patient enables accurate analysis of treatment adherence, healthcare utilization patterns, and patient journey analytics.

This concept is widely recognized in healthcare analytics and claims data modeling.
Citations:

* CMS Medicare Claims Data Structure documentation
* AHIMA Data Quality Management Model
* Kimball & Ross, *The Data Warehouse Toolkit*
* Healthcare Cost and Utilization Project (HCUP) data structure guidance

---

## Example

### Claim History for One Patient

| Patient_ID | Claim_Date | Product |
| ---------- | ---------- | ------- |
| P020       | 2023-01    | Drug A  |
| P020       | 2023-02    | Drug A  |
| P020       | 2023-03    | Drug A  |

This pattern represents a **recurring prescription therapy**, which is common for chronic disease treatment.

Single-record patients are uncommon in treatment datasets because patients typically require:

* Follow-up visits
* Prescription refills
* Diagnostic tests
* Ongoing monitoring

---

## Nuances That Must Be Understood

### 1. Different Diseases Produce Different Claim Patterns

Healthcare utilization varies depending on whether the condition is **chronic or acute**.

Chronic conditions generate recurring claims.

Examples include:

* Diabetes
* Hypertension
* Oncology maintenance therapy
* Rheumatoid arthritis

Example claim pattern:

| Month | Claim               |
| ----- | ------------------- |
| Jan   | Prescription refill |
| Feb   | Lab test            |
| Mar   | Physician visit     |

Acute conditions generate shorter sequences.

Examples include:

* Infections
* Minor injuries
* Short-term procedures

Example pattern:

| Month | Claim        |
| ----- | ------------ |
| Jan   | Consultation |
| Jan   | Prescription |
| Feb   | Follow-up    |

Synthetic datasets should reflect these differences.

---

### 2. Claims Represent Events, Not Patients

A common modeling mistake is confusing **patients with claim events**.

Correct structure:

| Table         | Description           |
| ------------- | --------------------- |
| Patient Table | One row per patient   |
| Claims Table  | Many rows per patient |

Relationship:

1 Patient → Many Claims

Example:

Patient table

| Patient_ID |
| ---------- |
| P020       |

Claims table

| Claim_ID | Patient_ID |
| -------- | ---------- |
| C1001    | P020       |
| C1002    | P020       |
| C1003    | P020       |

This structure allows longitudinal tracking.

---

### 3. Claim Frequency Should Be Realistic

Claim frequency depends on treatment type.

Typical healthcare utilization examples:

| Treatment Type      | Typical Claim Frequency |
| ------------------- | ----------------------- |
| Chronic medication  | Monthly refill          |
| Oncology infusion   | Every 2–3 weeks         |
| Primary care visits | Few per year            |
| Hospitalization     | Rare but high cost      |

Uniform claim spacing is unrealistic.

Example unrealistic pattern:

| Patient | Claims |
| ------- | ------ |
| P1      | 1      |
| P2      | 1      |
| P3      | 1      |

More realistic pattern:

| Patient | Claims |
| ------- | ------ |
| P1      | 2      |
| P2      | 7      |
| P3      | 4      |

---

### 4. Claim Sequences Enable Patient Journey Analysis

Multiple claims allow analysts to reconstruct the **treatment journey**.

Example patient pathway:

| Event              | Date |
| ------------------ | ---- |
| Diagnosis          | Jan  |
| First Prescription | Feb  |
| Refill             | Mar  |
| Lab Monitoring     | Apr  |

Without multiple claims, patient journey analytics becomes impossible.

---

### 5. Claims Often Cluster Around Treatment Periods

Healthcare claims tend to occur in **bursts around clinical activity**.

Example:

| Month | Claim Type         |
| ----- | ------------------ |
| Jan   | Diagnosis visit    |
| Jan   | Diagnostic imaging |
| Feb   | Treatment start    |
| Mar   | Follow-up          |

Long gaps between claims may indicate:

* Treatment discontinuation
* Disease remission
* Insurance coverage change

Synthetic data should include both clusters and gaps.

---

### 6. Claims Drive Most Healthcare Analytics

Many healthcare metrics depend on claim-level events.

Examples include:

* Total treatment duration
* Adherence rates
* Healthcare utilization
* Cost of care
* Provider prescribing patterns

If each patient has only one claim, these metrics cannot be calculated.

---

## Example Synthetic Distribution

Example claim counts across patients:

| Claims per Patient | Share of Patients |
| ------------------ | ----------------- |
| 1 claim            | 10%               |
| 2–3 claims         | 35%               |
| 4–6 claims         | 30%               |
| 7+ claims          | 25%               |

This distribution creates a more realistic dataset.

---

## Data Quality Validation Checks

Check patients with only one claim:

```sql
SELECT patient_id, COUNT(*) AS claim_count
FROM claims
GROUP BY patient_id
HAVING COUNT(*) = 1;
```

Check distribution of claim counts:

```sql
SELECT claim_count, COUNT(*)
FROM (
    SELECT patient_id, COUNT(*) AS claim_count
    FROM claims
    GROUP BY patient_id
) t
GROUP BY claim_count
ORDER BY claim_count;
```

These checks help ensure the dataset reflects realistic healthcare utilization patterns.

---

## Key Takeaway

Healthcare datasets must simulate **longitudinal patient activity** by generating multiple claims per patient over time. This structure reflects real-world medical care patterns and enables accurate treatment analysis, utilization reporting, and patient journey reconstruction.

---

## Points That May Require Verification or Are Context Dependent

The following parameters may vary depending on the healthcare system or dataset design:

* Typical number of claims per patient in specific therapeutic areas
* Frequency of prescription refills across countries or insurance systems
* Average claim counts in oncology versus primary care datasets

These values should be calibrated based on the intended analytical use case of the synthetic dataset.
