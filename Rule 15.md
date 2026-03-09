## Rule 15: Maintain Consistent Units of Measure

### Rule Definition

All numeric fields in the dataset must use **consistent units of measure and standardized formats**. Fields representing monetary values, quantities, dosage amounts, or time intervals should follow a single unit system across the entire dataset.

Consistency in measurement units is essential because analytical systems assume that values are comparable across records. If different units are mixed within the same field, calculations such as totals, averages, and trends become incorrect.

Standardization of measurement units is a fundamental principle of data quality and interoperability in information systems, ensuring that data values are interpreted consistently across systems and analyses.

---

## Example Dataset

Example claims table with standardized currency values:

| Claim_ID | Paid_Amount |
| -------- | ----------- |
| C9001    | 1450.25     |
| C9002    | 980.50      |
| C9003    | 2120.75     |

All values are stored in the **same currency and format**.

---

## Nuances That Must Be Understood

### 1. Currency Values Must Use a Single Unit

Healthcare claims data frequently includes monetary fields such as:

* Paid amount
* Billed amount
* Copayment
* Reimbursement amount

All monetary values must be stored using a single currency.

Example correct structure:

| Claim_ID | Currency | Paid_Amount |
| -------- | -------- | ----------- |
| C1001    | USD      | 1200.50     |
| C1002    | USD      | 850.25      |

Incorrect example:

| Claim_ID | Paid_Amount |
| -------- | ----------- |
| C1001    | 1200 USD    |
| C1002    | 900 EUR     |

Mixing currencies without conversion causes incorrect financial reporting.

---

### 2. Dosage Measurements Must Use Consistent Units

Drug dosage fields must follow a standardized unit.

Common pharmaceutical dosage units include:

| Unit | Example |
| ---- | ------- |
| mg   | 50 mg   |
| ml   | 5 ml    |
| IU   | 10 IU   |

Example dataset:

| Prescription_ID | Drug   | Dose  |
| --------------- | ------ | ----- |
| RX100           | Drug A | 50 mg |
| RX101           | Drug A | 50 mg |

Incorrect example:

| Prescription_ID | Drug   | Dose   |
| --------------- | ------ | ------ |
| RX100           | Drug A | 50 mg  |
| RX101           | Drug A | 0.05 g |

Although mathematically equivalent, mixing units complicates analysis.

---

### 3. Quantity Fields Must Represent the Same Measurement

Fields such as **drug quantity dispensed** should represent the same measurement type across records.

Example:

| Claim_ID | Quantity_Dispensed |
| -------- | ------------------ |
| C8001    | 30                 |
| C8002    | 60                 |

If some records represent **tablet counts** and others represent **days supply**, analytics will be incorrect.

Incorrect example:

| Claim_ID | Quantity   |
| -------- | ---------- |
| C8001    | 30 tablets |
| C8002    | 30 days    |

These values are not directly comparable.

---

### 4. Time Units Should Be Standardized

Healthcare datasets frequently include time measurements such as:

* Treatment duration
* Length of stay
* Days supply
* Time to treatment

These fields should use a single unit.

Example consistent structure:

| Patient_ID | Treatment_Duration_Days |
| ---------- | ----------------------- |
| P001       | 45                      |
| P002       | 90                      |

Incorrect example:

| Patient_ID | Treatment_Duration |
| ---------- | ------------------ |
| P001       | 45 days            |
| P002       | 3 months           |

Mixing time units makes time-based analytics difficult.

---

### 5. Unit Consistency Supports Reliable Aggregation

Standardized units allow analysts to perform calculations such as totals and averages.

Example calculation:

Claim amounts:

1450.25
+980.50
+2120.75

Digit-by-digit addition:

1450.25
+980.50
= 2430.75

2430.75
+2120.75
= **4551.50 total**

If some records used different currencies or units, this calculation would be incorrect.

---

## Example Standardized Schema

Example claim structure with consistent measurement units:

| Field              | Unit    |
| ------------------ | ------- |
| Paid_Amount        | USD     |
| Quantity_Dispensed | Tablets |
| Dose               | mg      |
| Treatment_Duration | Days    |

This ensures all values are directly comparable.

---

## Data Quality Validation Checks

Detect inconsistent currency formats:

```sql
SELECT DISTINCT currency
FROM claims;
```

Detect dosage unit inconsistencies:

```sql
SELECT DISTINCT dose_unit
FROM prescription_table;
```

Detect mixed duration units:

```sql
SELECT DISTINCT duration_unit
FROM treatment_table;
```

These checks help verify measurement consistency.

---

## Key Takeaway

Numeric values in healthcare datasets must use **consistent measurement units** to ensure reliable calculations and reporting. Standardizing currency, dosage, quantity, and time units prevents analytical errors and simplifies aggregation across records.

---

## Points That May Require Verification or Are Context Dependent

Some measurement conventions may vary depending on dataset design:

* Currency used for financial fields
* Dosage measurement units for different drug forms
* Whether treatment duration is measured in days or weeks

These conventions should be defined clearly in the dataset schema documentation.

