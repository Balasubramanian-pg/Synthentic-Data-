## Rule 11: Reflect Population Demographics

### Rule Definition

Synthetic healthcare datasets must reflect **realistic population demographics**, including age distribution, gender distribution, and insurance coverage patterns. These demographic characteristics strongly influence disease prevalence, treatment utilization, healthcare costs, and prescribing behavior.

Healthcare utilization patterns are closely associated with demographic characteristics such as age and sex because different population groups experience different disease risks and healthcare needs.

If synthetic datasets ignore demographic patterns and distribute patients randomly across ages or genders, the resulting analytics will not reflect realistic healthcare behavior.

---

## Example Age Distribution

Example population structure:

| Age Range | Share |
| --------- | ----- |
| 18–39     | 20%   |
| 40–64     | 45%   |
| 65+       | 35%   |

Digit-by-digit verification of totals:

20
+45
+35
= **100%**

This represents a population where middle-aged and older adults account for most healthcare utilization.

---

## Nuances That Must Be Understood

### 1. Age Strongly Influences Disease Prevalence

Different diseases affect different age groups.

Examples:

| Disease Area           | Typical Age Group |
| ---------------------- | ----------------- |
| Type 2 Diabetes        | 40+               |
| Cardiovascular disease | 50+               |
| Pediatric asthma       | Children          |
| Certain cancers        | 50+               |

Healthcare utilization tends to increase with age due to higher disease prevalence and chronic conditions.

Synthetic datasets should reflect these trends.

---

### 2. Gender Influences Treatment Patterns

Some diseases show strong gender differences.

Examples:

| Condition              | Higher Prevalence    |
| ---------------------- | -------------------- |
| Breast cancer          | Female               |
| Prostate cancer        | Male                 |
| Autoimmune diseases    | Female               |
| Cardiovascular disease | Male (earlier onset) |

If gender is distributed randomly across disease categories, treatment patterns become unrealistic.

---

### 3. Insurance Coverage Varies by Age Group

Insurance type often correlates with age and employment status.

Example coverage patterns:

| Age Group      | Typical Coverage                        |
| -------------- | --------------------------------------- |
| Children       | Family insurance or government programs |
| Working adults | Employer-sponsored insurance            |
| Seniors (65+)  | Medicare or equivalent programs         |

These relationships affect reimbursement patterns and treatment access.

---

### 4. Demographics Affect Healthcare Utilization

Older populations tend to generate more healthcare claims.

Example claim distribution by age group:

| Age Group | Average Claims per Year |
| --------- | ----------------------- |
| 18–39     | 2–3                     |
| 40–64     | 4–6                     |
| 65+       | 7–10                    |

Higher utilization among older adults is widely documented in healthcare cost and utilization research.

---

### 5. Demographic Realism Enables Population Health Analysis

Accurate demographic modeling supports several types of healthcare analytics.

Examples include:

* Disease prevalence analysis
* Healthcare cost forecasting
* Population health management
* Treatment adoption by demographic segment
* Insurance risk modeling

Without demographic realism, these analyses produce misleading results.

---

## Example Synthetic Demographic Table

Patient dimension example:

| Patient_ID | Age | Gender | Insurance_Type |
| ---------- | --- | ------ | -------------- |
| P100       | 68  | Female | Medicare       |
| P101       | 42  | Male   | Commercial     |
| P102       | 29  | Female | Commercial     |

Claims and treatments should reference these demographic attributes.

---

## Data Quality Validation Checks

Check age distribution:

```sql
SELECT age_group, COUNT(*)
FROM patient_dimension
GROUP BY age_group;
```

Check gender distribution by disease:

```sql
SELECT diagnosis, gender, COUNT(*)
FROM claims
JOIN patient_dimension USING(patient_id)
GROUP BY diagnosis, gender;
```

Check insurance distribution by age group:

```sql
SELECT insurance_type, age_group, COUNT(*)
FROM patient_dimension
GROUP BY insurance_type, age_group;
```

These checks help ensure demographic patterns remain realistic.

---

## Key Takeaway

Healthcare utilization and treatment patterns are strongly influenced by **population demographics such as age, gender, and insurance coverage**. Synthetic datasets must reflect these demographic relationships to produce realistic healthcare analytics and accurate population-level insights.

---

## Points That May Require Verification or Are Context Dependent

The following parameters may vary depending on the population being modeled:

* Exact age distribution of the dataset population
* Gender prevalence for specific diseases
* Insurance coverage patterns across countries or healthcare systems

These demographic assumptions should be explicitly documented in the dataset design.

