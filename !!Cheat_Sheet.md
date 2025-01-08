# SQL Cheat Sheet

## **Basic SQL Commands**

### **1. SELECT Data**
```sql
SELECT * FROM table_name;               -- Select all columns
SELECT column1, column2 FROM table_name; -- Select specific columns
```

### **2. Filtering Data (WHERE Clause)**
```sql
SELECT * FROM table_name
WHERE column_name = 'value';             -- Exact match

SELECT * FROM table_name
WHERE column_name > 100;                  -- Greater than filter

SELECT * FROM table_name
WHERE column_name BETWEEN 10 AND 50;      -- Range filter

SELECT * FROM table_name
WHERE column_name IN ('A', 'B', 'C');     -- Multiple values filter
```

### **3. Sorting Data (ORDER BY)**
```sql
SELECT * FROM table_name
ORDER BY column_name ASC;                 -- Ascending order

SELECT * FROM table_name
ORDER BY column_name DESC;                -- Descending order
```

### **4. Aggregate Functions**
```sql
SELECT COUNT(*) FROM table_name;          -- Count rows
SELECT AVG(column_name) FROM table_name;  -- Average
SELECT SUM(column_name) FROM table_name;  -- Sum
SELECT MIN(column_name) FROM table_name;  -- Minimum
SELECT MAX(column_name) FROM table_name;  -- Maximum
```

---

## **Joins in SQL**

### **1. INNER JOIN** (Matching rows in both tables)
```sql
SELECT a.column1, b.column2
FROM table1 a
INNER JOIN table2 b
ON a.id = b.id;
```

### **2. LEFT JOIN** (All rows from the left table and matches from the right)
```sql
SELECT a.column1, b.column2
FROM table1 a
LEFT JOIN table2 b
ON a.id = b.id;
```

### **3. RIGHT JOIN** (All rows from the right table and matches from the left)
```sql
SELECT a.column1, b.column2
FROM table1 a
RIGHT JOIN table2 b
ON a.id = b.id;
```

### **4. FULL OUTER JOIN** (All rows when there is a match in either table)
```sql
SELECT a.column1, b.column2
FROM table1 a
FULL OUTER JOIN table2 b
ON a.id = b.id;
```

---

## **Subqueries**
```sql
SELECT *
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees); -- Subquery for average salary
```

---

## **Window Functions**

### **1. Ranking**
```sql
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

### **2. Running Totals**
```sql
SELECT name, salary,
       SUM(salary) OVER (PARTITION BY department ORDER BY id) AS cumulative_salary
FROM employees;
```

---

## **Inserting and Updating Data**

### **1. Insert Data**
```sql
INSERT INTO table_name (column1, column2)
VALUES ('value1', 'value2');
```

### **2. Update Data**
```sql
UPDATE table_name
SET column1 = 'new_value'
WHERE column2 = 'value';
```

### **3. Delete Data**
```sql
DELETE FROM table_name
WHERE column_name = 'value';
```

---

## **Indexes**
```sql
CREATE INDEX index_name
ON table_name (column_name);

DROP INDEX index_name;
```

---

## **Views**
```sql
CREATE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE condition;

DROP VIEW view_name;
```

---

## **Backup & Restore**

### **Export Data**
```sql
SELECT * INTO OUTFILE '/tmp/data.csv'
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
FROM table_name;
```

### **Import Data**
```sql
LOAD DATA INFILE '/tmp/data.csv'
INTO TABLE table_name
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n';
```

---

## **Tips**
1. **Test Queries Safely:** Use **SELECT** first before **DELETE** or **UPDATE**.
2. **Index Key Columns:** Optimize query performance for large datasets.
3. **Backup Regularly:** Always save snapshots before data modifications.
4. **Use Aliases:** Simplify queries with table or column aliases.
5. **Comment Your Code:** Document queries for clarity and future use.

---

This cheat sheet summarizes the most important SQL commands and techniques for everyday tasks.

CompliedByUdithaWICK
