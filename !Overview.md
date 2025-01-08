### **Structured Query Language (SQL)**  
SQL is a standard programming language used to **manage, query, and manipulate relational databases**. It is essential for **data analysis, database design, and backend development**.

---

## **1. SQL Basics**

### **a. Creating a Database and Table**
```sql
-- Create Database
CREATE DATABASE company;

-- Use Database
USE company;

-- Create Table
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    department VARCHAR(30),
    salary DECIMAL(10, 2)
);
```

---

### **b. Inserting Data**
```sql
INSERT INTO employees (id, name, age, department, salary)
VALUES (1, 'John Doe', 30, 'HR', 50000.00);

INSERT INTO employees VALUES 
(2, 'Jane Smith', 25, 'IT', 60000.00),
(3, 'Mike Brown', 35, 'Finance', 55000.00);
```

---

### **c. Retrieving Data**
```sql
-- Select all columns
SELECT * FROM employees;

-- Select specific columns
SELECT name, salary FROM employees;

-- Filtering rows
SELECT * FROM employees WHERE age > 30;

-- Sorting rows
SELECT * FROM employees ORDER BY salary DESC;

-- Limiting rows
SELECT * FROM employees LIMIT 3;
```

---

### **d. Updating and Deleting Data**
```sql
-- Update data
UPDATE employees
SET salary = 58000
WHERE id = 1;

-- Delete a row
DELETE FROM employees WHERE id = 3;
```

---

## **2. SQL Functions**

### **a. Aggregate Functions**
```sql
SELECT COUNT(*) AS total_employees FROM employees;

SELECT AVG(salary) AS avg_salary FROM employees;

SELECT SUM(salary) AS total_salary FROM employees;

SELECT MIN(salary) AS min_salary, MAX(salary) AS max_salary FROM employees;
```

---

### **b. Grouping and Filtering Groups**
```sql
-- Group employees by department
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

-- Filter groups using HAVING
SELECT department, COUNT(*) AS count
FROM employees
GROUP BY department
HAVING COUNT(*) > 1;
```

---

## **3. Joins (Combining Tables)**

### **a. Inner Join**
```sql
SELECT e.id, e.name, d.department_name
FROM employees e
INNER JOIN departments d
ON e.department = d.department_id;
```

### **b. Left Join**
```sql
SELECT e.id, e.name, d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department = d.department_id;
```

### **c. Right Join**
```sql
SELECT e.id, e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department = d.department_id;
```

### **d. Full Outer Join (Simulated in MySQL using UNION)**
```sql
SELECT e.id, e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.department = d.department_id
UNION
SELECT e.id, e.name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.department = d.department_id;
```

---

## **4. Subqueries**
```sql
-- Find employees earning above average salary
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Find employees from HR department
SELECT name
FROM employees
WHERE department = (SELECT department_id FROM departments WHERE department_name = 'HR');
```

---

## **5. Views (Virtual Tables)**
```sql
-- Create a View
CREATE VIEW high_paid_employees AS
SELECT name, salary
FROM employees
WHERE salary > 50000;

-- Use the View
SELECT * FROM high_paid_employees;

-- Drop a View
DROP VIEW high_paid_employees;
```

---

## **6. Indexing**
```sql
-- Create an index for faster searching
CREATE INDEX idx_name ON employees(name);

-- Drop an index
DROP INDEX idx_name ON employees;
```

---

## **7. Transactions**
```sql
-- Start transaction
START TRANSACTION;

-- Insert multiple rows
INSERT INTO employees VALUES (4, 'Lisa Grey', 28, 'HR', 62000.00);

-- Rollback if needed
ROLLBACK;

-- Commit the transaction
COMMIT;
```

---

## **8. Advanced SQL**

### **a. Window Functions**
```sql
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

### **b. Case Statements**
```sql
SELECT name, salary,
       CASE
           WHEN salary > 60000 THEN 'High'
           WHEN salary BETWEEN 50000 AND 60000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_category
FROM employees;
```

---

## **9. Export and Import Data**

### **Export Data to CSV**
```sql
SELECT * FROM employees
INTO OUTFILE '/tmp/employees.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

### **Import Data from CSV**
```sql
LOAD DATA INFILE '/tmp/employees.csv'
INTO TABLE employees
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS; -- Skip header
```

---

## **10. Practice Datasets**

- **Sample Databases:**
  - [Sakila Database](https://dev.mysql.com/doc/sakila/en/) – Movies Database.  
  - [AdventureWorks](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) – Business Use Cases.  
  - [Chinook Database](https://chinookdatabase.codeplex.com/) – Music Store Example.  

---

This provides an overview of SQL commands and examples for **querying, manipulating, and analyzing data**. 
