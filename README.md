>In SQL, we have to learn about mainly 5 things which are queries, joins, functions, optimization, and transactions.

# SQL Query Examples and DDL Overview

## 1. Select all columns from the customers table

```sql
select * from dbo.customers
```

## 2. Select only the first_name column, but rename it as 'name'

```sql
select first_name as name from dbo.customers
```

## 3. Select all customers except those from Germany

```sql
select * from dbo.customers where country != 'Germany'
```

## 4. Order customers by their score (ascending by default)

```sql
select * from dbo.customers order by score
```

## 5. Order customers by country (ascending), and within each country by score (descending)

```sql
select * from dbo.customers order by country asc, score desc
```

## 6. Group by country, sum the scores for each country where score >= 400, and only show countries with a total score >= 800

```sql
select country, sum(score) as total_score
from dbo.customers
where score >= 400
group by country
having sum(score) >= 800
```

## 7. Key difference between HAVING and WHERE

- **WHERE** filters rows before aggregation (before GROUP BY)
- **HAVING** filters groups after aggregation

```sql
select country, avg(score) as avg_score
from dbo.customers
where score != 0
group by country
having avg(score) > 430
```

## 8. DISTINCT removes duplicate values from the result set

```sql
select distinct country from dbo.customers
```

## 9. TOP is used to limit the number of rows returned

- Use `ORDER BY DESC` for highest values, `ASC` for lowest.

```sql
select top 5 * from dbo.customers order by score desc
```

## 10. Typical SQL query order

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. DISTINCT
7. ORDER BY
8. TOP

## 11. Example: Get top 3 countries (by sum of score), where score != 0, grouped by country, having sum(score) >= 430, ordered by score ascending

```sql
select top 3 country, sum(score) as score_max
from dbo.customers
where score != 0
group by country
having sum(score) >= 430
order by score_max asc;
```

---

## SQL Query Execution and Coding Order

1. **FROM**: Specifies the source table(s) for the query.
2. **WHERE**: Filters rows before any grouping or aggregation.
3. **GROUP BY**: Groups rows sharing a property so aggregate functions can be applied.
4. **HAVING**: Filters groups after aggregation.
5. **SELECT**: Chooses which columns or expressions to return.
6. **DISTINCT**: Removes duplicate rows from the result set.
7. **ORDER BY**: Sorts the result set.
8. **TOP**: Limits the number of rows returned (applied last).

> **Note:** Although you write SELECT first, SQL executes in the above logical order.

---

# DDL (Data Definition Language) Overview

DDL commands are used to define, modify, and remove database structures such as tables, schemas, and indexes.

**Common DDL commands:** `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`

---

## 1. CREATE: Used to create a new table or database object

```sql
create table dbo.customers (
  id int primary key,
  first_name varchar(50),
  last_name varchar(50),
  country varchar(50),
  score int
);
```

## 2. ALTER: Used to modify an existing database object

```sql
alter table dbo.customers add email varchar(100);
alter table dbo.customers drop column email;
```

## 3. DROP: Used to delete an entire table or database object

```sql
drop table dbo.customers;
```

## 4. TRUNCATE: Removes all rows from a table, but keeps the table structure

```sql
truncate table dbo.customers;
```

## 5. RENAME: Changes the name of a table or column (syntax may vary by SQL dialect)

**SQL Server:**
```sql
exec sp_rename 'dbo.customers', 'dbo.clients';
```
**MySQL:**
```sql
rename table customers to clients;
```

> DDL commands are auto-committed, meaning changes are permanent and cannot be rolled back.

---

## 6. INDEX: Used to create or drop indexes to improve query performance

- Indexes are special lookup tables that the database search engine can use to speed up data retrieval.
- Creating an index on a column (or columns) allows queries that filter or sort by those columns to run faster.
- Dropping an index removes this optimization.

```sql
-- Create index
create index idx_customers_country on dbo.customers(country);

-- Drop index
drop index idx_customers_country on dbo.customers;
```

## 7. SCHEMA: Used to create or drop schemas (namespaces for database objects)

- A schema is a logical container for database objects such as tables, views, and procedures.
- Creating a schema helps organize and group related objects, while dropping a schema removes it (and possibly its contents).

```sql
-- Create schema
create schema sales;

-- Drop schema
drop schema sales;
```

---

## Example DML and DDL Operations

### Insert Example

```sql
-- Insert into 'sai' table (example, columns and values must match)
-- insert into sai(column1, column2, ...) values(value1, value2, ...)
```

### Create Table Example

```sql
/* 
create table persons(
  id int not null primary key,
  first_name varchar(10),
  db0 date not null,
  phone int not null
);
*/
-- In DDL, you can use SERIAL or IDENTITY(1,1) for auto-incrementing IDs
```

### Insert Data Example

```sql
-- insert into customers values(7, 'sai', 'insia', 45)
-- select * from customers

insert into persons (id, first_name, db0, phone)
select id, first_name, null, null from customers
```

### Notes on NULL Handling

- When checking for NULL in string columns, use `IS NULL`.
- For numeric columns, use the number directly.

### Update and Delete Examples

```sql
-- Update single or multiple rows (example)
-- update customers set country = 'India' where id = 1

-- Delete customers where id > 5
delete from customers where id > 5;

-- View all customers
select * from customers;

-- Delete all rows from persons
delete from persons;
```

### Truncate Table

```sql
-- TRUNCATE is faster than DELETE for removing all rows
truncate table persons;
```

---

## WHERE Conditions and Operators

The `WHERE` clause is used to filter records in SQL queries. You can use various operators to specify your conditions.

### 1. Update with WHERE

```sql
update <table_name> set <column>=<value> where <condition>;
```

### 2. Comparison Operators

- `=` : Equal to
- `<>` or `!=` : Not equal to
- `>` : Greater than
- `<` : Less than
- `>=` : Greater than or equal to
- `<=` : Less than or equal to

**Examples:**

```sql
select * from dbo.customers where country <> 'Germany' order by score desc;
select * from dbo.customers where score >= 500;
```

### 3. Logical Operators

- `AND` : Both conditions must be true
- `OR` : At least one condition must be true
- `NOT` : Negates a condition

**Examples:**

```sql
select * from dbo.customers where score > 500 and country = 'USA';
select * from dbo.customers where score > 500 or country = 'USA';
select * from dbo.customers where not (score > 500 and country = 'USA');
```

### 4. Range Operator

- `BETWEEN ... AND ...` : Checks if a value is within a range (inclusive)

**Example:**

```sql
select * from dbo.customers where score between 100 and 500;
```

### 5. Membership Operator

- `IN (...)` : Checks if a value matches any value in a list
- `NOT IN (...)` : Checks if a value does not match any value in a list

**Examples:**

```sql
select * from dbo.customers where country in ('USA', 'UK');
select * from dbo.customers where country not in ('USA', 'UK');
```

### 6. Search Operator

- `LIKE` : Used for pattern matching with `%` (any number of characters) and `_` (single character)

**Example:**

```sql
select * from dbo.customers where first_name like '__r__';
```

---

## Combining Data: Joins and Set Operations

You can combine data from multiple tables in two main ways:

- **Joins (by columns):** Combine rows based on related columns (keys).
- **Set operations (by rows):** Combine result sets with the same columns.

### Types of Joins

1. **No Join:**  
   Select from tables independently.
   ```sql
   select * from customers;
   select * from orders;
   ```

2. **Inner Join:**  
   Returns only rows with matching keys in both tables.
   ```sql
   select * from customers
   join orders on customers.id = orders.customer_id;
   ```
   - `INNER JOIN` is the default if you just write `JOIN`.

3. **Left Join (Left Outer Join):**  
   Returns all rows from the left table, plus matching rows from the right table (if any).
   ```sql
   select * from customers
   left join orders on customers.id = orders.customer_id;
   ```

4. **Right Join (Right Outer Join):**  
   Returns all rows from the right table, plus matching rows from the left table (if any).
   ```sql
   select * from customers
   right join orders on customers.id = orders.customer_id;
   ```

5. **Full Join (Full Outer Join):**  
   Returns all rows from both tables, with matches where possible.
   ```sql
   select * from customers
   full join orders on customers.id = orders.customer_id;
   ```

### Advanced Joins

- **Left Anti Join:**  
  Returns rows from the left table with no match in the right table.
  ```sql
  select * from customers
  left join orders on customers.id = orders.customer_id
  where orders.customer_id is null;
  ```
  > Example: Customers who did not place any orders.

- **Right Anti Join:**  
  Returns rows from the right table with no match in the left table.
  ```sql
  select * from customers
  right join orders on customers.id = orders.customer_id
  where customers.id is null;
  ```
  Or:
  ```sql
  select * from orders
  left join customers on customers.id = orders.customer_id
  where customers.id is null;
  ```

- **Full Anti Join:**  
  Returns only rows that do not have a match in the other table.
  ```sql
  select * from orders
  full join customers on customers.id = orders.customer_id
  where customers.id is null or orders.customer_id is null;
  ```

- **Anti Inner Join:**  
  (Opposite of full join; rarely used term)  
  Returns only rows where both sides have non-null matches.
  ```sql
  select * from orders
  inner join customers on customers.id = orders.customer_id
  where customers.id is not null and orders.customer_id is not null;
  ```

- **Cross Join:**  
  Returns all possible combinations of rows from both tables (Cartesian product).
  ```sql
  select * from customers
  cross join orders;
  ```

---

## Example: Complex SELECT with Multiple LEFT JOINs

This query retrieves order details, customer info, product info, sales amount, and employee info for each order.

```sql
SELECT 
    o.orderID,
    c.FirstName AS customer_firstname,
    c.LastName AS customer_lastname,
    p.product,
    o.Sales,
    p.Price,
    e.FirstName AS employee_firstname,
    e.LastName AS employee_lastname
FROM sales.orders AS o
LEFT JOIN sales.customers AS c ON o.customerID = c.customerID
LEFT JOIN sales.products AS p ON o.productID = p.productID
LEFT JOIN sales.employees AS e ON o.salespersonID = e.employeeID;
```

**Example output:**

| orderID | customer_firstname | customer_lastname | product | Sales | Price | employee_firstname | employee_lastname |
|---------|-------------------|-------------------|---------|-------|-------|-------------------|------------------|
| 1       | John              | Doe               | Laptop  | 1000  | 900   | Alice             | Smith            |

---

## Set Operators in SQL

Set operators combine results from two or more `SELECT` statements.  
**Note:** The number and order of columns must match in all `SELECT`s.

### 1. `UNION` (removes duplicates)

```sql
SELECT FirstName FROM Sales.Employees
UNION
SELECT FirstName FROM Sales.Customers;
```

**Example output:**

| FirstName |
|-----------|
| John      |
| Alice     |
| Bob       |

---

### 2. `UNION ALL` (keeps duplicates)

```sql
SELECT FirstName FROM Sales.Employees
UNION ALL
SELECT FirstName FROM Sales.Customers;
```

**Example output:**

| FirstName |
|-----------|
| John      |
| Alice     |
| John      |  -- duplicate allowed

---

### 3. `EXCEPT` (rows in first SELECT not in second)

```sql
SELECT FirstName FROM Sales.Employees
EXCEPT
SELECT FirstName FROM Sales.Customers;
```
#### we can also use anti left join for the same
---
**Example output:**

| FirstName |
|-----------|
| Alice     |  -- Only in Employees, not in Customers

**Equivalent using LEFT JOIN:**

```sql
SELECT E.FirstName, E.LastName
FROM Sales.Employees E
LEFT JOIN Sales.Customers C ON E.FirstName = C.FirstName
WHERE C.CustomerID IS NULL;
```

---

### 4. `INTERSECT` (rows present in both SELECTs)

```sql
SELECT FirstName, LastName FROM Sales.Employees
INTERSECT
SELECT FirstName, LastName FROM Sales.Customers;
```
#### its same as inner join
---
**Example output:**

| FirstName | LastName |
|-----------|----------|
| John      | Doe      |  -- Exists in both tables

**Equivalent using JOIN:**

```sql
SELECT c.FirstName, c.LastName
FROM Sales.Employees e
JOIN Sales.Customers c ON c.FirstName = e.FirstName AND c.LastName = e.LastName;
```

---

> **Tip:** For static data (small, unchanging reference tables), set operators can optimize queries.


>now we need to learn about **SQL_FUNCTIONS** which has 2 types 1) single-row functions 2) multi-row functions

---

# SQL Functions Overview

SQL functions are built-in operations that process data and return a value. They are categorized into:

1. **Single-row functions** (Scalar functions): Operate on each row and return one result per row.
2. **Multi-row functions** (Aggregate functions): Operate on groups of rows and return a single result for the group.

## 1. Single-row (Scalar) Functions

These functions process individual values and return a result for each row.

**Examples:**

- **String functions:**  
  - `UPPER(column)`, `LOWER(column)`, `LEN(column)`, `SUBSTRING(column, start, length)`
- **Numeric functions:**  
  - `ABS(number)`, `ROUND(number, decimals)`, `CEILING(number)`, `FLOOR(number)`
- **Date functions:**  
  - `GETDATE()`, `DATEADD(day, 1, date)`, `DATEDIFF(day, start, end)`
- **NULL functions:**  
  - `ISNULL(column, default)`, `COALESCE(column1, column2, ...)`

**Example usage:**

```sql
select first_name, upper(first_name) as upper_name, len(first_name) as name_length
from dbo.customers;
```

## 2. Multi-row (Aggregate) Functions

These functions process multiple rows and return a single value for a group.

**Common aggregate functions:**

- `SUM(column)`
- `AVG(column)`
- `COUNT(column)`
- `MIN(column)`
- `MAX(column)`

**Example usage:**

```sql
select country, avg(score) as avg_score, count(*) as num_customers
from dbo.customers
group by country;
```

> **Note:** Aggregate functions are often used with `GROUP BY` and can be filtered using `HAVING`.

and window functions

---

## Window Functions

Window functions perform calculations across a set of table rows that are related to the current row, without collapsing the result set. They are often used for ranking, running totals, moving averages, and similar calculations.

**Syntax:**
```sql
<function>() OVER ([PARTITION BY ...] ORDER BY ...)
```

**Common window functions:**
- `ROW_NUMBER()`
- `RANK()`
- `DENSE_RANK()`
- `NTILE(n)`
- `SUM()`, `AVG()`, `MIN()`, `MAX()` (as windowed aggregates)
- `LEAD()`, `LAG()`

**Example: Ranking employees by salary within each department**
```sql
SELECT FirstName, LastName, Salary,
       RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS SalaryRank
FROM dbo.Employees;
```

**Example: Running total of sales by date**
```sql
SELECT OrderDate, Sales,
       SUM(Sales) OVER (ORDER BY OrderDate) AS RunningTotal
FROM dbo.Orders;
```

**Example: Previous row value (LAG)**
```sql
SELECT OrderID, Sales,
       LAG(Sales, 1) OVER (ORDER BY OrderID) AS PrevOrderSales
FROM dbo.Orders;
```

> Window functions do not reduce the number of rows returned; they add calculated columns based on the window specification.

---

# Common SQL Functions: String, Number, and Date/Time

## String Functions

String functions allow you to manipulate and extract text data.

- **Concatenation:** Combine strings together.
  ```sql
  select first_name, country, concat(first_name, ' ', country) as full_name from customers;
  ```

- **UPPER / LOWER:** Convert text to upper or lower case.
  ```sql
  select upper(first_name) from customers;
  select lower(first_name) from customers;
  select first_name, country, upper(concat(first_name, ' ', country)) as full_name from customers;
  ```

- **TRIM:** Remove extra spaces from both sides of a string.
  ```sql
  select trim(first_name), len(first_name) as length, len(trim(first_name)) as updated_length
  from customers
  where first_name != trim(first_name);
  ```

- **REPLACE:** Replace specific characters in a string.
  ```sql
  select '123-456-7889' as phone, replace('123-456-7889', '-', '') as clean_data;
  ```

- **LEN:** Get the length of a string.
  ```sql
  select len(trim(first_name)) from customers;
  select len('123456789');
  select len('-123456789');
  ```

- **LEFT / RIGHT:** Extract characters from the left or right of a string.
  ```sql
  select left(trim(first_name), 2) from customers;
  select right(trim(first_name), 3) from customers;
  ```

- **SUBSTRING:** Extract a substring from a specific position.
  ```sql
  select substring(trim(first_name), 2, len(first_name)) from customers;
  ```

---

## Number Functions

Number functions help with numeric calculations and formatting.

- **ROUND:** Round a number to a specified number of decimal places.
- **ABS:** Get the absolute (positive) value of a number.

```sql
select 4.344, round(4.344, 2) as rounded_value, abs(-34) as absolute_value;
```

---

## Date & Time Functions

Date and time functions extract or manipulate date/time values.

- **GETDATE():** Get the current date and time.
  ```sql
  select getdate() as current_datetime from sales.orders;
  ```

- **Extract day, month, year:**
  ```sql
  select orderid, day(creationtime) as day, month(creationtime) as month, year(creationtime) as year
  from sales.orders;
  ```

- **DATEPART:** Extract a specific part of a date.
  ```sql
  select datepart(year, creationtime) from sales.orders;
  select datepart(mm, creationtime) from sales.orders;
  select datepart(day, creationtime) from sales.orders;
  select datepart(week, creationtime) from sales.orders;
  select datepart(quarter, creationtime) from sales.orders;
  ```

- **DATENAME:** Get the name of a date part (e.g., month name).
  ```sql
  select datename(month, creationtime) from sales.orders;
  select datename(weekday, creationtime) from sales.orders;
  select datename(week, creationtime) from sales.orders;
  ```

- **DATETRUNC:** Truncate a date to a specified part (e.g., month, year).
  ```sql
  select datetrunc(month, creationtime) from sales.orders;
  select datetrunc(year, creationtime) from sales.orders;
  select datetrunc(day, creationtime) from sales.orders;
  ```

- **EOMONTH:** Get the last day of the month for a date.
  ```sql
  select month(eomonth(creationtime)) from sales.orders;
  ```

- **Aggregate by month name:**
  ```sql
  select datename(month, orderdate) as month_name, count(*) as no_of_orders
  from sales.orders
  group by datename(month, orderdate);

  select * from sales.orders where datename(month, orderdate) = 'February';
  ```

---

## Date Formatting and Conversion in SQL

You can change date formats and convert between data types using built-in SQL functions.

### 1. FORMAT: Change the display format of a date

```sql
-- Format a date as 'Day ddd MMM Q<quarter> yyyy hh:mm:ss tt'
select creationtime,
       'Day ' + format(creationtime, 'ddd MMM') +
       ' Q' + datename(quarter, creationtime) +
       format(creationtime, ' yyyy hh:mm:ss tt') as formatted_date
from sales.orders;
```

### 2. CONVERT: Convert between data types (e.g., string to int, datetime to date)

```sql
-- Convert string to int, datetime to date, and extract year part
select convert(int, '123') as as_integer,
       convert(date, creationtime) as as_date,
       datepart(year, creationtime) as year_part
from sales.orders;
```

### 3. CAST: Change one data type to another

```sql
-- Cast integer to varchar, datetime to date
select cast(145 as varchar) as as_text,
       cast(creationtime as date) as as_date
from sales.orders;
```

### 4. DATEADD: Add intervals to a date

```sql
-- Add years, months, or days to a date
select orderdate,
       dateadd(year, 2, orderdate) as plus_2_years,
       dateadd(month, 2, orderdate) as plus_2_months,
       dateadd(day, 2, orderdate) as plus_2_days
from sales.orders;
```

### 5. DATEDIFF: Calculate the difference between two dates

```sql
-- Number of days between orderdate and shipdate
select orderdate, shipdate,
       datediff(day, orderdate, shipdate) as days_between
from sales.orders;
```

### 6. GETDATE: Get the current date and time

```sql
select getdate() as current_datetime;
```

### 7. ISDATE: Check if a value is a valid date

```sql
-- Returns 1 if valid date, 0 otherwise
select isdate('2024-06-01') as is_valid_date;
select isdate('not-a-date') as is_valid_date;
```

---

## Handling NULLs in SQL

NULLs represent missing or unknown values in SQL. They are common in optional fields.

### 1. Replacing NULLs with Actual Data

- Use `ISNULL` (SQL Server) or `COALESCE` (standard SQL) to replace NULLs with a specified value.

```sql
-- Replace NULL ShipAddress with 'YAITSEMPTY' (in uppercase)
SELECT ISNULL(ShipAddress, UPPER('yaitsempty')) AS ShipAddress FROM sales.orders;

-- Replace NULL shipaddress with billaddress, or 'unknown' if both are NULL
SELECT COALESCE(shipaddress, billaddress, 'unknown') AS Address FROM sales.orders;
```

### 2. Replacing Existing Data with NULL

- Use `NULLIF` to return NULL if two expressions are equal.

```sql
-- Returns NULL if score is 0, otherwise returns score
SELECT NULLIF(score, 0) AS score_or_null FROM sales.Customers;
```

### 3. Checking for NULLs

- Use `IS NULL` and `IS NOT NULL` to filter or check for NULL values.

```sql
-- Find orders with missing ShipAddress
SELECT * FROM sales.orders WHERE ShipAddress IS NULL;

-- Find customers with a known score
SELECT * FROM sales.Customers WHERE score IS NOT NULL;
```

### 4. Aggregating and Ordering with NULLs

- Use `ISNULL` or `COALESCE` inside aggregates to treat NULLs as a default value.

```sql
-- Average score, treating NULL as 0
SELECT customerid, AVG(ISNULL(score, 0)) OVER() AS avg_score FROM sales.Customers;
```

- Order by NULLs last (SQL Server example):

```sql
SELECT customerid, score
FROM sales.Customers
ORDER BY CASE WHEN score IS NULL THEN 1 ELSE 0 END, score;
```

### 5. Notes

- `ISNULL` and `COALESCE` replace NULLs with actual data.
- `NULLIF` replaces existing data with NULL if a condition is met.
- `IS NULL` and `IS NOT NULL` are used to check for NULLs in queries.
- When using `IS NULL`, the result is TRUE (actual data) or FALSE (NULL). If the condition is satisfied, it returns the actual data; otherwise, NULL.
- `IS NOT NULL` is the opposite of `IS NULL`.

---
# -> ALL Examples for the single row functions

| Function      | Example SQL                                                                                                                                         | Description                                      |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|
| LEN           | `select first_name, country, len(country) as [just length man] from customers;`                                                                     | Get length of a string                           |
| LEFT          | `select left(first_name, 3) as [na i dont know whts it] from customers;`                                                                            | Get leftmost N characters                        |
| SUBSTRING     | `select substring(first_name, 2, 3) as [substring] from customers;`                                                                                 | Extract substring from position                  |
| REPLACE/CONCAT/UPPER/LOWER | `select replace(CONCAT(upper(first_name), '-', lower(country)), '-', '___') as FULL_NAME from customers;`                             | String manipulation and replacement              |
| ROUND         | `select round(3.234333, 4) as rounded_value;`                                                                                                       | Round a number                                   |
| ABS           | `select abs(-10) as absolute_value;`                                                                                                                | Absolute value                                   |
| DAY/MONTH/YEAR| `select day(getdate()), month(getdate()), year(getdate());`                                                                                        | Extract day, month, year from current date       |
| DATEPART      | `select datepart(day, getdate()), datepart(month, getdate()), datepart(year, getdate()), datepart(weekday, getdate()), datepart(quarter, getdate());` | Extract parts of date/time                       |
| DATENAME      | `select datename(day, getdate()), datename(month, getdate()), datename(week, getdate()), datename(weekday, getdate());`                             | Get name of date part                            |
| DATEADD       | `select day(dateadd(day, 2, getdate())), day(getdate()), month(dateadd(month, 2, getdate())), month(getdate());`                                   | Add interval to date                             |
| DATEDIFF/ABS  | `select abs(datediff(day, day(dateadd(day, 2, getdate())), day(getdate())));`                                                                      | Difference between dates (absolute value)        |


>```sql
-- ISNULL: Replace NULL values with a default value (SQL Server specific)
select isnull(score, 'unknown') as clean_score from customers;

-- COALESCE: Return first non-NULL value from a list (SQL Standard)
select coalesce(score, billi, 0) as final_score from customers;

-- NULLIF: Return NULL if two values are equal, otherwise return first value
select nullif(score, 0) as score_or_null from customers;

-- IS NULL and IS NOT NULL: Check for NULL values in WHERE clause
select * from customers where score IS NULL;
select * from customers where score IS NOT NULL;
```


--case 
--case 
--	when condition1 then 1 else 0
--end

## Using CASE Statements in SQL

The `CASE` statement allows you to add conditional logic to your SQL queries. It works like an IF-THEN-ELSE statement.

**Syntax:**
```sql
CASE 
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE default_result
END
```

**Example: Categorize customers based on score**
```sql
SELECT first_name, score,
    CASE
        WHEN score >= 800 THEN 'High'
        WHEN score >= 400 THEN 'Medium'
        ELSE 'Low'
    END AS score_category
FROM dbo.customers;
```

This will output each customer's name, score, and a category ("High", "Medium", or "Low") based on their score.

---

## Aggregate Functions (GROUP BY)

Aggregate functions perform calculations on groups of rows and return a single result per group.

**Common aggregate functions:**
- `COUNT(*)` - Count number of rows
- `SUM(column)` - Total sum of values
- `AVG(column)` - Average of values
- `MAX(column)` - Highest value
- `MIN(column)` - Lowest value

### Basic Aggregation (All Rows)

```sql
-- Get overall statistics for all orders
SELECT 
    COUNT(*) AS total_orders,
    AVG(sales) AS avg_sales,
    SUM(sales) AS total_sales,
    MAX(sales) AS highest_sale,
    MIN(sales) AS lowest_sale
FROM orders;
```

**Example Output:**
| total_orders | avg_sales | total_sales | highest_sale | lowest_sale |
|--------------|-----------|-------------|--------------|-------------|
| 1000         | 485.50    | 485500.00   | 2500.00      | 25.00       |

### Grouped Aggregation (GROUP BY)

```sql
-- Get statistics grouped by order_id
SELECT 
    order_id,
    COUNT(*) AS items_per_order,
    AVG(sales) AS avg_item_price,
    SUM(sales) AS order_total,
    MAX(sales) AS most_expensive_item,
    MIN(sales) AS cheapest_item
FROM orders
GROUP BY order_id;
```

**Example Output:**
| order_id | items_per_order | avg_item_price | order_total | most_expensive_item | cheapest_item |
|----------|-----------------|----------------|-------------|---------------------|---------------|
| 1001     | 3               | 150.00         | 450.00      | 200.00              | 100.00        |
| 1002     | 2               | 300.00         | 600.00      | 400.00              | 200.00        |

---

## Window Function Examples and Explanations

Window functions are powerful tools for analytics in SQL. They allow you to perform calculations across sets of rows related to the current row, without grouping or collapsing the result set. Below are some practical examples with explanations.

---

## 1. Running Total by Product

**Query:**
```sql
SELECT 
    sales,
    productid,
    SUM(sales) OVER (PARTITION BY productid ORDER BY sales, orderdate) AS running_total
FROM sales.orders;
```
**Explanation:**  
- `SUM(sales) OVER (...)` calculates a running total of sales for each product (`PARTITION BY productid`).
- The `ORDER BY sales, orderdate` defines the order in which the running total is calculated.
- Each row shows the cumulative sales for its product up to that point.

---

## 2. Moving Total, Running Total, Rolling Total, Moving Average

These terms refer to calculations over a "window" of rows.

- **Moving Total / Running Total:** Cumulative sum up to the current row.
- **Rolling Total:** Sum over a fixed number of previous rows.
- **Moving Average:** Average over a fixed number of previous rows.

**Example: Moving Average of Sales (last 3 orders per product)**
```sql
SELECT 
    orderid,
    productid,
    sales,
    AVG(sales) OVER (
        PARTITION BY productid 
        ORDER BY orderid 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS moving_avg_3_orders
FROM sales.orders;
```
**Explanation:**  
- Calculates the average sales for each product, considering the current and previous two orders.

---

## 3. Maximum Salary Across All Employees

**Query:**
```sql
SELECT 
    *,
    MAX(salary) OVER () AS max_salary
FROM sales.employees;
```
**Explanation:**  
- `MAX(salary) OVER ()` returns the highest salary in the entire employees table for every row.

---

## 4. Average Score with and without NULLs

**Query:**
```sql
SELECT 
    customerid,
    lastname,
    score,
    ISNULL(score, 0) AS score_without_null,
    AVG(score) OVER () AS avg_score,
    AVG(ISNULL(score, 0)) OVER () AS avg_score_with_nulls_as_zero
FROM sales.customers;
```
**Explanation:**  
- `ISNULL(score, 0)` replaces NULL scores with 0.
- `AVG(score) OVER ()` calculates the average, ignoring NULLs.
- `AVG(ISNULL(score, 0)) OVER ()` treats NULLs as zero for the average.

---

## 5. Percentage Value of Each Sale

**Query:**
```sql
SELECT 
    orderid,
    productid,
    sales,
    SUM(sales) OVER (PARTITION BY productid) AS total_sales_per_product,
    ROUND(CAST(sales AS FLOAT) / SUM(sales) OVER () * 100, 2) AS value_percentage
FROM sales.orders;
```
**Explanation:**  
- `SUM(sales) OVER (PARTITION BY productid)` gives total sales for each product.
- `ROUND(CAST(sales AS FLOAT) / SUM(sales) OVER () * 100, 2)` calculates the percentage of each sale compared to total sales, rounded to 2 decimals.

---

## Summary Table

| Example                      | Purpose                                      |
|------------------------------|----------------------------------------------|
| Running Total                | Cumulative sum per group                     |
| Moving Average               | Average over a window of rows                |
| MAX() OVER ()                | Maximum value for all rows                   |
| AVG() OVER ()                | Average value for all rows                   |
| AVG(ISNULL(...)) OVER ()     | Average treating NULLs as zero               |
| Percentage Calculation       | Sale as % of total sales                     |

---

# Window Ranking and Value Functions Explained for Beginners

Window functions are advanced SQL features that let you perform calculations across rows related to the current row, without grouping or collapsing the result set. They are very useful for analytics, reporting, and comparing data.

## 1. Ranking Functions

These functions assign a number or rank to each row, based on the order you specify.

### a) ROW_NUMBER()

- **What it does:** Gives each row a unique number, starting at 1, based on the order you choose.
- **Why use it:** To number rows, or pick the "top N" per group.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      ROW_NUMBER() OVER (ORDER BY sales DESC) AS row_num
  FROM sales.orders;
  ```
  *This will number the orders from highest sales (row_num = 1) to lowest.*

### b) RANK()

- **What it does:** Assigns a rank to each row, with gaps if there are ties.
- **Why use it:** To show position in a leaderboard, where ties get the same rank.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      RANK() OVER (ORDER BY sales DESC) AS sales_rank
  FROM sales.orders;
  ```
  *If two orders have the same sales, they get the same rank, and the next rank is skipped.*

### c) DENSE_RANK()

- **What it does:** Like RANK(), but no gaps in ranking for ties.
- **Why use it:** To rank items without skipping numbers for ties.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      DENSE_RANK() OVER (ORDER BY sales DESC) AS dense_sales_rank
  FROM sales.orders;
  ```
  *Tied sales get the same rank, and the next rank is just one higher.*

### d) NTILE(n)

- **What it does:** Splits rows into n groups (tiles) of roughly equal size.
- **Why use it:** To divide data into quartiles, deciles, etc.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      NTILE(4) OVER (ORDER BY sales DESC) AS sales_quartile
  FROM sales.orders;
  ```
  *This divides all orders into 4 groups based on sales.*

---

## 2. Percentage Ranking Functions

These help you see how each row compares to others as a percentage.

### a) CUME_DIST()

- **What it does:** Shows the fraction of rows with a value less than or equal to the current row.
- **Why use it:** To find percentiles (e.g., top 10%).
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      CUME_DIST() OVER (ORDER BY sales DESC) AS sales_cume_dist
  FROM sales.orders;
  ```
  *A value of 0.8 means this row is in the top 80%.*

### b) PERCENT_RANK()

- **What it does:** Shows the relative rank of a row as a percentage (from 0 to 1).
- **Why use it:** To see how far down the list each row is.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      PERCENT_RANK() OVER (ORDER BY sales DESC) AS sales_percent_rank
  FROM sales.orders;
  ```
  *A value of 0.5 means this row is halfway down the ranking.*

---

## 3. Value Functions

These let you look at values from other rows, like the previous or next row.

### a) LEAD()

- **What it does:** Gets the value from the next row.
- **Why use it:** To compare current value to the next one.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      LEAD(sales, 1) OVER (ORDER BY orderid) AS next_order_sales
  FROM sales.orders;
  ```
  *Shows the sales value of the next order.*

### b) LAG()

- **What it does:** Gets the value from the previous row.
- **Why use it:** To compare current value to the previous one.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      LAG(sales, 1) OVER (ORDER BY orderid) AS prev_order_sales
  FROM sales.orders;
  ```
  *Shows the sales value of the previous order.*

### c) FIRST_VALUE()

- **What it does:** Gets the first value in the window (partition).
- **Why use it:** To compare every row to the first row in the group.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      FIRST_VALUE(sales) OVER (ORDER BY orderid) AS first_sales
  FROM sales.orders;
  ```
  *Shows the sales value of the first order.*

### d) LAST_VALUE()

- **What it does:** Gets the last value in the window (partition).
- **Why use it:** To compare every row to the last row in the group.
- **Example:**
  ```sql
  SELECT 
      orderid,
      sales,
      LAST_VALUE(sales) OVER (ORDER BY orderid ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_sales
  FROM sales.orders;
  ```
  *Shows the sales value of the last order.*

---

## Summary Table

| Function         | What it does (for beginners)                  | Example Usage                        |
|------------------|-----------------------------------------------|--------------------------------------|
| ROW_NUMBER()     | Numbers each row in order                     | Find top N per group                 |
| RANK()           | Ranks rows, with gaps for ties                | Leaderboards                         |
| DENSE_RANK()     | Ranks rows, no gaps for ties                  | Dense ranking                        |
| NTILE(n)         | Splits rows into n groups                     | Quartiles, deciles                   |
| CUME_DIST()      | Shows percentile (fraction <= current row)    | Find top 10%                         |
| PERCENT_RANK()   | Shows relative rank as percent                | See how far down the list             |
| LEAD()           | Gets value from next row                      | Compare to next value                |
| LAG()            | Gets value from previous row                  | Compare to previous value            |
| FIRST_VALUE()    | Gets first value in group                     | Compare to first in group            |
| LAST_VALUE()     | Gets last value in group                      | Compare to last in group             |

---

**Tip for Beginners:**  
Window functions use the `OVER()` clause. You can use `ORDER BY` to sort, and `PARTITION BY` to group rows for calculation.  
They let you do things like ranking, running totals, and comparing rows—all without losing any rows from your result.

>There are Many techniques to optimize the SQL queries like indexing, query refactoring, caching, and database configuration.

# Database architecture and Components

**Database architecture** refers to the design and structure of a database system, including how data is stored, accessed, and managed on the server. It consists of several key components:

- **Database Engine:** The core service that processes SQL queries, manages transactions, and controls access to data.
- **Disk Storage:** Where persistent data is stored (tables, indexes, logs). Data on disk is durable and survives server restarts.
- **Cache (Buffer Pool):** Memory area used to temporarily store frequently accessed data and query results for faster performance.
- **User:** Represents the client or application connecting to the database, with specific permissions and roles.
- **Catalog (System Catalog/Metadata):** Stores information about database objects (tables, columns, indexes, users, etc.).
- **Temporary Storage:** Used for intermediate results, temporary tables, or sorting operations during query execution.

**Types of Data:**
- **DISK:** Permanent data stored on physical storage.
- **CACHE:** Volatile, fast-access data held in memory for performance.
- **TEMPORARY:** Data created for short-term use during query processing.

**Example:**
Suppose a user runs a query to select customer data. The database engine receives the query, checks the catalog for table definitions, retrieves data from disk (if not cached), may use temporary storage for sorting/grouping, and returns results. Frequently accessed data is kept in cache for faster future queries.

**Server Components Overview:**
| Component        | Role/Function                                      |
|------------------|---------------------------------------------------|
| Database Engine  | Executes queries, manages transactions             |
| Disk Storage     | Stores persistent data                             |
| Cache            | Speeds up access to frequently used data           |
| User             | Connects to DB, has permissions                    |
| Catalog          | Holds metadata about DB objects                    |
| Temporary        | Used for intermediate query results                |

**Diagram (simplified):**
```
[User/Application]
        |
   [Database Engine]
        |
-----------------------------
|   Disk   |   Cache   |   Temporary   |
-----------------------------
        |
   [Catalog/Metadata]
```

This architecture ensures efficient, secure, and reliable data management for multiple users and applications.




--subquery
--its mainly used in 3 cateegory like dependency,reult types,location or clauses

--result types
--scalar query,row query,table query
--select * from sales.products

--location or clause
--from
--select productid,price,avg(price) over() avg_prices from sales.products
--select * from (select productid,price,avg(price) over() avg_prices from sales.products)d where avg_prices<price
--select customerid,sum(sales) ,rank() over(order by sum(sales) desc )  from sales.orders group by customerid
--select colum 
--we use selecct sub query only when the sub queery returns one value as a ooutput or scalr query only 
--join columns
--where colums in ehich first is comparsion opertor then logical opertor like( in,all,all,exisits)

--correlated and non-correlated sub query
# Subqueries in SQL

Subqueries (also called inner queries or nested queries) are queries placed inside another SQL query. They are powerful tools for filtering, calculating, and joining data.

## 1. Categories of Subqueries

Subqueries can be classified based on:

- **Dependency:**  
  - *Correlated subquery*: Depends on the outer query for its values.
  - *Non-correlated subquery*: Can run independently of the outer query.

- **Result Types:**  
  - *Scalar subquery*: Returns a single value.
  - *Row subquery*: Returns a single row.
  - *Table subquery*: Returns multiple rows and columns.

- **Location/Clause:**  
  Subqueries can be used in different parts of a SQL statement:
  - `SELECT` clause
  - `FROM` clause
  - `WHERE` clause
  - `JOIN` clause

---

## 2. Result Types Explained

### a) Scalar Subquery

Returns a single value.  
**Example:** Find products with price above the average price.

```sql
SELECT productid, price
FROM sales.products
WHERE price > (SELECT AVG(price) FROM sales.products);
```

### b) Row Subquery

Returns a single row (multiple columns).  
**Example:** Get the product with the highest price.

```sql
SELECT *
FROM sales.products
WHERE (productid, price) = (
    SELECT TOP 1 productid, price
    FROM sales.products
    ORDER BY price DESC
);
```

### c) Table Subquery

Returns multiple rows and columns.  
**Example:** Use a subquery as a table in the FROM clause.

```sql
SELECT *
FROM (
    SELECT productid, price, AVG(price) OVER() AS avg_price
    FROM sales.products
) AS d
WHERE price > avg_price;
```

---

## 3. Subquery Locations

### a) FROM Clause

You can use a subquery as a derived table.

**Example:**  
Get products whose price is above the average.

```sql
SELECT *
FROM (
    SELECT productid, price, AVG(price) OVER() AS avg_price
    FROM sales.products
) AS d
WHERE price > avg_price;
```

### b) SELECT Clause

Use a subquery to calculate a value for each row.

**Example:**  
Show each product and the total number of products.

```sql
SELECT productid, price,
    (SELECT COUNT(*) FROM sales.products) AS total_products
FROM sales.products;
```

### c) WHERE Clause

Filter rows using a subquery.

**Example:**  
Find customers who placed at least one order.

```sql
SELECT *
FROM sales.customers
WHERE customerid IN (SELECT customerid FROM sales.orders);
```

### d) JOIN Clause

Join tables using a subquery.

**Example:**  
Get customers and their highest order amount.

```sql
SELECT c.customerid, c.firstname, o.max_sales
FROM sales.customers c
JOIN (
    SELECT customerid, MAX(sales) AS max_sales
    FROM sales.orders
    GROUP BY customerid
) o ON c.customerid = o.customerid;
```

---

## 4. Correlated vs Non-Correlated Subqueries

### a) Non-Correlated Subquery

Runs independently of the outer query.

**Example:**  
Find products above average price.

```sql
SELECT productid, price
FROM sales.products
WHERE price > (SELECT AVG(price) FROM sales.products);
```

### b) Correlated Subquery

References columns from the outer query.

**Example:**  
Find customers who placed more than one order.

```sql
SELECT customerid, firstname
FROM sales.customers c
WHERE 1 < (
    SELECT COUNT(*)
    FROM sales.orders o
    WHERE o.customerid = c.customerid
);
```

---

## 5. Subquery Use Cases

- **Filtering:**  
  Use subqueries in WHERE to filter results based on another query.

- **Calculations:**  
  Use subqueries in SELECT to calculate values per row.

- **Joins:**  
  Use subqueries in FROM or JOIN to create derived tables.

- **Existence Checks:**  
  Use `EXISTS` or `IN` with subqueries to check for related data.

---

**Summary Table: Subquery Types and Examples**

| Type         | Location   | Example SQL Snippet                                   | Description                       |
|--------------|------------|------------------------------------------------------|-----------------------------------|
| Scalar       | WHERE      | `WHERE price > (SELECT AVG(price) FROM products)`    | Single value                      |
| Row          | WHERE      | `WHERE (id, price) = (SELECT ...)`                   | Single row                        |
| Table        | FROM       | `FROM (SELECT ...) AS d`                             | Multiple rows/columns             |
| Correlated   | WHERE      | `WHERE EXISTS (SELECT ... WHERE o.id = c.id)`        | Depends on outer query            |
| Non-Correlated | WHERE    | `WHERE id IN (SELECT id FROM ...)`                   | Independent of outer query        |


--CTE(common table expression)
--temporpary table which can be used only in the main query only
-- the execution order is first CTE then the main query
--its like virtual table and can be used many times unlike sub-queries
--with fuckname as(select * from sales.database)
--standalone cte,multiple cte,nested cte are non-recursive cte 
--recursice cte are 
with series as(
select 1 as mynumber union all select mynumber+1 from series where mynumber<10
)
select *,count(*) over() from series option (maxrecursion 1000)

# Common Table Expressions (CTE) in SQL

A **Common Table Expression (CTE)** is a temporary result set (like a virtual table) that you can reference within a SELECT, INSERT, UPDATE, or DELETE statement. CTEs make complex queries easier to read and maintain.

## Key Points

- **Scope:** CTEs exist only for the duration of the main query.
- **Execution Order:** The CTE is evaluated first, then the main query runs using its result.
- **Reusability:** You can reference a CTE multiple times in the main query (unlike subqueries).
- **Syntax:**  
  ```sql
  WITH cte_name AS (
      -- your query here
      SELECT ...
  )
  SELECT ... FROM cte_name;
  ```

---

## Types of CTEs

### 1. Standalone (Single) CTE

A single CTE used in a query.

**Example:**  
Get all products with price above average.

```sql
WITH expensive_products AS (
    SELECT productid, price
    FROM sales.products
    WHERE price > (SELECT AVG(price) FROM sales.products)
)
SELECT * FROM expensive_products;
```
**Explanation:**  
- The CTE `expensive_products` selects products with price above average.
- The main query selects all rows from the CTE.

---

### 2. Multiple CTEs

You can define more than one CTE by separating them with commas.

**Example:**  
Get customers and their total orders.

```sql
WITH customer_orders AS (
    SELECT customerid, COUNT(*) AS total_orders
    FROM sales.orders
    GROUP BY customerid
),
high_value_customers AS (
    SELECT customerid
    FROM customer_orders
    WHERE total_orders > 5
)
SELECT c.customerid, c.firstname
FROM sales.customers c
JOIN high_value_customers hvc ON c.customerid = hvc.customerid;
```
**Explanation:**  
- `customer_orders` calculates total orders per customer.
- `high_value_customers` filters customers with more than 5 orders.
- The final query joins customers with high-value customers.

---

### 3. Nested CTEs

A CTE can reference another CTE defined earlier.

**Example:**  
Find products above average price, then get their sales.

```sql
WITH avg_price_cte AS (
    SELECT AVG(price) AS avg_price FROM sales.products
),
expensive_products AS (
    SELECT productid, price
    FROM sales.products, avg_price_cte
    WHERE sales.products.price > avg_price_cte.avg_price
)
SELECT * FROM expensive_products;
```
**Explanation:**  
- `avg_price_cte` calculates the average price.
- `expensive_products` uses that value to filter products.

---

### 4. Recursive CTEs

Recursive CTEs repeatedly reference themselves to produce a sequence or hierarchy.

**Example:**  
Generate a series of numbers from 1 to 10.

```sql
WITH series AS (
    SELECT 1 AS mynumber
    UNION ALL
    SELECT mynumber + 1 FROM series WHERE mynumber < 10
)
SELECT *, COUNT(*) OVER() AS total_numbers
FROM series
OPTION (MAXRECURSION 1000);
```
**Explanation:**  
- The CTE `series` starts with 1, then adds 1 until it reaches 10.
- The main query selects all numbers and counts them.

---

## When to Use CTEs

- Simplify complex queries (especially with multiple steps).
- Replace subqueries for better readability.
- Reference the same result set multiple times.
- Build recursive queries (hierarchies, sequences).

---

## Summary Table

| Type           | Description                                 | Example Use Case                |
|----------------|---------------------------------------------|---------------------------------|
| Standalone     | Single CTE for one query                    | Filter products above average   |
| Multiple       | Several CTEs for multi-step logic           | Find high-value customers       |
| Nested         | CTEs referencing other CTEs                 | Use calculated values           |
| Recursive      | CTEs that call themselves                   | Generate sequences, hierarchies |

---

**Tip:**  
CTEs make SQL queries easier to read, debug, and maintain. Use them for step-by-step logic and when you need to reuse results.

# SQL Views Explained (with Examples)

A **View** in SQL is a virtual table created by a query. Views help you simplify complex queries, hide sensitive data, and reuse logic.

## 1. Physical Level, Logical Level, View Level

- **Physical Level:** Actual tables and data stored on disk.
- **Logical Level:** How data is organized and related (schemas, relationships).
- **View Level:** What users see through views (customized, filtered, or joined data).

## 2. Creating a View

You can create a view to simplify access to data or hide complexity.

**Syntax:**
```sql
CREATE VIEW view_name AS
SELECT columns
FROM table
WHERE conditions;
```

**Example:**  
Create a view to show all orders:
```sql
CREATE VIEW saikumar AS
SELECT * FROM sales.orders;
```

Now you can use the view like a table:
```sql
SELECT * FROM saikumar;
```

## 3. Dropping a View

To remove a view when you no longer need it:
```sql
DROP VIEW saikumar;
```

## 4. Updating or Replacing a View

If you want to change the definition of a view, you can recreate it (or use `CREATE OR REPLACE` in some databases):

**Example:**  
Update the view to show only orders above 1000 sales:
```sql
CREATE OR REPLACE VIEW saikumar AS
SELECT * FROM sales.orders WHERE sales > 1000;
```
*(Note: In SQL Server, use `ALTER VIEW` instead.)*

## 5. Why Use Views?

- **Simplify queries:** Hide complex joins or filters.
- **Security:** Restrict access to sensitive columns.
- **Reuse logic:** Use the same query in multiple places.

**Example:**  
Suppose you want to show only customers from India:
```sql
CREATE VIEW indian_customers AS
SELECT * FROM sales.customers WHERE country = 'India';
```

Now, just query:
```sql
SELECT * FROM indian_customers;
```

## 6. More Examples

**a) Aggregated View**
```sql
CREATE VIEW sales_summary AS
SELECT country, SUM(sales) AS total_sales
FROM sales.orders
GROUP BY country;
```
Query:
```sql
SELECT * FROM sales_summary WHERE total_sales > 5000;
```

**b) Join View**
```sql
CREATE VIEW order_details AS
SELECT o.orderid, c.firstname, c.lastname, p.product, o.sales
FROM sales.orders o
JOIN sales.customers c ON o.customerid = c.customerid
JOIN sales.products p ON o.productid = p.productid;
```
Query:
```sql
SELECT * FROM order_details WHERE sales > 500;
```

---

**Summary Table: View Operations**

| Operation             | SQL Example                                  | Description                       |
|-----------------------|----------------------------------------------|-----------------------------------|
| Create View           | `CREATE VIEW ... AS SELECT ...`              | Define a new view                 |
| Query View            | `SELECT * FROM view_name`                    | Use the view like a table         |
| Drop View             | `DROP VIEW view_name`                        | Remove the view                   |
| Replace/Alter View    | `CREATE OR REPLACE VIEW ... AS SELECT ...`   | Update the view definition        |

---

**Tip:**  
Use views to make your SQL code cleaner, safer, and easier to maintain. Views are especially useful for reporting, analytics, and restricting access to sensitive data.

--database table
--a table is like collection of data
--table types which are permanet table and tempory tables
--CTAS create table name as() or select into table-name from .... where..
--- drop table name
--to refresh the CTAS use t-sql()  like if(name ,target) is not null then drop go CTAS

# Topic Explanations with Examples

## 1. Database Table

A table is a collection of related data, organized in rows and columns. Think of it like an Excel sheet.

**Example:**
```sql
CREATE TABLE customers (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  country VARCHAR(50)
);
```
This creates a table called `customers` with columns for ID, name, and country.

## 2. Permanent vs Temporary Tables

- **Permanent Table:** Data stays in the database until you delete it.
- **Temporary Table:** Data is only available during your session or until the server restarts.

**Example (Temporary Table):**
```sql
CREATE TABLE #temp_customers (
  id INT,
  name VARCHAR(50)
);
```
The `#` means it's a temporary table.

## 3. CTAS (Create Table As Select)

This lets you create a new table from the results of a query.

**Example:**
```sql
SELECT * INTO new_customers FROM customers WHERE country = 'India';
```
This creates a table `new_customers` with only customers from India.

## 4. Drop Table

Removes a table from the database.

**Example:**
```sql
DROP TABLE customers;
```
This deletes the `customers` table and all its data.

## 5. Refreshing CTAS Table (T-SQL)

Before recreating a table, you can check if it exists and drop it.

**Example:**
```sql
IF OBJECT_ID('new_customers', 'U') IS NOT NULL
  DROP TABLE new_customers;
GO
SELECT * INTO new_customers FROM customers WHERE country = 'India';
```
This checks if `new_customers` exists, drops it if it does, and then recreates it.

---
**Summary:**  
- Tables store data in rows and columns.
- Permanent tables keep data; temporary tables are short-lived.
- CTAS lets you create tables from query results.
- Drop removes tables.
- Use T-SQL to refresh tables safely.

Each example shows how to use SQL for basic table operations, making it easy to manage your data.

---

# Comparison Table: Subqueries vs CTEs vs Temporary Tables vs CTAS vs Views

| **Aspect** | **Subquery** | **CTE (Common Table Expression)** | **Temporary Table (#temp)** | **CTAS (Create Table As Select)** | **Views** |
|------------|--------------|-----------------------------------|------------------------------|-----------------------------------|-----------|
| **Storage** | No physical storage (in-memory during execution) | No physical storage (in-memory during execution) | Physical storage in tempdb | Physical storage in database | No physical storage (virtual table) |
| **Lifetime** | Duration of the query execution only | Duration of the single query/statement only | Session duration or until explicitly dropped | Permanent until explicitly dropped | Permanent until explicitly dropped |
| **When Deleted** | Automatically after query completes | Automatically after statement completes | When session ends OR explicitly dropped | Manual DROP TABLE command | Manual DROP VIEW command |
| **Scope** | Limited to the specific query where it's used | Limited to the single statement (SELECT/INSERT/UPDATE/DELETE) | Available throughout the session | Available to all users/sessions | Available to all authorized users/sessions |
| **Reusability** | Cannot be reused (must rewrite each time) | Can be referenced multiple times within same statement | Can be used multiple times in session | Can be used multiple times across sessions | Can be used multiple times across sessions |
| **Up-to-date** | Always current (executes each time) | Always current (executes each time) | Static snapshot (data at creation time) | Static snapshot (data at creation time) | Always current (executes underlying query) |
| **Performance** | Re-executes each time referenced | Re-executes each time referenced | Fast access after creation (indexed) | Fast access after creation (indexed) | Re-executes underlying query each time |
| **Memory Usage** | Minimal (temporary) | Minimal (temporary) | Uses tempdb space | Uses database storage space | Minimal (no data storage) |
| **Syntax** | `(SELECT ...)` within main query | `WITH name AS (SELECT ...) SELECT ...` | `CREATE TABLE #temp (...) INSERT ...` | `SELECT ... INTO new_table FROM ...` | `CREATE VIEW name AS SELECT ...` |
| **Indexing** | Not possible | Not possible | Can create indexes | Can create indexes | Not possible (indexes on base tables) |
| **Data Modification** | Read-only | Read-only | Full DML support (INSERT/UPDATE/DELETE) | Full DML support (INSERT/UPDATE/DELETE) | Limited (simple views only) |
| **Best Use Case** | Simple filtering/calculations | Complex multi-step logic within one query | Heavy processing with multiple operations | Creating permanent derived tables | Simplifying complex queries for reuse |

---

## Examples of Each Approach

### 1. **Subquery Example**
```sql
-- Find customers with above-average order amounts
SELECT customerid, firstname 
FROM sales.customers 
WHERE customerid IN (
    SELECT customerid 
    FROM sales.orders 
    WHERE sales > (SELECT AVG(sales) FROM sales.orders)
);
```

### 2. **CTE Example**
```sql
-- Same logic but more readable with CTE
WITH avg_sales AS (
    SELECT AVG(sales) AS avg_amount FROM sales.orders
),
high_value_orders AS (
    SELECT customerid 
    FROM sales.orders, avg_sales 
    WHERE sales > avg_amount
)
SELECT c.customerid, c.firstname 
FROM sales.customers c
JOIN high_value_orders h ON c.customerid = h.customerid;
```

### 3. **Temporary Table Example**
```sql
-- Create temp table for multiple operations
CREATE TABLE #temp_high_customers (
    customerid INT,
    total_sales DECIMAL(10,2),
    order_count INT
);

INSERT INTO #temp_high_customers
SELECT customerid, SUM(sales), COUNT(*)
FROM sales.orders
GROUP BY customerid
HAVING SUM(sales) > 10000;

-- Use multiple times in session
SELECT * FROM #temp_high_customers WHERE order_count > 5;
SELECT AVG(total_sales) FROM #temp_high_customers;

DROP TABLE #temp_high_customers;
```

### 4. **CTAS Example**
```sql
-- Create permanent table from query
SELECT customerid, SUM(sales) AS total_sales, COUNT(*) AS order_count
INTO customer_summary
FROM sales.orders
GROUP BY customerid;

-- Table persists and can be used by anyone
SELECT * FROM customer_summary WHERE total_sales > 10000;
```

### 5. **View Example**
```sql
-- Create reusable view
CREATE VIEW high_value_customers AS
SELECT c.customerid, c.firstname, c.lastname, SUM(o.sales) AS total_sales
FROM sales.customers c
JOIN sales.orders o ON c.customerid = o.customerid
GROUP BY c.customerid, c.firstname, c.lastname
HAVING SUM(o.sales) > 10000;

-- Always shows current data
SELECT * FROM high_value_customers;
```

---

## When to Use Each

- **Subqueries:** Simple one-time filtering or calculations
- **CTEs:** Complex multi-step logic within a single statement
- **Temporary Tables:** Heavy processing, multiple operations, need indexing
- **CTAS:** Creating permanent derived/summary tables
- **Views:** Simplifying complex queries for repeated use with current data

# SQL Server Table Partitioning Explained (with Examples)

## What is Table Partitioning?
Table partitioning helps you split large tables into smaller, manageable pieces called partitions. This improves performance and makes maintenance easier.

---

## 1. Partition Function

A **partition function** defines how SQL Server divides your data into partitions based on column values.

**Example:**  
Suppose you want to split data by date ranges:

```sql
CREATE PARTITION FUNCTION pfDateRange (date)
AS RANGE LEFT FOR VALUES ('2022-01-01', '2022-06-01', '2022-12-31');
```
- This creates 4 partitions:
  - Before 2022-01-01
  - 2022-01-01 to 2022-06-01
  - 2022-06-01 to 2022-12-31
  - After 2022-12-31

---

## 2. Filegroups

**Filegroups** are logical storage containers in SQL Server. You can add files to filegroups to organize where your data is stored.

**Add a file to a filegroup:**
```sql
ALTER DATABASE MyDatabase ADD FILE (
    NAME = 'DataFile1', -- logical name
    FILENAME = 'C:\Data\DataFile1.mdf' -- file location
) TO FILEGROUP FG1;
```

**Remove a file from a filegroup:**
```sql
ALTER DATABASE MyDatabase REMOVE FILE DataFile1;
```

- Files are usually saved as `.mdf` files.

---

## 3. Partition Scheme

A **partition scheme** connects your partition function to filegroups. It tells SQL Server where to store each partition.

**Example:**
```sql
CREATE PARTITION SCHEME psDateRange
AS PARTITION pfDateRange
TO (FG1, FG2, FG3, FG4);
```
- Each partition will be stored in a different filegroup.

---

## 4. Putting It All Together

**Step-by-step Example:**

1. **Create Filegroups:**
    ```sql
    ALTER DATABASE MyDatabase ADD FILEGROUP FG1;
    ALTER DATABASE MyDatabase ADD FILEGROUP FG2;
    ALTER DATABASE MyDatabase ADD FILEGROUP FG3;
    ALTER DATABASE MyDatabase ADD FILEGROUP FG4;
    ```

2. **Add Files to Filegroups:**
    ```sql
    ALTER DATABASE MyDatabase ADD FILE (
        NAME = 'DataFile1',
        FILENAME = 'C:\Data\DataFile1.mdf'
    ) TO FILEGROUP FG1;
    -- Repeat for FG2, FG3, FG4
    ```

3. **Create Partition Function:**
    ```sql
    CREATE PARTITION FUNCTION pfDateRange (date)
    AS RANGE LEFT FOR VALUES ('2022-01-01', '2022-06-01', '2022-12-31');
    ```

4. **Create Partition Scheme:**
    ```sql
    CREATE PARTITION SCHEME psDateRange
    AS PARTITION pfDateRange
    TO (FG1, FG2, FG3, FG4);
    ```

5. **Create a Partitioned Table:**
    ```sql
    CREATE TABLE Orders (
        OrderID INT,
        OrderDate DATE
    ) ON psDateRange(OrderDate);
    ```

---

## Summary

- **Partition Function:** Sets boundaries for partitions.
- **Filegroup:** Defines where data for each partition is stored.
- **Partition Scheme:** Connects partition function to filegroups.
- **Partitioned Table:** Uses the scheme to store data in partitions.

---

## Quick Reference

```sql
-- Partition Function
CREATE PARTITION FUNCTION pfDateRange (date) AS RANGE LEFT FOR VALUES ('2022-01-01', '2022-06-01', '2022-12-31');

-- Partition Scheme
CREATE PARTITION SCHEME psDateRange AS PARTITION pfDateRange TO (FG1, FG2, FG3, FG4);

-- Partitioned Table
CREATE TABLE Orders (
    OrderID INT,
    OrderDate DATE
) ON psDateRange(OrderDate);
```
## Tips to Optimize SQL Queries for Better Performance
| Tip # | Description |
|-------|-------------|
| 1     | Select only required columns to avoid fetching unnecessary data. |
| 2     | Avoid unnecessary DISTINCT and ORDER BY clauses. |
| 3     | Limit rows for exploration queries. |
| 4     | Create a non-clustered index on frequently used columns in WHERE clause. |
| 5     | Avoid applying functions to columns in WHERE clause. |
| 6     | Avoid leading wildcards in LIKE queries to enable index usage. |
| 7     | Use IN instead of multiple OR conditions. |
| 8     | Prefer INNER JOIN for faster processing when possible. |
| 9     | Use explicit (ANSI) JOINs instead of implicit joins. |
| 10    | Index columns used in ON clauses. |
| 11    | Filter large tables before joining, using CTEs or subqueries. |
| 12    | Aggregate data before joining to avoid inefficient correlated queries. |
| 13    | Use UNION instead of OR in joins. |
| 14    | Check for nested loops and use SQL hints (e.g., hash joins) for big tables. |
| 15    | Use UNION ALL instead of UNION when duplicates are acceptable. |
| 16    | Use UNION ALL with DISTINCT if duplicates are not acceptable. |
| 17    | Use columnstore indexes for aggregation on large tables. |
| 18    | Pre-aggregate data and store in new tables for reporting. |
| 19    | Prefer JOIN over IN if performance is similar; EXISTS can be better. |
| 20    | Avoid redundant logic in queries. |
| 21    | Avoid VARCHAR and TEXT data types if possible; TEXT is worse than VARCHAR. |
| 22    | Avoid unnecessarily large length (MAX) in data types. |
| 23    | Use NOT NULL constraints where applicable. |
| 24    | Ensure all tables have a clustered primary key. |
| 25    | Create non-clustered indexes for frequently used foreign keys. |
| 26    | Avoid over-indexing tables. |
| 27    | Drop unused indexes. |
| 28    | Update statistics weekly. |
| 29    | Rebuild and reorganize indexes weekly. |
| 30    | Partition large tables and apply columnstore indexes for best performance. |

# SQL Concepts Explained

## Stored Procedures

A stored procedure is a set of SQL statements that can be executed as a single unit. It helps in reusing code and improving performance.

**Syntax Example:**
```sql
CREATE PROCEDURE saikumar
AS
BEGIN
    -- SQL statements here
END
```

**Executing a Stored Procedure:**
```sql
EXEC saikumar
```

**Stored Procedure with Parameters:**
```sql
CREATE PROCEDURE saikumar @parameter NVARCHAR(50)
AS
BEGIN
    SELECT * FROM table_name WHERE name = @parameter
END
```

**Executing with Parameter:**
```sql
EXEC saikumar @parameter = 'John'
```

---

## Variables, IF-ELSE, TRY-CATCH

Variables are used to store data temporarily. IF-ELSE is used for conditional logic. TRY-CATCH is used for error handling.

**Example:**
```sql
DECLARE @age INT = 25

IF @age > 18
    PRINT 'Adult'
ELSE
    PRINT 'Minor'

BEGIN TRY
    -- SQL statements that may cause errors
END TRY
BEGIN CATCH
    PRINT ERROR_MESSAGE()
END CATCH
```

---

## Triggers

A trigger is a special type of stored procedure that runs automatically when a specific event occurs in the database (like INSERT, UPDATE, DELETE).

**Syntax Example:**
```sql
CREATE TRIGGER trg_after_insert
ON table_name
AFTER INSERT
AS
BEGIN
    -- Actions to perform after insert
END
```

**Explanation:**  
This trigger runs after a new row is inserted into `table_name`.
