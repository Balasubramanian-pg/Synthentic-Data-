# Writing Rules for Mock Pharma Dataset Guides

*Most people treat mock data as a placeholder - just enough rows to make the SAS code run without red text in the log. That is a mistake. Mock data isn't there to prove your code works on perfect inputs; it's there to prove your code handles the garbage that real life throws at clinical trials. If your mock data is clean, your production run will fail.*

## Introduction

Mock datasets are widely used in pharmaceutical analytics when real-world healthcare data cannot be shared because of privacy regulations or commercial restrictions. Synthetic data allows teams to develop dashboards, test data pipelines, and train analysts without exposing protected health information.

However, synthetic datasets must behave like real healthcare data. Random numbers alone are not sufficient. The structure, relationships, and statistical patterns should resemble those found in actual claims, payer, and provider datasets used in pharmaceutical reporting.

The following eighteen rules outline practical constraints and modeling practices for constructing mock pharmaceutical datasets used in reporting environments.

# Rule 1: Maintain Patient-Level Uniqueness

Every patient identifier must represent one individual consistently across the dataset.

Key practices:

* Each `Patient_ID` appears once in the patient dimension.
* All claims referencing that patient must use the same identifier.
* Demographic attributes should remain stable across records.

Example:

| Patient_ID | Gender | Birth_Year |
| ---------- | ------ | ---------- |
| P001       | Female | 1968       |

Changing demographics across records breaks patient-level analytics.

# Rule 2: Ensure Referential Integrity

Fact tables must reference valid dimension records.

Examples of relationships:

* Claim → Patient
* Claim → HCP
* Claim → Product
* Claim → Payer Plan

Example structure:

| Claim_ID | Patient_ID | HCP_ID |
| -------- | ---------- | ------ |
| C1001    | P001       | H230   |

If `P001` does not exist in the patient table, the dataset becomes unusable for reporting joins.

# Rule 3: Use Realistic Date Sequences

Healthcare events follow chronological order.

Examples of valid sequences:

Diagnosis Date < Treatment Start Date < Follow-Up Claims

Example:

| Patient_ID | Diagnosis_Date | Treatment_Start |
| ---------- | -------------- | --------------- |
| P010       | 2023-01-12     | 2023-02-03      |

Claims should not occur before diagnosis or enrollment.

# Rule 4: Simulate Multiple Claims Per Patient

Patients usually generate several claims over time.

Typical patterns:

* Chronic conditions produce recurring claims.
* Acute conditions produce short claim sequences.

Example:

| Patient_ID | Claim_Date | Product |
| ---------- | ---------- | ------- |
| P020       | Jan        | Drug A  |
| P020       | Feb        | Drug A  |
| P020       | Mar        | Drug A  |

Single-record patients are uncommon in treatment datasets.

# Rule 5: Model Physician Consistency

Each physician identifier must represent one healthcare provider.

Attributes that should remain constant:

* Physician specialty
* Practice location
* Provider identifier

Example:

| HCP_ID | Specialty |
| ------ | --------- |
| H501   | Oncology  |

Randomly changing specialties leads to unrealistic provider data.

# Rule 6: Align Drugs With Disease Areas

Pharmaceutical products usually correspond to specific diagnoses.

Example mapping:

| Product | Typical Diagnosis |
| ------- | ----------------- |
| Drug A  | Oncology          |
| Drug B  | Diabetes          |

A cancer therapy appearing alongside diabetes diagnosis codes would distort clinical analytics.

# Rule 7: Represent Payer and Plan Relationships

Insurance plans belong to specific payer organizations.

Example:

| Payer            | Plan       |
| ---------------- | ---------- |
| Aetna            | Aetna PPO  |
| UnitedHealthcare | UHC Choice |

Plans should not appear under multiple payers.

# Rule 8: Reflect Coverage Restrictions

Formulary restrictions are common in payer datasets.

Typical fields include:

* Formulary tier
* Prior authorization requirement
* Step therapy requirement

Example:

| Plan_ID | Product | Formulary_Status |
| ------- | ------- | ---------------- |
| PL102   | Drug A  | Preferred        |
| PL103   | Drug A  | Restricted       |

These fields enable market access reporting.

# Rule 9: Maintain Geographic Consistency

Locations must follow administrative hierarchy.

Example hierarchy:

ZIP Code → State → Region

Example:

| ZIP   | State | Region    |
| ----- | ----- | --------- |
| 02139 | MA    | Northeast |

ZIP codes must correspond to valid states.

# Rule 10: Avoid Uniform Distributions

Real healthcare data rarely distributes evenly.

Example unrealistic pattern:

| Product | Claims |
| ------- | ------ |
| Drug A  | 25%    |
| Drug B  | 25%    |
| Drug C  | 25%    |
| Drug D  | 25%    |

More realistic pattern:

| Product | Claims |
| ------- | ------ |
| Drug A  | 45%    |
| Drug B  | 30%    |
| Drug C  | 15%    |
| Drug D  | 10%    |

# Rule 11: Reflect Population Demographics

Patient demographics affect treatment patterns.

Common demographic variables:

* Age
* Gender
* Insurance type

Example age distribution:

| Age Range | Share |
| --------- | ----- |
| 18–39     | 20%   |
| 40–64     | 45%   |
| 65+       | 35%   |

# Rule 12: Include Therapy Changes

Some patients switch therapies during treatment.

Example sequence:

| Patient_ID | Month | Product |
| ---------- | ----- | ------- |
| P055       | Jan   | Drug A  |
| P055       | May   | Drug B  |

Therapy switching supports treatment pathway analysis.

# Rule 13: Introduce Small Data Imperfections

Real datasets contain limited inconsistencies.

Examples:

* Missing ZIP codes
* Reversed claims
* Null specialty values

However, these cases should remain rare.

Typical range:

1–5 percent of records.

# Rule 14: Simulate Market Adoption Over Time

Drug utilization often increases after product launch.

Example growth pattern:

| Quarter | Claims |
| ------- | ------ |
| Q1      | 200    |
| Q2      | 420    |
| Q3      | 690    |

This pattern supports trend analysis in dashboards.

# Rule 15: Maintain Consistent Units of Measure

Numeric fields should follow a consistent format.

Examples:

* Currency fields stored in the same unit
* Dosage values using the same measurement scale

Example:

| Claim_ID | Paid_Amount |
| -------- | ----------- |
| C9001    | 1450.25     |

Mixing currencies or units complicates reporting.

# Rule 16: Support Common Reporting Metrics

The dataset should allow calculation of standard pharma KPIs.

Examples include:

* New Patient Starts
* Total Patients
* Market Share
* Coverage Penetration

Columns must support these calculations.

# Rule 17: Keep Dimension Tables Clean

Dimension tables should contain stable reference data.

Examples:

* Product list
* HCP master table
* Payer list

Dimension tables should avoid duplicate identifiers.

# Rule 18: Document Dataset Assumptions

Synthetic datasets require clear documentation.

Examples of assumptions:

* Total patient population
* Number of payers
* Number of HCPs
* Time period covered

This documentation helps regenerate or extend the dataset later.

# Summary

Mock pharmaceutical datasets should follow structural rules that reflect how real healthcare data behaves. The rules above focus on relationships, chronology, distribution patterns, and reporting requirements.

When these constraints are applied, synthetic datasets become suitable for:

* dashboard development
* reporting model testing
* analytics training environments
* BI proof-of-concept projects

The goal is not perfect realism. The goal is data that behaves consistently with pharmaceutical reporting workflows.
