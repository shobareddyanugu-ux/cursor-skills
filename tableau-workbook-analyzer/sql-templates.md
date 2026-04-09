# SQL Templates for Tableau Metric Extraction

## Base Template Structure

```sql
-- =====================================================================
-- [Dashboard Name] - Combined Metrics Query
-- Source: [Tableau Workbook Name]
-- Generated: [Date]
-- =====================================================================

-- =====================================================================
-- SECTION 1: BACKEND SQL (from Tableau Custom SQL)
-- =====================================================================
WITH backend_data AS (
  -- Paste the original Tableau Custom SQL here
  -- Usually found in: <relation type="text"><![CDATA[...]]></relation>
  SELECT * FROM source_table
),

-- =====================================================================
-- SECTION 2: CALCULATED FIELDS (converted from Tableau)
-- =====================================================================
calculated_fields AS (
  SELECT
    *,
    
    -- Dimension calculations
    CASE
      WHEN condition1 THEN 'Value1'
      WHEN condition2 THEN 'Value2'
      ELSE 'Other'
    END AS calculated_dimension,
    
    -- Count metrics (binary flags)
    CASE WHEN filter_condition THEN 1 ELSE 0 END AS metric_count_flag,
    
    -- Dollar metrics (conditional amounts)
    CASE WHEN filter_condition THEN amount_field ELSE 0 END AS metric_dollars
    
  FROM backend_data
),

-- =====================================================================
-- SECTION 3: FINAL METRICS (aggregated output)
-- =====================================================================
final_metrics AS (
  SELECT
    -- Dimensions
    dimension1,
    dimension2,
    calculated_dimension,
    
    -- Count aggregations
    SUM(metric_count_flag) AS metric_count,
    COUNT(DISTINCT id_field) AS unique_count,
    
    -- Dollar aggregations
    SUM(metric_dollars) AS total_dollars,
    
    -- Percentage calculations
    100.0 * SUM(metric_count_flag) / NULLIF(SUM(SUM(metric_count_flag)) OVER (), 0) AS pct_of_total
    
  FROM calculated_fields
  GROUP BY dimension1, dimension2, calculated_dimension
)

-- Final output
SELECT * FROM final_metrics
ORDER BY dimension1, dimension2;
```

## Common Metric Patterns

### Pattern 1: Conditional Count

**Tableau:**
```
IF [Status] = "Active" THEN 1 ELSE 0 END
```

**SQL:**
```sql
-- In calculated_fields CTE:
CASE WHEN status = 'Active' THEN 1 ELSE 0 END AS active_flag

-- In final_metrics CTE:
SUM(active_flag) AS active_count
```

### Pattern 2: Conditional Sum

**Tableau:**
```
IF [Category] = "Sales" THEN [Amount] ELSE 0 END
```

**SQL:**
```sql
-- In calculated_fields CTE:
CASE WHEN category = 'Sales' THEN amount ELSE 0 END AS sales_amount

-- In final_metrics CTE:
SUM(sales_amount) AS total_sales
```

### Pattern 3: Multiple Conditions (Parameter-driven)

**Tableau:**
```
IF [Parameters].[View Type] = "Option A"
    THEN SUM([Metric A])
ELSEIF [Parameters].[View Type] = "Option B"
    THEN SUM([Metric B])
END
```

**SQL (create all variants):**
```sql
-- In calculated_fields CTE:
metric_a,
metric_b,

-- In final_metrics CTE:
SUM(metric_a) AS metric_a_total,
SUM(metric_b) AS metric_b_total,
SUM(metric_a + metric_b) AS combined_total  -- If "Both" is an option
```

### Pattern 4: String Classification

**Tableau:**
```
IF Contains([Channel], "EMAIL") THEN 'Email'
ELSEIF Contains([Channel], "WEB") THEN 'Web'
ELSEIF [Channel] = 'Phone' THEN 'Phone'
ELSE 'Other'
END
```

**SQL:**
```sql
CASE
  WHEN channel LIKE '%EMAIL%' THEN 'Email'
  WHEN channel LIKE '%WEB%' THEN 'Web'
  WHEN channel = 'Phone' THEN 'Phone'
  ELSE 'Other'
END AS channel_group
```

### Pattern 5: Percent of Total (Table Calculation)

**Tableau:**
```
SUM([Count]) / TOTAL(SUM([Count]))
```

**SQL:**
```sql
-- In final_metrics CTE (with window function):
SUM(count_field) AS count_total,
ROUND(100.0 * SUM(count_field) / NULLIF(SUM(SUM(count_field)) OVER (), 0), 1) AS pct_of_total
```

### Pattern 6: Year-over-Year Comparison

**Tableau:**
```
{FIXED [Customer]: SUM(IF YEAR([Date]) = YEAR(TODAY()) THEN [Sales] END)}
{FIXED [Customer]: SUM(IF YEAR([Date]) = YEAR(TODAY())-1 THEN [Sales] END)}
```

**SQL:**
```sql
WITH customer_sales AS (
  SELECT
    customer,
    SUM(CASE WHEN YEAR(date) = YEAR(CURRENT_DATE) THEN sales ELSE 0 END) AS cy_sales,
    SUM(CASE WHEN YEAR(date) = YEAR(CURRENT_DATE) - 1 THEN sales ELSE 0 END) AS py_sales
  FROM sales_data
  GROUP BY customer
)
SELECT
  customer,
  cy_sales,
  py_sales,
  cy_sales - py_sales AS yoy_change,
  ROUND(100.0 * (cy_sales - py_sales) / NULLIF(py_sales, 0), 1) AS yoy_pct_change
FROM customer_sales
```

### Pattern 7: Running Total

**Tableau:**
```
RUNNING_SUM(SUM([Sales]))
```

**SQL:**
```sql
SELECT
  date,
  SUM(sales) AS daily_sales,
  SUM(SUM(sales)) OVER (ORDER BY date ROWS UNBOUNDED PRECEDING) AS running_total
FROM sales_data
GROUP BY date
ORDER BY date
```

### Pattern 8: Ranking

**Tableau:**
```
RANK(SUM([Sales]))
```

**SQL:**
```sql
SELECT
  category,
  SUM(sales) AS total_sales,
  RANK() OVER (ORDER BY SUM(sales) DESC) AS sales_rank,
  DENSE_RANK() OVER (ORDER BY SUM(sales) DESC) AS sales_dense_rank
FROM sales_data
GROUP BY category
```

## Filter Handling

### Converting Tableau Filters to SQL WHERE Clauses

```sql
-- Quick filters become WHERE conditions
WHERE 
  -- Date range filter
  date BETWEEN '2024-01-01' AND '2024-12-31'
  
  -- Dimension filter (multi-select)
  AND region IN ('East', 'West', 'Central')
  
  -- Exclude nulls
  AND product_group IS NOT NULL
  AND product_group <> 'Other'
  
  -- Measure filter (after aggregation - use HAVING)
HAVING
  SUM(sales) > 1000
```

## Output Variants

### Detail Level (CAN/Record Level)

```sql
SELECT
  -- All base dimensions
  id,
  date,
  customer,
  
  -- Calculated dimensions
  channel_group,
  
  -- Individual record metrics (not aggregated)
  active_flag,
  sales_amount
  
FROM calculated_fields
WHERE filter_conditions
ORDER BY date, customer;
```

### Summary Level (Aggregated)

```sql
SELECT
  -- Group by dimensions
  channel_group,
  region,
  
  -- Aggregated metrics
  COUNT(*) AS record_count,
  COUNT(DISTINCT customer) AS unique_customers,
  SUM(active_flag) AS active_count,
  SUM(sales_amount) AS total_sales,
  
  -- Calculated ratios
  ROUND(100.0 * SUM(active_flag) / COUNT(*), 1) AS active_pct
  
FROM calculated_fields
WHERE filter_conditions
GROUP BY channel_group, region
ORDER BY channel_group, region;
```

### Grand Total

```sql
SELECT
  'TOTAL' AS category,
  COUNT(*) AS record_count,
  SUM(sales_amount) AS total_sales
FROM calculated_fields
WHERE filter_conditions;
```

## Validation Query

Run this to verify your SQL matches Tableau:

```sql
-- Compare totals
SELECT
  'SQL Total' AS source,
  COUNT(*) AS records,
  SUM(metric) AS metric_total
FROM your_sql_output

UNION ALL

-- If you have Tableau export data
SELECT
  'Tableau Export' AS source,
  COUNT(*) AS records,
  SUM(metric) AS metric_total
FROM tableau_export_table;
```
