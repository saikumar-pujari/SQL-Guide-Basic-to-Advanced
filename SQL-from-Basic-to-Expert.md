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


> now we need to learn about **SQL_FUNCTIONS** which has 2 types 1) single-row functions 2) multi-row functions

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
