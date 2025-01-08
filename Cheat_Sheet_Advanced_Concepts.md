Here are a few **additional concepts** that could enhance further:

---

## **1. Advanced SQL Concepts**

### **1.1 Common Table Expressions (CTEs)**
Useful for improving query readability and reusability.

```sql
WITH sales_summary AS (
    SELECT department_id, SUM(salary) AS total_salary
    FROM employees
    GROUP BY department_id
)
SELECT *
FROM sales_summary
WHERE total_salary > 50000;
```
- **Why Use?** Simplifies complex queries and allows recursive operations.

---

### **1.2 Recursive Queries**
Helpful for hierarchical data like organizational charts.

```sql
WITH RECURSIVE EmployeeHierarchy AS (
    SELECT id, name, manager_id
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT e.id, e.name, e.manager_id
    FROM employees e
    INNER JOIN EmployeeHierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM EmployeeHierarchy;
```

---

### **1.3 JSON Handling**
For working with JSON data in SQL databases that support it (e.g., PostgreSQL, MySQL).

```sql
SELECT JSON_EXTRACT(data, '$.name') AS name
FROM employees;
```

---

## **2. String Functions**

```sql
SELECT LENGTH(name) FROM employees;          -- Length of string
SELECT UPPER(name) FROM employees;           -- Convert to uppercase
SELECT LOWER(name) FROM employees;           -- Convert to lowercase
SELECT TRIM('  data  ') FROM employees;      -- Remove whitespace
SELECT SUBSTRING(name, 1, 3) FROM employees; -- Extract substring
```

---

## **3. Date and Time Functions**

```sql
SELECT NOW();                                -- Current timestamp
SELECT CURDATE();                            -- Current date
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d');       -- Format date
SELECT DATEDIFF(NOW(), '2023-01-01');        -- Days difference
SELECT TIMESTAMPDIFF(YEAR, birth_date, NOW()); -- Age calculation
```

---

## **4. Pivot Tables with CASE WHEN**

```sql
SELECT 
    department,
    SUM(CASE WHEN gender = 'Male' THEN salary ELSE 0 END) AS male_salary,
    SUM(CASE WHEN gender = 'Female' THEN salary ELSE 0 END) AS female_salary
FROM employees
GROUP BY department;
```

---

## **5. Error Handling with TRY-CATCH** *(Database-Specific)*

Example for **SQL Server**:
```sql
BEGIN TRY
    INSERT INTO employees (id, name, salary) VALUES (1, 'John', 50000);
END TRY
BEGIN CATCH
    PRINT 'An error occurred!';
END CATCH
```

---

## **6. Transactions for Data Integrity**

```sql
START TRANSACTION;

UPDATE employees
SET salary = salary * 1.10
WHERE department = 'Sales';

COMMIT; -- Save changes

-- ROLLBACK; -- Undo changes if something goes wrong
```

---

## **7. Row-Level Security (RLS)** *(PostgreSQL Example)*

```sql
CREATE POLICY employee_policy
ON employees
FOR SELECT
USING (department = current_department);
```
- **Purpose:** Restricts data access based on roles.

---

## **8. Analytical SQL (Advanced Window Functions)**

### **8.1 Cumulative Percentages**
```sql
SELECT name, salary,
       salary * 100.0 / SUM(salary) OVER () AS percentage_of_total
FROM employees;
```

### **8.2 Rank Distribution by Quartiles**
```sql
SELECT name, salary,
       NTILE(4) OVER (ORDER BY salary) AS quartile
FROM employees;
```

---

## **9. User-Defined Functions (UDFs)**

### **Creating a Custom Function**
```sql
CREATE FUNCTION get_bonus(salary DECIMAL(10,2))
RETURNS DECIMAL(10,2)
BEGIN
    RETURN salary * 0.10; -- Bonus = 10% of salary
END;
```

### **Using the Function**
```sql
SELECT name, salary, get_bonus(salary) AS bonus
FROM employees;
```

---

## **10. Security Features**

### **10.1 GRANT Permissions**
```sql
GRANT SELECT, INSERT ON employees TO 'user1'@'localhost';
```

### **10.2 REVOKE Permissions**
```sql
REVOKE SELECT ON employees FROM 'user1'@'localhost';
```

---

## **11. Performance Optimization Techniques**

### **11.1 Indexing for Faster Queries**
```sql
CREATE INDEX idx_name ON employees(name);
```

### **11.2 EXPLAIN Query Execution Plan**
```sql
EXPLAIN SELECT * FROM employees WHERE salary > 50000;
```

---

## **12. Backup and Restore**

### **Export Database to SQL File**
```bash
mysqldump -u username -p database_name > backup.sql
```

### **Import Database from SQL File**
```bash
mysql -u username -p database_name < backup.sql
```

---

## **Final Suggestions**
1. Add **real-world query examples** based on datasets (e.g., employees, sales).  
2. Include **SQL best practices** for writing readable and efficient queries.  
3. Provide **links to tools** like **SQL Fiddle** or **DB Fiddle** for interactive testing.  
4. Add **interview-specific SQL queries** like the **second-highest salary** or **duplicate removal** tasks.  

---

CompiledByUdithaWICK
