## Rule 2: Ensure Referential Integrity

### Rule Definition

Every key stored in a **fact table** must reference an existing and valid record in the corresponding **dimension table**.

Referential integrity ensures that relationships between datasets remain consistent and that every transactional record can be correctly linked to its descriptive attributes. In healthcare and pharmaceutical analytics, this rule guarantees that claims, prescriptions, encounters, and procedures always point to real entities such as patients, healthcare providers, products, or payer plans.

Without referential integrity, analytical joins fail, records become orphaned, and metrics such as treatment volumes, provider utilization, and payer reimbursements become inaccurate.

Referential integrity is a foundational concept in relational database systems and dimensional data warehouse design.
Citations:

* E. F. Codd, Relational Model of Data (1970)
* Kimball & Ross, *The Data Warehouse Toolkit*
* AHIMA Data Quality Management Model
* ISO 8000 Data Quality Standard

---

## Example

### Fact Table: Claims

| Claim_ID | Patient_ID | HCP_ID | Product_ID | Payer_ID |
| -------- | ---------- | ------ | ---------- | -------- |
| C1001    | P001       | H230   | DRG001     | PAY101   |

### Dimension Tables

Patient Table

| Patient_ID | Gender | Birth_Year |
| ---------- | ------ | ---------- |
| P001       | Female | 1968       |

HCP Table

| HCP_ID | Specialty |
| ------ | --------- |
| H230   | Oncology  |

Product Table

| Product_ID | Drug_Name     |
| ---------- | ------------- |
| DRG001     | Pembrolizumab |

Payer Plan Table

| Payer_ID | Plan_Name          |
| -------- | ------------------ |
| PAY101   | Medicare Advantage |

In this structure, every identifier in the claims table must exist in its respective dimension table.

---

## Nuances That Must Be Understood

### 1. Fact Tables Should Never Contain Unknown Foreign Keys

Fact tables store transactional events. They should reference dimension tables through **foreign keys**.

Incorrect design example:

| Claim_ID | Patient_ID |
| -------- | ---------- |
| C1001    | P999       |

If **P999** does not exist in the patient table, the claim record becomes an **orphan record**.

This breaks reporting queries such as:

```sql
SELECT *
FROM claims c
JOIN patient p
ON c.patient_id = p.patient_id;
```

The join will drop the orphan record.

---

### 2. Use Foreign Key Constraints Where Possible

Relational databases enforce referential integrity through **foreign key constraints**.

Example:

```sql
ALTER TABLE claims
ADD CONSTRAINT fk_patient
FOREIGN KEY (patient_id)
REFERENCES patient(patient_id);
```

This prevents inserting a claim with a patient identifier that does not exist.

Sources confirm that foreign key constraints are the standard mechanism used in relational systems to maintain referential integrity.
Citations:

* Date, *An Introduction to Database Systems*
* PostgreSQL documentation on foreign keys
* Oracle Database Concepts Guide

---

### 3. Synthetic Data Must Be Generated in the Correct Order

When building synthetic healthcare datasets, table generation order is critical.

Correct generation order:

1. Dimension tables
2. Fact tables referencing those dimensions

Example workflow:

Step 1: Generate Patient table
Step 2: Generate HCP table
Step 3: Generate Product table
Step 4: Generate Claims referencing those IDs

Incorrect workflow:

Generate claims first and randomly assign identifiers that may not exist.

This frequently produces invalid references.

---

### 4. Healthcare Data Models Often Use Many Dimensions

A pharmaceutical claim may reference several entities simultaneously.

Typical relationships:

| Fact Table | Dimension                 |
| ---------- | ------------------------- |
| Claim      | Patient                   |
| Claim      | HCP (Healthcare Provider) |
| Claim      | Product / Drug            |
| Claim      | Payer Plan                |
| Claim      | Pharmacy                  |
| Claim      | Geography                 |

Because fact tables can contain **millions or billions of records**, broken references multiply quickly and corrupt reporting outputs.

---

### 5. Handle Unknown or Missing Entities Explicitly

In real healthcare data, some information may be missing. Instead of leaving keys null or invalid, many warehouses create **default dimension records**.

Example:

| Patient_ID | Description     |
| ---------- | --------------- |
| P000       | Unknown Patient |

Claims with missing patient identifiers can reference this record.

Example:

| Claim_ID | Patient_ID |
| -------- | ---------- |
| C2001    | P000       |

This technique preserves referential integrity while flagging incomplete data.

This pattern is commonly recommended in dimensional modeling practices.
Source: Kimball & Ross, *The Data Warehouse Toolkit*.

---

### 6. Referential Integrity Enables Reliable Analytics

Several healthcare and pharma analyses depend on valid table relationships:

* Provider performance analysis
* Drug utilization reporting
* Payer reimbursement analysis
* Patient journey tracking
* Market access analysis
* Clinical outcome studies

Broken references prevent accurate aggregation across dimensions.

Example failure scenario:

A claim references a product that does not exist in the product dimension. Drug utilization reports would miss that claim entirely.

---

## Data Quality Validation Checks

Common validation queries used in healthcare data pipelines:

Check claims referencing missing patients:

```sql
SELECT c.patient_id
FROM claims c
LEFT JOIN patient p
ON c.patient_id = p.patient_id
WHERE p.patient_id IS NULL;
```

Check missing HCP references:

```sql
SELECT c.hcp_id
FROM claims c
LEFT JOIN hcp h
ON c.hcp_id = h.hcp_id
WHERE h.hcp_id IS NULL;
```

These checks are frequently implemented as part of **data quality monitoring frameworks**.

---

## Key Takeaway

Referential integrity ensures that every transaction in the dataset points to a valid and existing entity. By enforcing this rule, the data model maintains reliable relationships between facts and dimensions, allowing accurate joins, aggregations, and analytical insights.

Without referential integrity, healthcare datasets quickly accumulate orphan records that distort reporting and undermine trust in the data.

---

## Points That May Require Verification or Are Context Dependent

The following implementation details may vary by organization:

* Whether strict foreign key constraints are enforced in production data warehouses
* Whether unknown dimension records are used instead of null values
* The exact number of dimensions referenced by pharmaceutical claim models

These choices depend on the organization's data governance standards and warehouse architecture.
