---
name: tableau-workbook-analyzer
description: >-
  Extract metrics, SQL queries, and calculated fields from Tableau workbooks (.twb/.twbx).
  Generates combined SQL with all metric definitions. Use when analyzing Tableau workbooks,
  extracting dashboard metrics, reverse-engineering Tableau SQL, or documenting dashboard logic.
---

# Tableau Workbook Analyzer

Extracts backend SQL, calculated fields, and metrics from Tableau workbooks to generate comprehensive documentation and combined SQL queries.

## Workflow

### Step 1: Extract the Workbook

**For .twbx files** (packaged workbooks):
```bash
# List contents
unzip -l "workbook.twbx"

# Extract the .twb file
unzip -p "workbook.twbx" "*.twb" > /tmp/workbook.twb
```

**For .twb files**: Use directly (they're XML).

### Step 2: Extract Backend SQL

Search for the Custom SQL query in the datasource:

```bash
# Find the SQL query location
grep -n "relation.*type=\"text\"" /tmp/workbook.twb

# The SQL is in CDATA blocks after <relation> tags
```

Look for patterns:
- `<relation connection='...' name='Custom SQL Query' type='text'><![CDATA[...SQL HERE...]]></relation>`
- Extract everything between `<![CDATA[` and `]]>`

### Step 3: Extract Calculated Fields

Search for column definitions with calculations:

```bash
# Find all calculated fields
grep -n "<column caption" /tmp/workbook.twb
```

Key patterns to extract:
- `<column caption='Field Name' ... name='[internal_name]' ...>`
- `<calculation class='tableau' formula='...' />`

### Step 4: Identify Dashboard Metrics

Look for worksheets and their measure usage:

```bash
# Find worksheets
grep "<worksheet name=" /tmp/workbook.twb

# Find dashboard
grep "<dashboard name=" /tmp/workbook.twb
```

### Step 5: Map Calculated Field Logic

For each calculated field, document:

| Component | How to Find |
|-----------|-------------|
| Display Name | `caption='...'` attribute |
| Internal Name | `name='[...]'` attribute |
| Data Type | `datatype='...'` attribute |
| Role | `role='measure'` or `role='dimension'` |
| Formula | Content of `<calculation formula='...'/>` |

### Step 6: Generate Combined SQL

Create a single SQL query that:
1. Uses the backend SQL as base CTEs
2. Converts Tableau calculated fields to SQL CASE statements
3. Produces final aggregated metrics

## Output Template

Generate three outputs:

### Output 1: Metrics List with Definitions

```markdown
## Dashboard Metrics

### Count Metrics
| Metric | Definition | Tableau Formula |
|--------|------------|-----------------|
| Metric Name | Business meaning | `IF condition THEN 1 ELSE 0 END` |

### Dollar Metrics
| Metric | Definition | Tableau Formula |
|--------|------------|-----------------|
| Metric Name | Business meaning | `SUM([field])` |

### Dimensions
| Dimension | Definition | Values |
|-----------|------------|--------|
| Dimension Name | What it represents | Value1, Value2, ... |
```

### Output 2: Business Summary

Explain what the dashboard measures in plain English:
- What business process it tracks
- Key questions it answers
- How metrics relate to each other

### Output 3: Combined SQL Query

```sql
-- Backend CTEs from Tableau datasource
WITH base_data AS (
  -- Original SQL here
),

-- Tableau calculated fields converted to SQL
metrics AS (
  SELECT
    *,
    -- Calculated field 1
    CASE WHEN condition THEN value ELSE 0 END AS metric_1,
    -- Calculated field 2
    CASE WHEN condition THEN value ELSE 0 END AS metric_2
  FROM base_data
)

-- Final aggregated output
SELECT
  dimension_1,
  dimension_2,
  SUM(metric_1) AS metric_1,
  SUM(metric_2) AS metric_2
FROM metrics
GROUP BY dimension_1, dimension_2;
```

## Tableau Formula to SQL Conversion

| Tableau | SQL Equivalent |
|---------|----------------|
| `IF cond THEN x ELSE y END` | `CASE WHEN cond THEN x ELSE y END` |
| `IFNULL(a, b)` | `COALESCE(a, b)` |
| `ISNULL(a)` | `a IS NULL` |
| `Contains(str, 'x')` | `str LIKE '%x%'` or `CONTAINS(str, 'x')` |
| `[Field] <> 'value'` | `field <> 'value'` |
| `SUM([Field])` | `SUM(field)` |
| `COUNTD([Field])` | `COUNT(DISTINCT field)` |
| `ATTR([Field])` | `MAX(field)` or `MIN(field)` |
| `ZN([Field])` | `COALESCE(field, 0)` |
| `IIF(cond, x, y)` | `CASE WHEN cond THEN x ELSE y END` |
| `DATETRUNC('month', [Date])` | `DATE_TRUNC('month', date)` |
| `DATEDIFF('day', a, b)` | `DATEDIFF(day, a, b)` or `b - a` |
| `[Parameters].[Param]` | Use as filter or variable |

## Parameter Handling

Tableau parameters become SQL variables or filter conditions:

```sql
-- Option 1: Hardcode the default value
WHERE status = 'Active'  -- Parameter default was 'Active'

-- Option 2: Create variants for each parameter value
-- Parameter: Committed/Transacted with values: 'Committed Only', 'Transacted Only', 'Committed + Transacted'

-- For 'Committed + Transacted':
SUM(committed_count + transacted_count) AS count

-- For 'Transacted Only':
SUM(transacted_count) AS count
```

## Common Patterns

### Conditional Counts
Tableau:
```
IF [cart_type] = "TRANSACTED" THEN 1 ELSE 0 END
```
SQL:
```sql
CASE WHEN cart_type = 'TRANSACTED' THEN 1 ELSE 0 END AS transacted_count
```

### Conditional Sums
Tableau:
```
IF [cart_type] = "TRANSACTED" THEN [net_price] ELSE 0 END
```
SQL:
```sql
CASE WHEN cart_type = 'TRANSACTED' THEN net_price ELSE 0 END AS transacted_dollars
```

### String Mapping
Tableau:
```
IF Contains([channel], "EMAIL") THEN 'Email'
ELSEIF [channel] = 'Web' THEN 'Web Direct'
ELSE 'Other'
END
```
SQL:
```sql
CASE
  WHEN channel LIKE '%EMAIL%' THEN 'Email'
  WHEN channel = 'Web' THEN 'Web Direct'
  ELSE 'Other'
END AS channel_group
```

### Table Calculations (% of Total)
Tableau:
```
SUM([Count]) / TOTAL(SUM([Count]))
```
SQL:
```sql
100.0 * SUM(count) / SUM(SUM(count)) OVER () AS pct_of_total
```

## Validation Checklist

After generating the combined SQL, verify:

- [ ] All calculated fields from Tableau are converted to SQL
- [ ] Parameter logic is handled (hardcoded or documented)
- [ ] Data types match (dates, numbers, strings)
- [ ] Aggregation levels are correct (detail vs summary)
- [ ] NULL handling matches Tableau behavior
- [ ] Filter conditions are included
- [ ] Output columns match dashboard metrics

## Additional Resources

For complex workbooks, see:
- [calculated-fields-reference.md](calculated-fields-reference.md) - Common Tableau functions
- [sql-templates.md](sql-templates.md) - Reusable SQL patterns
