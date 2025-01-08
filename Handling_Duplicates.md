### **Handling Duplicates in SQL**

Duplicates in a database can cause **data inconsistencies** and **incorrect analysis**. SQL provides several methods to **detect, remove, and manage duplicates** effectively.

---

## **1. Detecting Duplicates**

### **a. Find All Duplicate Rows**
```sql
SELECT *
FROM employees
WHERE id IN (
    SELECT id
    FROM employees
    GROUP BY id, name, age, department, salary
    HAVING COUNT(*) > 1
);
```

### **b. Count Duplicates Based on Specific Columns**
```sql
SELECT name, COUNT(*) AS count
FROM employees
GROUP BY name
HAVING COUNT(*) > 1;
```

### **c. View Duplicate Rows with All Details**
```sql
SELECT *
FROM employees e1
WHERE EXISTS (
    SELECT 1
    FROM employees e2
    WHERE e1.name = e2.name AND e1.department = e2.department
    AND e1.id > e2.id
);
```
**Explanation:**
- **EXISTS** checks if duplicate rows exist.  
- Uses comparison on specific columns (name and department).  
- Ignores duplicates based on the **id**.

---

## **2. Removing Duplicates**

### **a. Delete All Duplicate Rows (Keep One)**
```sql
DELETE FROM employees
WHERE id NOT IN (
    SELECT MIN(id)
    FROM employees
    GROUP BY name, age, department, salary
);
```
**Explanation:**
- **GROUP BY** groups data based on the specified columns.  
- **MIN(id)** keeps the first occurrence and deletes others.

---

### **b. Delete Duplicate Rows with Self-Join**
```sql
DELETE e1
FROM employees e1
JOIN employees e2
ON e1.name = e2.name AND e1.department = e2.department
WHERE e1.id > e2.id;
```
**Explanation:**
- Joins the table with itself to compare rows.  
- Deletes rows with **higher IDs** (duplicates).

---

### **c. Delete All Duplicates (No Conditions)**
```sql
DELETE FROM employees
WHERE ROWID NOT IN (
    SELECT MIN(ROWID)
    FROM employees
    GROUP BY name, department
);
```
**Note:** **ROWID** is database-specific and available in some SQL engines like **Oracle**. For others, use **ID** or **ROW_NUMBER()**.

---

## **3. Preventing Duplicates (Constraints)**

### **a. Add Unique Constraints**
```sql
ALTER TABLE employees
ADD CONSTRAINT unique_employee UNIQUE(name, department);
```
**Explanation:**
- Prevents insertion of duplicate rows with the same **name** and **department**.  

---

## **4. Using Window Functions to Handle Duplicates**

### **a. Find Duplicates with ROW_NUMBER()**
```sql
SELECT *, 
       ROW_NUMBER() OVER(PARTITION BY name, department ORDER BY id) AS row_num
FROM employees;
```
**Explanation:**
- Assigns a **row number** for each duplicate group.  
- Rows with **row_num > 1** are duplicates.

---

### **b. Delete Duplicates Using ROW_NUMBER()**
```sql
DELETE FROM employees
WHERE id IN (
    SELECT id
    FROM (
        SELECT id,
               ROW_NUMBER() OVER(PARTITION BY name, department ORDER BY id) AS row_num
        FROM employees
    ) subquery
    WHERE row_num > 1
);
```
**Explanation:**
- Keeps the first occurrence (**row_num = 1**) and deletes the rest.  

---

## **5. Soft Delete (Mark Duplicates Instead of Deleting)**

```sql
ALTER TABLE employees ADD COLUMN is_duplicate BOOLEAN DEFAULT FALSE;

UPDATE employees
SET is_duplicate = TRUE
WHERE id IN (
    SELECT id
    FROM (
        SELECT id, 
               ROW_NUMBER() OVER(PARTITION BY name, department ORDER BY id) AS row_num
        FROM employees
    ) subquery
    WHERE row_num > 1
);
```
**Explanation:**
- Adds a new column (**is_duplicate**) to **mark duplicates** instead of deleting them.  
- Useful for auditing or future cleanup.

---

## **6. Deduplication with SELECT DISTINCT**

### **a. Select Unique Rows**
```sql
SELECT DISTINCT name, department
FROM employees;
```

### **b. Insert Unique Records into a New Table**
```sql
CREATE TABLE unique_employees AS
SELECT DISTINCT *
FROM employees;
```

---

## **7. Tips for Handling Duplicates**

1. **Backup Before Deletion:** Always create a backup before removing duplicates to avoid accidental data loss.  
2. **Check Primary Keys:** Ensure primary keys are properly defined to prevent duplicates during data insertion.  
3. **Normalize Data:** Structure data to avoid redundancy by following database normalization rules.  
4. **Use Indexes:** Add unique indexes to enforce uniqueness constraints.  
5. **Audit Data Inserts:** Track inserts and monitor logs to avoid accidental duplicate entries.

---
