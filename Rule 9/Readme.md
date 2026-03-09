## Rule 9: Maintain Geographic Consistency

### Rule Definition

Location attributes in the dataset must follow a **valid geographic hierarchy**, where smaller geographic units correctly map to larger administrative regions. Each location identifier should correspond to a valid parent region to ensure that geographic analytics produce accurate results.

Healthcare datasets frequently use location fields such as **ZIP code, city, state, and region** to analyze healthcare utilization, prescribing patterns, and patient demographics across geographic areas. Maintaining geographic consistency ensures that each record can be accurately aggregated and mapped.

Geographic identifiers such as ZIP codes are commonly used to group demographic and operational data for analysis, even though they were originally designed for mail routing. ([Wikipedia][1])

---

## Example Geographic Hierarchy

Typical geographic structure used in healthcare analytics:

ZIP Code → State → Region

Example:

| ZIP   | State | Region    |
| ----- | ----- | --------- |
| 02139 | MA    | Northeast |
| 60611 | IL    | Midwest   |
| 94107 | CA    | West      |

Each ZIP code must correspond to a valid state and region.

---

## Nuances That Must Be Understood

### 1. Geographic Data Is Hierarchical

Location data typically follows nested administrative layers.

Example hierarchy commonly used in analytics:

| Level    | Example       |
| -------- | ------------- |
| ZIP Code | 02139         |
| City     | Cambridge     |
| State    | Massachusetts |
| Region   | Northeast     |

Analytical reports often aggregate data upward along this hierarchy.

Example:

* Claims by ZIP
* Claims by state
* Claims by region

If the hierarchy is inconsistent, these rollups produce incorrect results.

---

### 2. Postal Codes Represent Geographic Delivery Areas

Postal codes such as ZIP codes represent **mail delivery zones used by postal services to route mail efficiently**. ([Wikipedia][1])

The digits in a ZIP code provide geographic structure:

* First digit represents a broad geographic region of the country
* Next digits narrow the location to regional processing areas and local delivery zones ([Woosmap][2])

Because these codes represent geographic groupings, they are often used as proxies for geographic analysis in datasets.

---

### 3. ZIP Codes May Not Perfectly Match Administrative Boundaries

Although ZIP codes are often used for geographic analysis, they are designed primarily for **mail routing efficiency rather than political boundaries**. As a result, some ZIP codes may cross city or county boundaries. ([GeoPostcodes][3])

However, for analytics purposes, datasets usually enforce a **consistent ZIP-to-state mapping** to maintain reliable geographic aggregation.

Example acceptable mapping:

| ZIP   | State |
| ----- | ----- |
| 10001 | NY    |
| 30301 | GA    |

Example inconsistent mapping:

| ZIP   | State |
| ----- | ----- |
| 10001 | NY    |
| 10001 | CA    |

This would break geographic reporting.

---

### 4. Geographic Consistency Enables Regional Analytics

Many healthcare analytics depend on geographic segmentation.

Examples include:

* Regional treatment adoption
* Provider density by state
* Market penetration by territory
* Healthcare utilization by ZIP code

Example reporting output:

| Region    | Claims |
| --------- | ------ |
| Northeast | 18,200 |
| Midwest   | 14,600 |
| South     | 21,300 |

If ZIP codes map to incorrect states or regions, these results become misleading.

---

### 5. Physician and Patient Locations Should Align

In many healthcare datasets, the geographic location of providers and patients is used to analyze treatment patterns.

Example structure:

| Patient_ZIP | Physician_ZIP |
| ----------- | ------------- |
| 02139       | 02138         |

These relationships allow analysts to study:

* Patient travel patterns
* Provider coverage areas
* Regional treatment variation

If geographic fields are inconsistent, these analyses cannot be performed reliably.

---

## Example Synthetic Geography Table

A reference table helps maintain geographic consistency.

| ZIP   | City      | State | Region    |
| ----- | --------- | ----- | --------- |
| 02139 | Cambridge | MA    | Northeast |
| 30301 | Atlanta   | GA    | South     |
| 60611 | Chicago   | IL    | Midwest   |

Claims should reference this table using the ZIP code.

---

## Data Quality Validation Checks

Detect ZIP codes mapped to multiple states:

```sql
SELECT zip_code
FROM geography_dimension
GROUP BY zip_code
HAVING COUNT(DISTINCT state) > 1;
```

Detect claims referencing unknown ZIP codes:

```sql
SELECT c.zip_code
FROM claims c
LEFT JOIN geography_dimension g
ON c.zip_code = g.zip_code
WHERE g.zip_code IS NULL;
```

These validation checks ensure geographic fields remain consistent across the dataset.

---

## Key Takeaway

Geographic attributes must follow a consistent hierarchy so that each location correctly maps to its parent administrative region. Maintaining this structure ensures that geographic aggregation, regional analysis, and healthcare utilization studies produce accurate and reliable insights.

---

## Points That May Require Verification or Are Context Dependent

Some aspects of geographic modeling depend on the intended dataset design:

* Whether ZIP codes are mapped to counties or regions
* How regional groupings are defined (for example Northeast, Midwest, South)
* How cross-boundary ZIP codes are handled in analytics systems

These decisions vary across organizations and should be documented in the dataset design.

[1]: https://en.wikipedia.org/wiki/ZIP_Code?utm_source=chatgpt.com "ZIP Code"
[2]: https://www.woosmap.com/blog/what-is-a-zip-code?utm_source=chatgpt.com "ZIP Code: History, Structure, and Importance in the US"
[3]: https://www.geopostcodes.com/blog/do-zip-codes-cross-state-lines/?utm_source=chatgpt.com "Do ZIP Codes Cross State Lines? - GeoPostcodes"

