## Rule 14: Simulate Market Adoption Over Time

### Rule Definition

Pharmaceutical utilization typically changes over time, especially after a **new drug launch**. Synthetic healthcare datasets should simulate **gradual market adoption**, where the number of prescriptions or claims increases as awareness, clinical evidence, and physician familiarity grow.

Drug adoption rarely appears instantly at full market share. Instead, prescribing patterns tend to expand progressively as physicians begin prescribing the therapy, payer coverage expands, and clinical guidelines incorporate the treatment.

Modeling adoption trends ensures that time-series analyses, dashboards, and forecasting models behave realistically.

---

## Example Adoption Pattern

Example quarterly claims growth for a newly launched drug:

| Quarter | Claims |
| ------- | ------ |
| Q1      | 200    |
| Q2      | 420    |
| Q3      | 690    |
| Q4      | 950    |

Digit-by-digit verification of growth:

Q1 to Q2 increase

420
−200
= **220**

Q2 to Q3 increase

690
−420
= **270**

Q3 to Q4 increase

950
−690
= **260**

This pattern shows increasing adoption over time.

---

## Nuances That Must Be Understood

### 1. Drug Adoption Usually Follows a Gradual Curve

New therapies typically experience three stages of adoption:

| Stage          | Description                                 |
| -------------- | ------------------------------------------- |
| Early adoption | Small group of physicians begin prescribing |
| Growth phase   | More providers adopt the therapy            |
| Mature phase   | Adoption stabilizes across the market       |

Example claims pattern:

| Month | Claims |
| ----- | ------ |
| Jan   | 50     |
| Feb   | 80     |
| Mar   | 120    |
| Apr   | 190    |

This reflects increasing utilization.

---

### 2. Physician Adoption Drives Market Growth

New drug adoption depends heavily on physician behavior.

Factors influencing prescribing include:

* Clinical trial evidence
* Physician familiarity
* Peer recommendations
* Treatment guidelines
* Insurance coverage

Because physicians adopt therapies at different speeds, utilization tends to increase gradually rather than appearing instantly.

---

### 3. Insurance Coverage Expands Over Time

Payer coverage can significantly affect drug adoption.

Example timeline:

| Period    | Coverage Status            |
| --------- | -------------------------- |
| Launch    | Limited coverage           |
| 6 months  | Partial formulary coverage |
| 12 months | Broad coverage             |

As coverage expands, prescribing volume often increases.

---

### 4. Competing Drugs Influence Market Growth

Drug markets often contain multiple therapies competing for prescriptions.

Example therapeutic area:

| Drug   | Q1 Claims | Q4 Claims |
| ------ | --------- | --------- |
| Drug A | 200       | 950       |
| Drug B | 800       | 700       |
| Drug C | 600       | 500       |

In this example, Drug A gains adoption while competitors lose share.

This dynamic is common in pharmaceutical markets.

---

### 5. Adoption Trends Enable Time-Series Analytics

Modeling adoption allows analysts to study:

* Drug launch performance
* Market share changes
* Physician adoption rates
* Growth forecasting
* Competitive market dynamics

Example dashboard metric:

**Quarter-over-quarter growth**

Calculation example:

Claims in Q3 = 690
Claims in Q2 = 420

Growth calculation:

690
−420
= **270 claim increase**

Growth percentage:

270 ÷ 420 = 0.6428 ≈ **64.3% growth**

---

## Example Synthetic Dataset Pattern

Example time-series claims table:

| Quarter | Drug   | Claims |
| ------- | ------ | ------ |
| Q1      | Drug A | 200    |
| Q2      | Drug A | 420    |
| Q3      | Drug A | 690    |
| Q4      | Drug A | 950    |

Example competing drug decline:

| Quarter | Drug B Claims |
| ------- | ------------- |
| Q1      | 900           |
| Q2      | 820           |
| Q3      | 760           |
| Q4      | 700           |

These patterns simulate realistic market competition.

---

## Data Quality Validation Checks

Check claim trends by quarter:

```sql
SELECT quarter, product_id, COUNT(*) AS claim_count
FROM claims
GROUP BY quarter, product_id
ORDER BY quarter;
```

Detect unrealistic flat utilization:

```sql
SELECT product_id, COUNT(DISTINCT claim_count)
FROM (
    SELECT product_id, quarter, COUNT(*) AS claim_count
    FROM claims
    GROUP BY product_id, quarter
) t
GROUP BY product_id;
```

If a product has identical counts across many periods, the dataset may lack realistic adoption trends.

---

## Key Takeaway

Pharmaceutical utilization evolves over time as drugs enter the market and gain physician acceptance. Synthetic healthcare datasets should simulate **gradual market adoption patterns**, enabling realistic time-series analytics, market share analysis, and forecasting models.

---

## Points That May Require Verification or Are Context Dependent

The exact adoption curve may vary depending on:

* Therapeutic area (oncology drugs often adopt faster than primary care drugs)
* Payer coverage policies
* Competitive market dynamics
* Regulatory approval timing

These parameters should be calibrated according to the intended analytical use case of the dataset.

