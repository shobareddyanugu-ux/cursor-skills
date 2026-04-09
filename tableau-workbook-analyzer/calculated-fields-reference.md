# Tableau Calculated Fields Reference

## Function Conversion Table

### Logical Functions

| Tableau | SQL (Databricks/Spark) | SQL (Redshift/PostgreSQL) |
|---------|------------------------|---------------------------|
| `IF cond THEN x ELSE y END` | `CASE WHEN cond THEN x ELSE y END` | Same |
| `IIF(cond, x, y)` | `CASE WHEN cond THEN x ELSE y END` | Same |
| `IFNULL(a, b)` | `COALESCE(a, b)` | Same |
| `ISNULL(a)` | `a IS NULL` | Same |
| `ZN(a)` | `COALESCE(a, 0)` | Same |
| `CASE WHEN ... END` | Same | Same |
| `AND`, `OR`, `NOT` | Same | Same |
| `[Field] IN ('a', 'b')` | `field IN ('a', 'b')` | Same |

### String Functions

| Tableau | SQL (Databricks/Spark) | SQL (Redshift/PostgreSQL) |
|---------|------------------------|---------------------------|
| `Contains([str], 'x')` | `str LIKE '%x%'` or `CONTAINS(str, 'x')` | `str LIKE '%x%'` or `POSITION('x' IN str) > 0` |
| `STARTSWITH([str], 'x')` | `str LIKE 'x%'` or `STARTSWITH(str, 'x')` | `str LIKE 'x%'` |
| `ENDSWITH([str], 'x')` | `str LIKE '%x'` or `ENDSWITH(str, 'x')` | `str LIKE '%x'` |
| `LEFT([str], n)` | `LEFT(str, n)` | Same |
| `RIGHT([str], n)` | `RIGHT(str, n)` | Same |
| `MID([str], start, len)` | `SUBSTRING(str, start, len)` | Same |
| `LEN([str])` | `LENGTH(str)` | Same |
| `UPPER([str])` | `UPPER(str)` | Same |
| `LOWER([str])` | `LOWER(str)` | Same |
| `TRIM([str])` | `TRIM(str)` | Same |
| `LTRIM([str])` | `LTRIM(str)` | Same |
| `RTRIM([str])` | `RTRIM(str)` | Same |
| `REPLACE([str], 'old', 'new')` | `REPLACE(str, 'old', 'new')` | Same |
| `SPLIT([str], 'delim', n)` | `SPLIT(str, 'delim')[n-1]` | `SPLIT_PART(str, 'delim', n)` |
| `[str] + [str2]` | `CONCAT(str, str2)` | Same |
| `REGEXP_EXTRACT([str], 'pattern')` | `REGEXP_EXTRACT(str, 'pattern')` | `REGEXP_SUBSTR(str, 'pattern')` |

### Date Functions

| Tableau | SQL (Databricks/Spark) | SQL (Redshift/PostgreSQL) |
|---------|------------------------|---------------------------|
| `DATETRUNC('month', [date])` | `DATE_TRUNC('month', date)` | Same |
| `DATEPART('year', [date])` | `YEAR(date)` | `DATE_PART('year', date)` |
| `DATEPART('month', [date])` | `MONTH(date)` | `DATE_PART('month', date)` |
| `DATEPART('day', [date])` | `DAY(date)` | `DATE_PART('day', date)` |
| `DATEPART('week', [date])` | `WEEKOFYEAR(date)` | `DATE_PART('week', date)` |
| `DATEPART('quarter', [date])` | `QUARTER(date)` | `DATE_PART('quarter', date)` |
| `DATEDIFF('day', [a], [b])` | `DATEDIFF(b, a)` | `DATEDIFF('day', a, b)` or `b - a` |
| `DATEADD('day', n, [date])` | `DATE_ADD(date, n)` | `DATEADD('day', n, date)` |
| `TODAY()` | `CURRENT_DATE` | Same |
| `NOW()` | `CURRENT_TIMESTAMP` | Same |
| `YEAR([date])` | `YEAR(date)` | `EXTRACT(YEAR FROM date)` |
| `MONTH([date])` | `MONTH(date)` | `EXTRACT(MONTH FROM date)` |
| `MAKEDATE(year, month, day)` | `MAKE_DATE(year, month, day)` | `TO_DATE(year\|\|'-'\|\|month\|\|'-'\|\|day, 'YYYY-MM-DD')` |

### Aggregate Functions

| Tableau | SQL Equivalent |
|---------|----------------|
| `SUM([field])` | `SUM(field)` |
| `AVG([field])` | `AVG(field)` |
| `MIN([field])` | `MIN(field)` |
| `MAX([field])` | `MAX(field)` |
| `COUNT([field])` | `COUNT(field)` |
| `COUNTD([field])` | `COUNT(DISTINCT field)` |
| `ATTR([field])` | `MAX(field)` (when expecting single value) |
| `MEDIAN([field])` | `PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY field)` |
| `STDEV([field])` | `STDDEV(field)` |
| `VAR([field])` | `VARIANCE(field)` |

### Table Calculations

| Tableau | SQL Window Function |
|---------|---------------------|
| `RUNNING_SUM(SUM([field]))` | `SUM(SUM(field)) OVER (ORDER BY ... ROWS UNBOUNDED PRECEDING)` |
| `RUNNING_AVG(SUM([field]))` | `AVG(SUM(field)) OVER (ORDER BY ... ROWS UNBOUNDED PRECEDING)` |
| `RUNNING_COUNT(SUM([field]))` | `COUNT(*) OVER (ORDER BY ... ROWS UNBOUNDED PRECEDING)` |
| `TOTAL(SUM([field]))` | `SUM(SUM(field)) OVER ()` |
| `SUM([field]) / TOTAL(SUM([field]))` | `SUM(field) / SUM(SUM(field)) OVER ()` (% of total) |
| `RANK(SUM([field]))` | `RANK() OVER (ORDER BY SUM(field) DESC)` |
| `RANK_DENSE(SUM([field]))` | `DENSE_RANK() OVER (ORDER BY SUM(field) DESC)` |
| `INDEX()` | `ROW_NUMBER() OVER (ORDER BY ...)` |
| `FIRST()` | `ROW_NUMBER() OVER (...) - 1` (0-based) |
| `LAST()` | `COUNT(*) OVER () - ROW_NUMBER() OVER (...)` |
| `LOOKUP(SUM([field]), n)` | `LAG(SUM(field), -n)` or `LEAD(SUM(field), n)` |
| `WINDOW_SUM(SUM([field]), -2, 2)` | `SUM(SUM(field)) OVER (ORDER BY ... ROWS BETWEEN 2 PRECEDING AND 2 FOLLOWING)` |

### Number Functions

| Tableau | SQL Equivalent |
|---------|----------------|
| `ABS([num])` | `ABS(num)` |
| `CEILING([num])` | `CEIL(num)` |
| `FLOOR([num])` | `FLOOR(num)` |
| `ROUND([num], decimals)` | `ROUND(num, decimals)` |
| `POWER([num], exp)` | `POWER(num, exp)` |
| `SQRT([num])` | `SQRT(num)` |
| `LOG([num])` | `LN(num)` (natural log) |
| `LOG([num], base)` | `LOG(base, num)` |
| `EXP([num])` | `EXP(num)` |
| `[num] % divisor` | `num % divisor` or `MOD(num, divisor)` |
| `INT([num])` | `CAST(num AS INT)` |
| `FLOAT([num])` | `CAST(num AS DOUBLE)` |

### Type Conversion

| Tableau | SQL Equivalent |
|---------|----------------|
| `STR([num])` | `CAST(num AS STRING)` |
| `INT([str])` | `CAST(str AS INT)` |
| `FLOAT([str])` | `CAST(str AS DOUBLE)` |
| `DATE([str])` | `CAST(str AS DATE)` |
| `DATETIME([str])` | `CAST(str AS TIMESTAMP)` |

## Special Handling

### Parameters

Tableau parameters in calculated fields appear as `[Parameters].[Parameter Name]`.

**Strategies:**
1. **Hardcode default**: Replace with the parameter's default value
2. **Document variants**: Create multiple queries for each parameter option
3. **Use SQL variables**: `SET @param = 'value';` then reference `@param`

### LOD Expressions

| Tableau LOD | SQL Equivalent |
|-------------|----------------|
| `{FIXED [dim]: SUM([measure])}` | Subquery or window function |
| `{INCLUDE [dim]: SUM([measure])}` | Add dimension to GROUP BY |
| `{EXCLUDE [dim]: SUM([measure])}` | Remove dimension from GROUP BY |

**FIXED example:**
```sql
-- Tableau: {FIXED [Customer]: SUM([Sales])}
SELECT customer, SUM(sales) AS customer_total
FROM data
GROUP BY customer

-- Then join back to detail
SELECT d.*, ct.customer_total
FROM data d
JOIN customer_totals ct ON d.customer = ct.customer
```

### Sets

Tableau sets become SQL IN clauses or CASE statements:

```sql
-- Tableau: IN [Top Customers Set]
CASE 
  WHEN customer IN (SELECT customer FROM top_customers) THEN 'In Set'
  ELSE 'Out of Set'
END
```

### Groups

Tableau groups become CASE statements:

```sql
-- Tableau group: Region Group
CASE
  WHEN region IN ('East', 'Northeast') THEN 'Eastern'
  WHEN region IN ('West', 'Southwest') THEN 'Western'
  ELSE 'Other'
END AS region_group
```
