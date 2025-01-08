Here are **additional examples** for handling **duplicates** in SQL with more **use cases** and explanations.

---

## **1. Detecting Exact Duplicates (All Columns Match)**  
Find rows where all column values are identical.

```sql
SELECT *, COUNT(*) AS count
FROM employees
GROUP BY id, name, age, department, salary
HAVING COUNT(*) > 1;
```
**Explanation:**
- Groups rows by all columns.  
- Filters groups with a **count > 1**, indicating duplicates.

---

## **2. Detecting Partial Duplicates (Based on Specific Columns)**  
Find duplicates based only on specific columns (e.g., **name** and **department**).

```sql
SELECT name, department, COUNT(*) AS count
FROM employees
GROUP BY name, department
HAVING COUNT(*) > 1;
```
**Use Case:** Check if employees have been assigned to the **same department** multiple times.

---

## **3. Deleting Exact Duplicates**  
Keep only **one occurrence** of exact duplicates and delete others.

```sql
DELETE FROM employees
WHERE id NOT IN (
    SELECT MIN(id)
    FROM employees
    GROUP BY name, department, salary
);
```
**Explanation:**
- Groups duplicates and keeps the row with the **minimum ID**.  
- Deletes all others.

---

## **4. Delete Partial Duplicates**  
Remove duplicates based on **name** and **department**, keeping the **lowest salary**.

```sql
DELETE FROM employees
WHERE id NOT IN (
    SELECT MIN(id)
    FROM employees
    GROUP BY name, department
);
```

---

## **5. Handling Duplicates with ROW_NUMBER()**

### **a. Identify Duplicates Using ROW_NUMBER()**
```sql
SELECT *,
       ROW_NUMBER() OVER(PARTITION BY name, department ORDER BY id) AS row_num
FROM employees;
```
**Explanation:**
- Assigns a **row number** within each duplicate group.  
- Rows with **row_num > 1** are duplicates.

### **b. Remove Duplicates with ROW_NUMBER()**
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
**Use Case:** Deletes all duplicates while keeping only the **first occurrence** based on the **ID**.

---

## **6. Marking Duplicates Instead of Deleting (Soft Delete)**  
Useful when you want to **review duplicates later** instead of deleting them immediately.

```sql
ALTER TABLE employees ADD COLUMN is_duplicate BOOLEAN DEFAULT FALSE;

UPDATE employees
SET is_duplicate = TRUE
WHERE id IN (
    SELECT id
    FROM (
        SELECT id, ROW_NUMBER() OVER(PARTITION BY name, department ORDER BY id) AS row_num
        FROM employees
    ) subquery
    WHERE row_num > 1
);
```

---

## **7. Deduplicating with SELECT INTO (Copy Unique Rows)**  
Create a **new table** with unique records.

```sql
CREATE TABLE unique_employees AS
SELECT DISTINCT *
FROM employees;
```
**Use Case:** Preserves the **original table** while creating a clean version with **unique rows**.

---

## **8. Preventing Future Duplicates (Constraints)**

### **a. Add a UNIQUE Constraint**
```sql
ALTER TABLE employees
ADD CONSTRAINT unique_employee UNIQUE(name, department);
```

### **b. Add a UNIQUE Index**
```sql
CREATE UNIQUE INDEX idx_unique_employee
ON employees(name, department);
```
**Purpose:**  
- Ensures no future duplicates based on **name** and **department**.

---

## **9. Deduplicating Joins**

### **a. Find Duplicate Rows in Joins**
```sql
SELECT e1.*
FROM employees e1
JOIN employees e2
ON e1.name = e2.name AND e1.department = e2.department
WHERE e1.id > e2.id;
```

### **b. Delete Duplicate Rows from Joins**
```sql
DELETE e1
FROM employees e1
JOIN employees e2
ON e1.name = e2.name AND e1.department = e2.department
WHERE e1.id > e2.id;
```
**Explanation:**
- Deletes rows with **higher IDs**, retaining only **one copy**.

---

## **10. Finding Duplicate Records Based on Dates**

### **a. Detect Duplicate Orders by Date**
```sql
SELECT order_id, customer_id, order_date, COUNT(*) AS count
FROM orders
GROUP BY customer_id, order_date
HAVING COUNT(*) > 1;
```

### **b. Keep Only the Latest Order for Each Customer**
```sql
SELECT *
FROM (
    SELECT *, ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date DESC) AS row_num
    FROM orders
) subquery
WHERE row_num = 1;
```
**Explanation:**
- Uses **ROW_NUMBER()** to keep the most recent order for each customer.

---

## **11. Handling Time-Based Duplicates (Retention Analysis)**

### **a. Find Customers with Multiple Purchases**
```sql
SELECT customer_id, COUNT(*) AS purchase_count
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 1;
```

### **b. Identify Consecutive Purchases (Churn Analysis)**
```sql
SELECT customer_id, order_date,
       LEAD(order_date) OVER(PARTITION BY customer_id ORDER BY order_date) AS next_order_date,
       DATEDIFF(LEAD(order_date) OVER(PARTITION BY customer_id ORDER BY order_date), order_date) AS days_between
FROM orders;
```
**Use Case:**  
- Tracks the number of days between purchases to identify **customer churn patterns**.

---

## **12. Exporting Unique Rows (Backup)**

### **Export Unique Data:**
```sql
SELECT DISTINCT * 
INTO OUTFILE '/tmp/unique_employees.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
FROM employees;
```

---

## **Best Practices for Duplicate Handling**

1. **Analyze Before Deleting:** Always inspect duplicates with SELECT before DELETE.  
2. **Backup Tables:** Use SELECT INTO or create snapshots before modifying data.  
3. **Use Temporary Tables:** Create temp tables for intermediate results when testing queries.  
4. **Monitor Logs:** Review logs to prevent accidental duplicate insertion.  
5. **Constraints:** Use **PRIMARY KEYS**, **UNIQUE constraints**, and **INDEXES** to avoid future duplicates.

---

CompiledByUdithaWICK
