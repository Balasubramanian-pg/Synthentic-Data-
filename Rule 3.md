## Rule 3: Use Realistic Date Sequences

### Rule Definition

All healthcare events must follow a **logical chronological order** that reflects how medical care actually occurs. Dates representing diagnoses, treatments, claims, procedures, and follow-ups must be sequenced so that earlier clinical events precede later ones.

Healthcare data models rely heavily on **temporal relationships** to reconstruct patient journeys, evaluate treatment effectiveness, and measure healthcare utilization. When events appear in an impossible order, such as a treatment occurring before diagnosis, the dataset becomes unreliable for clinical analysis and reporting.

Temporal consistency is a well-established requirement in healthcare data quality frameworks and clinical data management standards.
Citations:

* FDA Clinical Data Standards Guidance (CDISC models for clinical trials)
* AHIMA Data Quality Management Model
* ISO 8000 Data Quality Standards
* Kimball & Ross, *The Data Warehouse Toolkit*

---

## Example

| Patient_ID | Diagnosis_Date | Treatment_Start |
| ---------- | -------------- | --------------- |
| P010       | 2023-01-12     | 2023-02-03      |

This sequence is valid because the diagnosis occurs before treatment.

A typical patient timeline may follow this pattern:

1. Patient enrollment or eligibility begins
2. Symptoms or diagnostic testing occurs
3. Diagnosis is recorded
4. Treatment begins
5. Follow-up visits and claims are recorded

Example chronological order:

| Event           | Date       |
| --------------- | ---------- |
| Enrollment Date | 2023-01-01 |
| Diagnosis Date  | 2023-01-12 |
| Treatment Start | 2023-02-03 |
| Follow-Up Claim | 2023-03-10 |

---

## Nuances That Must Be Understood

### 1. Healthcare Events Occur in Logical Phases

A typical healthcare journey follows a structured timeline.

Common event ordering:

| Phase       | Example Event                      |
| ----------- | ---------------------------------- |
| Eligibility | Insurance coverage begins          |
| Diagnosis   | Disease confirmed                  |
| Treatment   | Medication or procedure begins     |
| Monitoring  | Follow-up visits and tests         |
| Outcome     | Recovery, remission, or escalation |

Claims and procedures should align with these phases.

Example violation:

| Event           | Date       |
| --------------- | ---------- |
| Treatment Start | 2023-01-05 |
| Diagnosis       | 2023-01-20 |

This implies treatment occurred before the disease was identified.

---

### 2. Claims Should Occur After the Service Date

Healthcare claims represent **billing for services that already occurred**. Therefore, the service date must precede or match the claim submission date.

Correct pattern:

| Service_Date | Claim_Submission_Date |
| ------------ | --------------------- |
| 2023-03-01   | 2023-03-05            |

Incorrect pattern:

| Service_Date | Claim_Submission_Date |
| ------------ | --------------------- |
| 2023-03-05   | 2023-03-01            |

This indicates billing occurred before the service happened.

Such inconsistencies make reimbursement and utilization analytics unreliable.

---

### 3. Enrollment Periods Define Valid Activity Windows

Healthcare claims and treatments should fall within the **patient's insurance enrollment period**.

Example:

Enrollment table

| Patient_ID | Enrollment_Start | Enrollment_End |
| ---------- | ---------------- | -------------- |
| P010       | 2023-01-01       | 2023-12-31     |

Claims outside this window are suspicious.

Incorrect example:

| Claim_Date | Enrollment_End |
| ---------- | -------------- |
| 2024-02-10 | 2023-12-31     |

This suggests a claim occurred after coverage ended.

---

### 4. Follow-Up Events Should Occur After Treatment Initiation

Many healthcare analytics depend on follow-up events such as:

* Lab tests
* Monitoring visits
* Prescription refills
* Adverse event monitoring

Example sequence:

| Event           | Date       |
| --------------- | ---------- |
| Treatment Start | 2023-02-03 |
| Lab Test        | 2023-02-18 |
| Follow-Up Visit | 2023-03-01 |

If follow-up events appear before treatment begins, the dataset contradicts clinical workflows.

---

### 5. Synthetic Data Must Respect Temporal Logic

When generating synthetic healthcare data:

* Patient birth dates must precede all medical events
* Diagnosis should occur before treatment
* Claims must occur after service dates
* Follow-ups must occur after treatment initiation

Incorrect synthetic pattern:

| Event     | Date       |
| --------- | ---------- |
| Claim     | 2023-02-01 |
| Diagnosis | 2023-02-10 |

This suggests billing for a treatment before diagnosis.

Correct generation approach:

1. Generate patient demographics
2. Assign diagnosis dates
3. Generate treatment start dates after diagnosis
4. Generate follow-up claims after treatment

---

### 6. Temporal Errors Break Patient Journey Analytics

Chronological integrity is essential for analyses such as:

* Time-to-treatment analysis
* Disease progression studies
* Drug adherence tracking
* Clinical pathway analysis
* Health economics and outcomes research

If event sequences are invalid, calculations such as **treatment lag** become impossible.

Example metric:

Treatment Lag = Treatment_Start − Diagnosis_Date

Digit-by-digit example:

Diagnosis_Date: 2023-01-12
Treatment_Start: 2023-02-03

Days between events:

* January remaining days after 12
  31 − 12 = 19
* February days until 3
  3

Total lag:

19 + 3 = **22 days**

If treatment appears before diagnosis, this metric becomes negative and analytically meaningless.

---

## Data Quality Validation Checks

Common validation queries used in healthcare datasets:

Detect treatment before diagnosis:

```sql
SELECT patient_id
FROM patient_treatment
WHERE treatment_start < diagnosis_date;
```

Detect claims before enrollment:

```sql
SELECT c.patient_id
FROM claims c
JOIN enrollment e
ON c.patient_id = e.patient_id
WHERE c.claim_date < e.enrollment_start;
```

Detect follow-ups before treatment:

```sql
SELECT patient_id
FROM followup_visits
WHERE followup_date < treatment_start;
```

These checks are often implemented in healthcare **data quality monitoring pipelines**.

---

## Key Takeaway

Healthcare datasets must preserve **temporal realism**. Every medical event must occur in a plausible chronological sequence that mirrors real clinical workflows. Enforcing valid date ordering ensures that patient journeys, treatment timelines, and utilization metrics remain analytically meaningful.

---

## Points That May Require Verification or Are Context Dependent

The following aspects can vary by healthcare system or dataset design:

* Whether diagnosis must strictly precede treatment in all clinical scenarios
* The acceptable delay between service date and claim submission
* How enrollment periods are modeled across different payer systems

These variations depend on payer policies, healthcare systems, and regional reporting standards.
