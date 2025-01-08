### **SQL Joins**  
SQL **Joins** are used to **combine rows** from two or more tables based on a **related column**. Joins are essential for **retrieving data** spread across multiple tables.

---

## **1. Types of Joins**

### **1.1 INNER JOIN**  
Returns **only matching rows** from both tables.  

```sql
SELECT e.id, e.name, d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id;
```
**Explanation:**
- Matches rows where **employees.department_id = departments.department_id**.  
- Rows without matches are **excluded**.

---

### **1.2 LEFT JOIN (LEFT OUTER JOIN)**  
Returns **all rows from the left table** and **matching rows from the right table**. Non-matching rows in the right table are filled with **NULL**.  

```sql
SELECT e.id, e.name, d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id;
```
**Use Case:**  
- Retrieve all employees, even if they **don’t belong to any department**.

---

### **1.3 RIGHT JOIN (RIGHT OUTER JOIN)**  
Returns **all rows from the right table** and **matching rows from the left table**. Non-matching rows in the left table are filled with **NULL**.  

```sql
SELECT e.id, e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department_id = d.department_id;
```
**Use Case:**  
- Retrieve **all departments**, even if they have **no employees**.

---

### **1.4 FULL OUTER JOIN**  
Returns **all rows from both tables**. Non-matching rows in either table are filled with **NULL**.  

**Note:** MySQL does not support **FULL OUTER JOIN** directly, so it can be simulated using **UNION**:

```sql
SELECT e.id, e.name, d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id

UNION

SELECT e.id, e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department_id = d.department_id;
```
**Use Case:**  
- Retrieve a **complete list of employees and departments**, showing unmatched records from both sides.

---

### **1.5 CROSS JOIN**  
Produces a **Cartesian Product** of rows, combining **all rows from both tables**.  

```sql
SELECT e.name, d.department_name
FROM employees e
CROSS JOIN departments d;
```
**Use Case:**  
- Generate all possible **combinations** between employees and departments.

---

### **1.6 SELF JOIN**  
Joins a table with **itself**.  

```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
INNER JOIN employees e2
ON e1.manager_id = e2.id;
```
**Use Case:**  
- Retrieve a **hierarchy** where employees are related to managers in the same table.

---

## **2. Practical Examples**

### **2.1 Find Employees and Their Department Names**
```sql
SELECT e.name, e.salary, d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id;
```

---

### **2.2 Find Employees Without Departments**
```sql
SELECT e.name, e.salary
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

---

### **2.3 Find Departments Without Employees**
```sql
SELECT d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department_id = d.department_id
WHERE e.id IS NULL;
```

---

### **2.4 Find Employees Working in Multiple Departments**
```sql
SELECT e.name, COUNT(d.department_id) AS department_count
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id
GROUP BY e.name
HAVING COUNT(d.department_id) > 1;
```

---

### **2.5 Calculate Total Salary by Department**
```sql
SELECT d.department_name, SUM(e.salary) AS total_salary
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id
GROUP BY d.department_name;
```

---

### **2.6 Find Employees with Their Managers (Self Join)**
```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2
ON e1.manager_id = e2.id;
```

---

## **3. Joining More Than Two Tables**

```sql
SELECT e.id, e.name, d.department_name, l.location
FROM employees e
INNER JOIN departments d
ON e.department_id = d.department_id
INNER JOIN locations l
ON d.location_id = l.location_id;
```
**Use Case:**  
- Combines **employees, departments, and locations**.

---

## **4. Nested Joins (Multiple JOIN Conditions)**

```sql
SELECT e.name, d.department_name, l.city
FROM employees e
LEFT JOIN departments d ON e.department_id = d.department_id
LEFT JOIN locations l ON d.location_id = l.location_id
WHERE l.city = 'Berlin';
```

**Use Case:**  
- Find employees based in **Berlin**.

---

## **5. Tips for Joins**

1. **Aliases:** Use aliases (**e, d**) to make queries more readable.  
2. **Indexes:** Create indexes on **foreign keys** to improve performance.  
3. **NULL Handling:** Always check for **NULL** values when using **OUTER JOINs**.  
4. **Check Results:** Test with a **LIMIT 10** query before joining large datasets.  
5. **Use Subqueries for Filtering:** Use subqueries to pre-filter data before joining, if performance slows down.

---

## **6. Real-World Use Cases**

1. **Customer Orders Analysis:**
```sql
SELECT c.customer_name, o.order_date, p.product_name
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
INNER JOIN products p ON o.product_id = p.product_id;
```

2. **Top 3 Products by Region:**
```sql
SELECT r.region_name, p.product_name, SUM(s.sales) AS total_sales,
       RANK() OVER (PARTITION BY r.region_name ORDER BY SUM(s.sales) DESC) AS rank
FROM sales s
INNER JOIN products p ON s.product_id = p.product_id
INNER JOIN regions r ON s.region_id = r.region_id
GROUP BY r.region_name, p.product_name;
```

3. **Employee Salary Growth Over Time:**
```sql
SELECT e.name, s.salary, s.salary - LAG(s.salary, 1) OVER (PARTITION BY e.id ORDER BY s.date) AS salary_growth
FROM employees e
INNER JOIN salaries s ON e.id = s.employee_id;
```

---

CompliedByUdithaWick
