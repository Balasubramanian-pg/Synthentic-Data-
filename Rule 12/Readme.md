## Rule 12: Include Therapy Changes

### Rule Definition

Synthetic healthcare datasets should model **therapy changes over time**, where some patients switch from one treatment to another during the course of care. These therapy changes reflect real-world clinical practice, where treatments may be adjusted due to effectiveness, side effects, disease progression, or insurance coverage constraints.

Including therapy changes allows analysts to study **treatment pathways**, drug switching patterns, and therapy persistence. Without such changes, datasets appear unrealistic because patients rarely remain on the exact same therapy indefinitely.

---

## Example Treatment Sequence

Example therapy switch for a patient:

| Patient_ID | Month | Product |
| ---------- | ----- | ------- |
| P055       | Jan   | Drug A  |
| P055       | May   | Drug B  |

This indicates the patient started with Drug A and later switched to Drug B.

---

## Nuances That Must Be Understood

### 1. Therapy Switching Is Common in Clinical Practice

Patients often change medications due to:

* Lack of treatment effectiveness
* Adverse side effects
* Disease progression
* New clinical guidelines
* Insurance coverage restrictions

Example scenario:

| Month | Treatment |
| ----- | --------- |
| Jan   | Drug A    |
| Mar   | Drug A    |
| May   | Drug B    |

This may indicate Drug A did not achieve the desired clinical outcome.

---

### 2. Therapy Lines Represent Treatment Stages

Healthcare analytics often tracks **lines of therapy (LOT)**.

Typical sequence:

| Line of Therapy | Description                       |
| --------------- | --------------------------------- |
| First Line      | Initial treatment after diagnosis |
| Second Line     | Used if first therapy fails       |
| Third Line      | Later-stage treatment             |

Example treatment pathway:

| Patient_ID | Therapy_Line | Drug   |
| ---------- | ------------ | ------ |
| P055       | 1            | Drug A |
| P055       | 2            | Drug B |

Modeling therapy lines helps analyze disease progression and treatment strategies.

---

### 3. Switching Patterns Vary by Disease Area

Different therapeutic areas have different switching behavior.

Examples:

| Disease          | Switching Frequency                      |
| ---------------- | ---------------------------------------- |
| Hypertension     | Moderate switching                       |
| Diabetes         | Moderate switching                       |
| Oncology         | Frequent switching as disease progresses |
| Acute infections | Rare switching                           |

Synthetic datasets should reflect the therapeutic area being modeled.

---

### 4. Therapy Persistence Should Also Be Modeled

Many patients remain on the same treatment for extended periods.

Example persistence pattern:

| Month | Drug   |
| ----- | ------ |
| Jan   | Drug A |
| Feb   | Drug A |
| Mar   | Drug A |
| Apr   | Drug A |

Switching should occur for **some patients**, but not all.

Example realistic distribution:

| Treatment Behavior      | Share of Patients |
| ----------------------- | ----------------- |
| Stay on initial therapy | 60%               |
| Switch once             | 30%               |
| Switch multiple times   | 10%               |

Digit-by-digit verification:

60
+30
+10
= **100%**

---

### 5. Therapy Switching Enables Advanced Analytics

Modeling therapy changes allows calculation of key pharmaceutical metrics.

Examples include:

* Time to therapy switch
* Treatment persistence
* Therapy line transitions
* Comparative treatment effectiveness
* Drug adoption patterns

Example calculation:

Months on Drug A before switch.

Example timeline:

Start: January
Switch: May

Month calculation:

Jan → Feb = 1
Feb → Mar = 2
Mar → Apr = 3
Apr → May = 4

Total time on Drug A = **4 months**

---

## Example Synthetic Treatment Table

| Patient_ID | Claim_Date | Product | Therapy_Line |
| ---------- | ---------- | ------- | ------------ |
| P055       | 2023-01    | Drug A  | 1            |
| P055       | 2023-03    | Drug A  | 1            |
| P055       | 2023-05    | Drug B  | 2            |

This structure allows analysts to track therapy progression.

---

## Data Quality Validation Checks

Detect patients with multiple therapies:

```sql
SELECT patient_id, COUNT(DISTINCT product) AS therapy_count
FROM claims
GROUP BY patient_id
HAVING COUNT(DISTINCT product) > 1;
```

Detect therapy line inconsistencies:

```sql
SELECT patient_id
FROM treatment_table
WHERE therapy_line IS NULL;
```

Check time gaps between therapies:

```sql
SELECT patient_id, product, claim_date
FROM treatment_table
ORDER BY patient_id, claim_date;
```

These checks help validate treatment progression.

---

## Key Takeaway

Real healthcare treatment journeys often involve **therapy changes**, where patients move from one drug to another over time. Synthetic datasets should include these transitions to realistically represent treatment pathways and support advanced pharmaceutical analytics.

---

## Points That May Require Verification or Are Context Dependent

The following parameters may vary depending on the dataset design:

* Typical therapy switching rates in specific disease areas
* Average duration of treatment before switching
* Number of therapy lines observed in clinical practice

These values should be calibrated based on the therapeutic area and analytical objectives of the dataset.

