## Rule 16: Support Common Reporting Metrics

### Rule Definition

The dataset must contain **all necessary fields required to calculate standard pharmaceutical and healthcare reporting metrics**. Synthetic datasets should be structured so that commonly used business and clinical KPIs can be computed directly from the data without requiring external assumptions.

Healthcare analytics platforms and pharmaceutical dashboards rely on specific metrics to evaluate treatment adoption, patient utilization, payer coverage, and market performance. If the dataset lacks the columns required to compute these metrics, many real-world analytical workflows cannot be reproduced.

Designing datasets around common reporting metrics ensures that dashboards, BI tools, and analytical models behave realistically.

---

## Example Metrics Supported by the Dataset

Common pharmaceutical KPIs include:

| Metric                   | Description                                    |
| ------------------------ | ---------------------------------------------- |
| New Patient Starts (NPS) | Patients initiating therapy for the first time |
| Total Patients           | Number of unique treated patients              |
| Market Share             | Percentage of claims for each drug             |
| Coverage Penetration     | Share of plans covering a therapy              |
| Treatment Persistence    | Duration patients remain on therapy            |

The dataset must include columns that enable calculation of these metrics.

---

## Example Required Columns

To support these metrics, a typical dataset requires fields such as:

| Column         | Purpose                            |
| -------------- | ---------------------------------- |
| Patient_ID     | Identify unique patients           |
| Claim_Date     | Track treatment timelines          |
| Product_ID     | Identify the drug used             |
| Plan_ID        | Identify payer coverage            |
| Diagnosis_Code | Associate treatments with diseases |
| HCP_ID         | Track prescribing physicians       |

Without these columns, key pharmaceutical analyses cannot be performed.

---

## Nuances That Must Be Understood

### 1. New Patient Starts Require First Treatment Detection

**New Patient Starts (NPS)** represent patients receiving a drug for the first time.

Example dataset:

| Patient_ID | Claim_Date | Product |
| ---------- | ---------- | ------- |
| P100       | Jan        | Drug A  |
| P100       | Feb        | Drug A  |
| P101       | Mar        | Drug A  |

Here:

* P100 started therapy in January
* P101 started therapy in March

NPS = **2 patients**

Calculation explanation:

Unique patients starting therapy:

Patient P100
Patient P101

Total = **2**

To calculate NPS, the dataset must contain:

* Patient identifier
* Drug identifier
* Claim date

---

### 2. Total Patients Requires Unique Patient Tracking

Total treated patients represents the number of distinct patients receiving a therapy.

Example:

| Patient_ID | Product |
| ---------- | ------- |
| P100       | Drug A  |
| P101       | Drug A  |
| P102       | Drug A  |

Total patients = **3**

Digit-by-digit counting:

P100 = 1
P101 = 2
P102 = 3

Total = **3 patients**

This metric requires a reliable patient identifier.

---

### 3. Market Share Requires Product-Level Claims

Market share compares the proportion of claims for each drug within a therapeutic area.

Example claims:

| Product | Claims |
| ------- | ------ |
| Drug A  | 450    |
| Drug B  | 300    |
| Drug C  | 250    |

Digit-by-digit calculation of total claims:

450
+300
+250
= **1000 claims**

Market share calculation:

Drug A share

450 ÷ 1000 = 0.45 = **45%**

Drug B share

300 ÷ 1000 = 0.30 = **30%**

Drug C share

250 ÷ 1000 = 0.25 = **25%**

This requires a **product identifier** in the dataset.

---

### 4. Coverage Penetration Requires Plan-Level Data

Coverage penetration measures how widely a therapy is covered across insurance plans.

Example:

| Plan_ID | Drug A Coverage |
| ------- | --------------- |
| PL101   | Covered         |
| PL102   | Covered         |
| PL103   | Not Covered     |
| PL104   | Covered         |

Digit-by-digit calculation:

Covered plans:

PL101 = 1
PL102 = 2
PL104 = 3

Total plans = 4

Coverage penetration calculation:

3 ÷ 4 = 0.75 = **75% coverage**

This metric requires:

* Plan identifier
* Coverage status
* Drug identifier

---

### 5. Treatment Persistence Requires Longitudinal Data

Treatment persistence measures how long patients remain on therapy.

Example patient timeline:

| Patient_ID | Claim_Date | Product |
| ---------- | ---------- | ------- |
| P100       | Jan        | Drug A  |
| P100       | Feb        | Drug A  |
| P100       | Mar        | Drug A  |

Duration calculation:

Jan → Feb = 1 month
Feb → Mar = 2 months

Total treatment duration = **2 months**

Without timestamps and patient identifiers, persistence cannot be calculated.

---

## Example Minimal Dataset Supporting Key Metrics

Example claims table:

| Claim_ID | Patient_ID | Product_ID | Claim_Date | Plan_ID |
| -------- | ---------- | ---------- | ---------- | ------- |
| C1001    | P100       | Drug A     | 2023-01    | PL101   |
| C1002    | P100       | Drug A     | 2023-02    | PL101   |
| C1003    | P101       | Drug B     | 2023-03    | PL102   |

This structure supports most pharmaceutical analytics.

---

## Data Quality Validation Checks

Check that required columns exist:

```sql
SELECT column_name
FROM information_schema.columns
WHERE table_name = 'claims';
```

Verify patient-level metrics can be computed:

```sql
SELECT COUNT(DISTINCT patient_id)
FROM claims;
```

Verify product-level claims:

```sql
SELECT product_id, COUNT(*)
FROM claims
GROUP BY product_id;
```

These checks confirm that the dataset supports reporting calculations.

---

## Key Takeaway

Synthetic healthcare datasets should be designed with **analytical metrics in mind**. By including the necessary identifiers and time-based fields, the dataset can support common pharmaceutical KPIs such as new patient starts, market share, coverage penetration, and treatment persistence.

---

## Points That May Require Verification or Are Context Dependent

Some reporting metrics may vary depending on the intended analytical environment:

* Definition of New Patient Starts (for example 6-month clean period vs lifetime first use)
* Market share calculation scope (therapeutic class vs total market)
* Persistence measurement thresholds (for example allowable refill gaps)

These metric definitions should be clearly documented as part of the dataset design.

