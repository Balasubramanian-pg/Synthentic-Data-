## Rule 8: Reflect Coverage Restrictions

### Rule Definition

Healthcare insurance plans often apply **coverage restrictions** that determine whether and how a drug can be reimbursed. Synthetic healthcare datasets must include fields that represent these restrictions so that payer access and market access analytics can be performed realistically.

Coverage restrictions typically appear in **drug formularies**, which are lists of medications approved for reimbursement under a specific insurance plan. These formularies define how easily a drug can be prescribed and reimbursed. Drug formularies are widely used by insurance plans to control costs and guide prescribing behavior.

Without modeling these restrictions, pharmaceutical analytics such as payer access, formulary positioning, and coverage impact analysis cannot be simulated accurately.

---

## Example Structure

### Formulary Coverage Table

| Plan_ID | Product | Formulary_Status | Prior_Authorization | Step_Therapy |
| ------- | ------- | ---------------- | ------------------- | ------------ |
| PL102   | Drug A  | Preferred        | No                  | No           |
| PL103   | Drug A  | Restricted       | Yes                 | Yes          |

This table indicates that Drug A is easily accessible in **PL102** but more restricted in **PL103**.

Claims referencing these plans should reflect these coverage rules.

---

## Nuances That Must Be Understood

### 1. Formularies Control Drug Access

Insurance plans maintain **formularies**, which classify drugs according to coverage status and cost-sharing tiers.

Typical formulary tiers include:

| Tier   | Meaning             |
| ------ | ------------------- |
| Tier 1 | Preferred generic   |
| Tier 2 | Preferred brand     |
| Tier 3 | Non-preferred brand |
| Tier 4 | Specialty drug      |

Higher tiers usually require higher patient copayments.

Formularies help insurers manage drug costs and encourage the use of preferred therapies.

---

### 2. Prior Authorization Is Common for High-Cost Drugs

**Prior authorization** requires a physician to obtain approval from the insurer before a drug can be reimbursed.

Example scenario:

| Plan  | Drug   | Prior Authorization |
| ----- | ------ | ------------------- |
| PL102 | Drug A | No                  |
| PL103 | Drug A | Yes                 |

If prior authorization is required, a physician must submit clinical justification before prescribing.

This is common for:

* Specialty drugs
* Oncology therapies
* High-cost biologics

Prior authorization policies are used widely by insurers to control medication use and cost.

---

### 3. Step Therapy Requires Trying Lower-Cost Drugs First

**Step therapy** requires patients to try one or more preferred medications before accessing a more expensive treatment.

Example:

| Step   | Drug           |
| ------ | -------------- |
| Step 1 | Generic Drug B |
| Step 2 | Brand Drug A   |

If Drug B fails or causes side effects, Drug A may then be approved.

Step therapy policies are common in insurance formularies to promote cost-effective treatment pathways.

---

### 4. Coverage Restrictions Affect Prescribing Behavior

Physicians often adapt prescribing patterns based on payer restrictions.

Example:

| Plan  | Preferred Drug |
| ----- | -------------- |
| PL102 | Drug A         |
| PL103 | Drug B         |

In analytics, this results in different utilization patterns across payer plans.

Such variations are frequently analyzed in **market access and payer strategy studies**.

---

### 5. Claims Data Reflects Coverage Outcomes

Claims datasets indirectly reflect coverage restrictions through:

* Rejected claims
* Drug switching patterns
* Delayed treatment initiation
* Prior authorization approvals

Example treatment pattern:

| Patient_ID | Month | Product |
| ---------- | ----- | ------- |
| P200       | Jan   | Drug B  |
| P200       | Mar   | Drug A  |

This could represent step therapy progression.

---

## Example Synthetic Distribution

Example coverage distribution for a drug:

| Coverage Status | Share of Plans |
| --------------- | -------------- |
| Preferred       | 40%            |
| Non-Preferred   | 35%            |
| Restricted      | 25%            |

This distribution creates realistic payer variability.

---

## Data Quality Validation Checks

Check drugs appearing without formulary records:

```sql
SELECT DISTINCT product_id
FROM claims
WHERE product_id NOT IN (
    SELECT product_id
    FROM formulary_coverage
);
```

Check plans with missing restriction data:

```sql
SELECT plan_id
FROM formulary_coverage
WHERE formulary_status IS NULL;
```

These checks help ensure coverage rules are properly represented.

---

## Key Takeaway

Healthcare insurance plans often impose restrictions such as **formulary tiers, prior authorization requirements, and step therapy policies**. Synthetic healthcare datasets should model these restrictions because they strongly influence prescribing behavior, treatment access, and pharmaceutical market analytics.

---

## Points That May Require Verification or Are Context Dependent

Some aspects of coverage restriction modeling may vary across healthcare systems:

* The number of formulary tiers used by different insurers
* The proportion of drugs requiring prior authorization
* Regional regulations affecting step therapy policies

These factors differ across countries and insurance markets and should be calibrated according to the dataset’s intended use case.

