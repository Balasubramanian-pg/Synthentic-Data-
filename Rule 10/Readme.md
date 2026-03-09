## Rule 10: Avoid Uniform Distributions

### Rule Definition

Synthetic healthcare datasets should **not distribute values evenly across categories** because real-world healthcare data rarely follows perfectly balanced patterns. Instead, datasets should reflect **skewed or uneven distributions** where certain drugs, providers, or treatments occur more frequently than others.

Uniform distributions make datasets appear artificial and reduce the realism of analytics. In real healthcare environments, treatment utilization, prescribing patterns, and patient volumes typically follow **skewed distributions**, where a small number of entities account for a large share of activity.

This principle is widely observed in health services research, where utilization patterns often concentrate around certain providers, therapies, or populations.
Citations:

* Health Services Research utilization studies (Agency for Healthcare Research and Quality)
* Kimball & Ross, *The Data Warehouse Toolkit*
* AHIMA Data Quality Management Model

---

## Example of an Unrealistic Pattern

Uniform distribution across drugs:

| Product | Claims Share |
| ------- | ------------ |
| Drug A  | 25%          |
| Drug B  | 25%          |
| Drug C  | 25%          |
| Drug D  | 25%          |

This pattern is unlikely because healthcare utilization rarely spreads evenly across treatments.

---

## Example of a Realistic Pattern

More realistic utilization distribution:

| Product | Claims Share |
| ------- | ------------ |
| Drug A  | 45%          |
| Drug B  | 30%          |
| Drug C  | 15%          |
| Drug D  | 10%          |

This reflects a common market pattern where:

* One therapy dominates usage
* A few alternatives hold moderate share
* Several therapies have smaller adoption

---

## Nuances That Must Be Understood

### 1. Healthcare Utilization Is Naturally Skewed

In real healthcare systems, certain entities tend to dominate activity.

Examples include:

* A small number of physicians writing a large portion of prescriptions
* A few drugs dominating market share within a therapeutic area
* Certain hospitals handling the majority of procedures in a region

This uneven distribution reflects real-world clinical practice and treatment preferences.

---

### 2. Drug Markets Often Show Leader Dominance

Pharmaceutical markets commonly exhibit **market concentration**, where leading therapies capture a significant portion of prescriptions.

Example therapeutic market:

| Drug   | Claims |
| ------ | ------ |
| Drug A | 450    |
| Drug B | 300    |
| Drug C | 150    |
| Drug D | 100    |

Digit-by-digit working for total claims:

450
+300
+150
+100
= 1000 total claims

Market share calculations:

Drug A share

450 ÷ 1000 = 0.45 → **45%**

Drug B share

300 ÷ 1000 = 0.30 → **30%**

Drug C share

150 ÷ 1000 = 0.15 → **15%**

Drug D share

100 ÷ 1000 = 0.10 → **10%**

This type of distribution better reflects real treatment markets.

---

### 3. Physician Activity Is Uneven

Healthcare providers vary significantly in patient volume.

Example distribution:

| Physician | Claims |
| --------- | ------ |
| H501      | 220    |
| H502      | 140    |
| H503      | 35     |
| H504      | 12     |

Reasons for variation include:

* Practice size
* Specialty demand
* Hospital affiliation
* Regional population density

Synthetic datasets should reproduce these differences.

---

### 4. Patient Utilization Also Varies

Patients generate very different numbers of claims.

Example realistic distribution:

| Claims per Patient | Share of Patients |
| ------------------ | ----------------- |
| 1–2 claims         | 35%               |
| 3–5 claims         | 40%               |
| 6–10 claims        | 20%               |
| 11+ claims         | 5%                |

This pattern reflects how some patients require significantly more care than others.

---

### 5. Uniform Data Breaks Analytical Realism

Uniform datasets create misleading analytics.

Example unrealistic dashboard outcome:

| Drug   | Claims |
| ------ | ------ |
| Drug A | 250    |
| Drug B | 250    |
| Drug C | 250    |
| Drug D | 250    |

Such results rarely occur in real healthcare markets.

This affects analyses such as:

* Market share dashboards
* Prescribing trends
* Provider adoption analysis
* Treatment pathway modeling

---

## Example Synthetic Distribution Strategy

When generating synthetic data, probabilities should be assigned to categories.

Example probability weights for drugs:

| Drug   | Probability |
| ------ | ----------- |
| Drug A | 0.45        |
| Drug B | 0.30        |
| Drug C | 0.15        |
| Drug D | 0.10        |

During dataset generation, claims should be assigned using these weighted probabilities rather than equal probability.

---

## Data Quality Validation Checks

Detect suspiciously uniform distributions:

```sql
SELECT product_id, COUNT(*) AS claim_count
FROM claims
GROUP BY product_id;
```

Check variance in provider activity:

```sql
SELECT hcp_id, COUNT(*) AS claim_count
FROM claims
GROUP BY hcp_id
ORDER BY claim_count DESC;
```

If most entities have nearly identical counts, the dataset may be artificially uniform.

---

## Key Takeaway

Healthcare data naturally exhibits **uneven utilization patterns**. Synthetic datasets should replicate these realistic skews rather than distributing activity evenly across drugs, providers, or patients. Modeling these distributions ensures that dashboards, analytics, and market simulations behave more like real-world healthcare systems.

---

## Points That May Require Verification or Are Context Dependent

The exact distribution patterns may vary depending on:

* Therapeutic area (oncology vs primary care)
* Geographic healthcare market
* Insurance coverage differences
* Dataset size and time horizon

These parameters should be calibrated according to the intended analytical use case of the synthetic dataset.

