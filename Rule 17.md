## Rule 17: Keep Dimension Tables Clean

### Rule Definition

Dimension tables must contain **stable, well-defined reference data without duplicate identifiers or conflicting attribute values**. Each entity in a dimension table should appear only once, and its descriptive attributes should remain consistent.

Dimension tables form the **reference backbone of analytical data models**, particularly in dimensional data warehouses where fact tables reference dimension records through keys. If dimension tables contain duplicates or inconsistent values, joins between fact tables and dimensions produce incorrect results.

Maintaining clean dimension tables ensures that analytics such as aggregations, filtering, and grouping behave correctly.

This principle aligns with standard dimensional modeling practices where dimension tables store descriptive attributes while fact tables store measurable events. Sources include Kimball & Ross, *The Data Warehouse Toolkit*, and common dimensional modeling practices in analytics systems.

---

## Example Dimension Table

Example **Product Dimension**

| Product_ID | Product_Name | Therapeutic_Area |
| ---------- | ------------ | ---------------- |
| DRG001     | Drug A       | Oncology         |
| DRG002     | Drug B       | Diabetes         |
| DRG003     | Drug C       | Cardiology       |

Each **Product_ID appears exactly once**.

---

## Example Fact Table Referencing the Dimension

Claims table:

| Claim_ID | Patient_ID | Product_ID |
| -------- | ---------- | ---------- |
| C1001    | P010       | DRG001     |
| C1002    | P011       | DRG002     |
| C1003    | P012       | DRG001     |

The claims table references the product dimension through **Product_ID**.

---

## Nuances That Must Be Understood

### 1. Dimension Tables Store Descriptive Attributes

Dimension tables typically contain **descriptive attributes used for filtering and grouping**.

Examples of common dimensions:

| Dimension | Example Attributes          |
| --------- | --------------------------- |
| Product   | Drug name, therapeutic area |
| Physician | Specialty, location         |
| Payer     | Organization name           |
| Geography | ZIP code, state, region     |
| Time      | Date, month, quarter        |

Fact tables store events such as claims or prescriptions.

Example structure:

Fact Table → Claims
Dimension Tables → Product, Patient, Physician, Payer

---

### 2. Duplicate Dimension Records Break Analytics

Example incorrect product dimension:

| Product_ID | Product_Name |
| ---------- | ------------ |
| DRG001     | Drug A       |
| DRG001     | Drug A       |

If duplicates exist, joining fact tables may produce duplicate rows.

Example join problem:

Claims table:

| Claim_ID | Product_ID |
| -------- | ---------- |
| C1001    | DRG001     |

Product table with duplicates:

| Product_ID | Product_Name |
| ---------- | ------------ |
| DRG001     | Drug A       |
| DRG001     | Drug A       |

Join result:

| Claim_ID | Product_Name |
| -------- | ------------ |
| C1001    | Drug A       |
| C1001    | Drug A       |

The claim appears twice, inflating analytics.

---

### 3. Attributes Should Not Conflict

Dimension attributes should remain consistent.

Incorrect example:

| Product_ID | Product_Name | Therapeutic_Area |
| ---------- | ------------ | ---------------- |
| DRG001     | Drug A       | Oncology         |
| DRG001     | Drug A       | Cardiology       |

This creates ambiguity about the drug’s therapeutic area.

---

### 4. Dimension Tables Should Be Relatively Small

Dimension tables usually contain far fewer records than fact tables.

Example size comparison:

| Table               | Typical Size             |
| ------------------- | ------------------------ |
| Claims Fact Table   | Millions of rows         |
| Product Dimension   | Tens or hundreds of rows |
| Physician Dimension | Thousands of rows        |

Because dimensions are small and frequently referenced, maintaining clean data is essential.

---

### 5. Dimension Tables Enable Efficient Aggregation

Clean dimensions allow accurate grouping in analytics.

Example query:

```sql
SELECT product_name, COUNT(*) AS claim_count
FROM claims
JOIN product_dimension
ON claims.product_id = product_dimension.product_id
GROUP BY product_name;
```

If the dimension contains duplicates, claim counts become inflated.

---

## Example Clean Dimension Tables

Product dimension:

| Product_ID | Product_Name | Therapeutic_Area |
| ---------- | ------------ | ---------------- |
| DRG001     | Drug A       | Oncology         |
| DRG002     | Drug B       | Diabetes         |

Physician dimension:

| HCP_ID | Specialty     | State |
| ------ | ------------- | ----- |
| H501   | Oncology      | MA    |
| H502   | Endocrinology | IL    |

Payer dimension:

| Payer_ID | Payer_Name       |
| -------- | ---------------- |
| PAY01    | Aetna            |
| PAY02    | UnitedHealthcare |

Each identifier appears only once.

---

## Data Quality Validation Checks

Detect duplicate dimension identifiers:

```sql
SELECT product_id, COUNT(*)
FROM product_dimension
GROUP BY product_id
HAVING COUNT(*) > 1;
```

Detect conflicting attributes:

```sql
SELECT product_id
FROM product_dimension
GROUP BY product_id
HAVING COUNT(DISTINCT therapeutic_area) > 1;
```

Detect orphan fact references:

```sql
SELECT c.product_id
FROM claims c
LEFT JOIN product_dimension p
ON c.product_id = p.product_id
WHERE p.product_id IS NULL;
```

These checks ensure dimension tables remain clean.

---

## Key Takeaway

Dimension tables act as the **reference layer of the data model**, providing descriptive attributes for analytical queries. Keeping these tables clean by enforcing unique identifiers and consistent attributes prevents incorrect joins, duplicate aggregations, and unreliable analytics.

---

## Points That May Require Verification or Are Context Dependent

Some dimension management practices vary depending on the warehouse architecture:

* Whether dimension tables support historical changes (Slowly Changing Dimensions)
* Whether surrogate keys or natural keys are used
* Whether reference data is maintained internally or sourced from external systems

These design choices should be documented as part of the dataset architecture.

