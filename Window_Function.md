### **SQL Window Functions**  
SQL **Window Functions** perform calculations across a **set of rows related to the current row** within a query result. Unlike aggregate functions, window functions do **not collapse rows**; instead, they retain individual rows while calculating values **over a specific window or partition**.

---

## **1. Basic Syntax:**
```sql
<window_function>(expression) 
OVER (
    PARTITION BY column_name
    ORDER BY column_name
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

- **PARTITION BY:** Divides rows into groups (optional).  
- **ORDER BY:** Defines the order of rows within each partition.  
- **ROWS BETWEEN:** Specifies the window frame relative to the current row.  

---

## **2. Types of Window Functions:**

### **A. Ranking Functions**
1. **ROW_NUMBER()** - Assigns a unique rank to each row.
```sql
SELECT name, department, salary,
       ROW_NUMBER() OVER(PARTITION BY department ORDER BY salary DESC) AS row_num
FROM employees;
```

2. **RANK()** - Assigns ranks; ties get the **same rank** but skip numbers.
```sql
SELECT name, salary,
       RANK() OVER(ORDER BY salary DESC) AS rank
FROM employees;
```

3. **DENSE_RANK()** - Assigns ranks; ties get the **same rank** but do **not skip numbers**.
```sql
SELECT name, salary,
       DENSE_RANK() OVER(ORDER BY salary DESC) AS dense_rank
FROM employees;
```

4. **NTILE(n)** - Divides rows into **n equal buckets**.
```sql
SELECT name, salary,
       NTILE(4) OVER(ORDER BY salary DESC) AS quartile
FROM employees;
```

---

### **B. Aggregate Functions**
Perform calculations **within a window frame** without collapsing rows.  

1. **SUM()** - Cumulative sum.
```sql
SELECT name, salary,
       SUM(salary) OVER(PARTITION BY department ORDER BY salary) AS cum_sum
FROM employees;
```

2. **AVG()** - Moving average.
```sql
SELECT name, salary,
       AVG(salary) OVER(PARTITION BY department ORDER BY salary ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM employees;
```

3. **MIN() / MAX()** - Running minimum/maximum.
```sql
SELECT name, salary,
       MIN(salary) OVER(PARTITION BY department ORDER BY salary) AS min_salary,
       MAX(salary) OVER(PARTITION BY department ORDER BY salary) AS max_salary
FROM employees;
```

4. **COUNT()** - Cumulative count.
```sql
SELECT name, salary,
       COUNT(*) OVER(PARTITION BY department ORDER BY salary) AS running_count
FROM employees;
```

---

### **C. Value Functions**
1. **LAG()** - Accesses **previous row's value**.
```sql
SELECT name, salary,
       LAG(salary, 1, 0) OVER(ORDER BY salary) AS prev_salary
FROM employees;
```

2. **LEAD()** - Accesses **next row's value**.
```sql
SELECT name, salary,
       LEAD(salary, 1, 0) OVER(ORDER BY salary) AS next_salary
FROM employees;
```

3. **FIRST_VALUE()** - Returns the **first value** in the window.
```sql
SELECT name, salary,
       FIRST_VALUE(salary) OVER(ORDER BY salary) AS lowest_salary
FROM employees;
```

4. **LAST_VALUE()** - Returns the **last value** in the window.
```sql
SELECT name, salary,
       LAST_VALUE(salary) OVER(ORDER BY salary ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS highest_salary
FROM employees;
```

---

## **3. Frame Clauses in Windows**

### **ROWS vs RANGE Clauses**
- **ROWS:** Counts rows **physically** (fixed window size).  
- **RANGE:** Considers rows with **same values** in ordering (dynamic size).  

Examples:

1. **Cumulative Sum (ROWS):**
```sql
SELECT name, salary,
       SUM(salary) OVER(ORDER BY salary ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cum_sum
FROM employees;
```

2. **Moving Average (RANGE):**
```sql
SELECT name, salary,
       AVG(salary) OVER(ORDER BY salary RANGE BETWEEN INTERVAL 1 DAY PRECEDING AND CURRENT ROW) AS moving_avg
FROM sales;
```

---

## **4. Use Cases of Window Functions**

1. **Find Top-N Rows per Group:**
```sql
SELECT name, department, salary
FROM (
    SELECT name, department, salary,
           RANK() OVER(PARTITION BY department ORDER BY salary DESC) AS rank
    FROM employees
) subquery
WHERE rank <= 3;
```

2. **Cumulative Sales Analysis:**
```sql
SELECT order_date, customer_id, sales_amount,
       SUM(sales_amount) OVER(PARTITION BY customer_id ORDER BY order_date) AS cumulative_sales
FROM sales;
```

3. **Salary Differences:**
```sql
SELECT name, salary,
       salary - LAG(salary, 1) OVER(ORDER BY salary) AS salary_diff
FROM employees;
```

4. **Running Total and Percentages:**
```sql
SELECT name, salary,
       SUM(salary) OVER(ORDER BY salary) AS running_total,
       salary * 100.0 / SUM(salary) OVER() AS percent_of_total
FROM employees;
```

---

## **5. Advantages of Window Functions**

1. **Preserve Individual Rows:** Unlike **GROUP BY**, it keeps detailed row-level data.  
2. **Flexibility:** Supports **ranking, cumulative sums, and comparisons** without subqueries.  
3. **Performance:** Efficient for analytical tasks like **time series** and **reporting dashboards**.  

---

CompiledByUdithaWICK
