Here are **more SQL examples** demonstrating practical use cases of **window functions** in real-world scenarios:

---

## **1. Rank Top-N Salaries by Department**  
Find the **top 3 highest-paid employees** in each department.

```sql
SELECT name, department, salary, 
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees
WHERE RANK() OVER (PARTITION BY department ORDER BY salary DESC) <= 3;
```

**Explanation:**
- **RANK()** assigns ranks to salaries within each department.  
- **PARTITION BY department** groups data by department.  
- **ORDER BY salary DESC** sorts salaries in descending order.  
- Filter rows with ranks **<= 3**.

---

## **2. Calculate Cumulative Revenue by Month**  
Track **cumulative sales** by month.

```sql
SELECT order_date, customer_id, amount,
       SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS cumulative_sales
FROM sales;
```

**Explanation:**
- **SUM()** calculates the running total for each customer.  
- **PARTITION BY customer_id** ensures calculations are separate for each customer.  
- **ORDER BY order_date** calculates totals incrementally by date.

---

## **3. Compare Current and Previous Month Sales**  
Analyze **month-over-month growth**.

```sql
SELECT order_date, 
       SUM(amount) AS monthly_sales,
       LAG(SUM(amount)) OVER (ORDER BY order_date) AS prev_month_sales,
       SUM(amount) - LAG(SUM(amount)) OVER (ORDER BY order_date) AS growth
FROM sales
GROUP BY order_date;
```

**Explanation:**
- **LAG()** retrieves sales from the previous month.  
- **SUM(amount)** calculates monthly sales.  
- **Growth** is calculated as the difference between current and previous month sales.

---

## **4. Running Average for Sales (Moving Window)**  
Calculate **3-month moving average sales**.

```sql
SELECT order_date, customer_id, amount,
       AVG(amount) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
FROM sales;
```

**Explanation:**
- **ROWS BETWEEN 2 PRECEDING AND CURRENT ROW** defines a 3-row window (current + previous 2 rows).  
- **AVG()** computes the average for each moving window.

---

## **5. Find Top 3 Products by Revenue per Region**  
Identify the **best-performing products** by region.

```sql
SELECT region, product_id, SUM(revenue) AS total_revenue,
       RANK() OVER (PARTITION BY region ORDER BY SUM(revenue) DESC) AS rank
FROM sales
GROUP BY region, product_id
HAVING RANK() OVER (PARTITION BY region ORDER BY SUM(revenue) DESC) <= 3;
```

**Explanation:**
- Groups data by **region** and **product_id**.  
- Ranks products within each region based on **total revenue**.  
- Filters rows to include only the **top 3** products.

---

## **6. Salary Percentile Calculation**  
Calculate each employee's **percentile rank** in terms of salary.

```sql
SELECT name, salary,
       PERCENT_RANK() OVER (ORDER BY salary DESC) AS percentile_rank
FROM employees;
```

**Explanation:**
- **PERCENT_RANK()** calculates the relative rank of each row as a percentage.  
- **ORDER BY salary DESC** ranks by descending salary values.

---

## **7. Sales Performance Based on Previous Year**  
Compare **current year sales** with the **previous year**.

```sql
SELECT year, SUM(amount) AS sales,
       LAG(SUM(amount)) OVER (ORDER BY year) AS prev_year_sales,
       SUM(amount) - LAG(SUM(amount)) OVER (ORDER BY year) AS growth
FROM sales
GROUP BY year;
```

**Explanation:**
- **LAG()** gets the sales of the previous year.  
- Calculates **growth** by subtracting previous year sales from the current year.

---

## **8. Customer Retention Analysis**  
Track whether customers made **repeat purchases**.

```sql
SELECT customer_id, order_date,
       LEAD(order_date) OVER (PARTITION BY customer_id ORDER BY order_date) AS next_purchase_date,
       DATEDIFF(LEAD(order_date) OVER (PARTITION BY customer_id ORDER BY order_date), order_date) AS days_between
FROM orders;
```

**Explanation:**
- **LEAD()** finds the **next purchase date** for each customer.  
- **DATEDIFF()** calculates the number of days between purchases.  
- Useful for retention and churn analysis.

---

## **9. Find Employees with the Highest Salaries per Department**  
Identify the **highest salary earner** in each department.

```sql
SELECT name, department, salary
FROM (
    SELECT name, department, salary,
           RANK() OVER(PARTITION BY department ORDER BY salary DESC) AS rank
    FROM employees
) ranked
WHERE rank = 1;
```

**Explanation:**
- **RANK()** assigns salaries in descending order for each department.  
- Filters to include only the **top earners** in each department.

---

## **10. Calculate Revenue Contribution Percentage**  
Find what **percentage of total revenue** each sale contributes.

```sql
SELECT order_id, amount,
       SUM(amount) OVER() AS total_sales,
       amount * 100.0 / SUM(amount) OVER() AS percent_of_total
FROM sales;
```

**Explanation:**
- **SUM(amount) OVER()** calculates the total revenue for all rows.  
- Each sale's percentage is computed as a share of the total.

---

## **11. Detect Data Anomalies Using Window Functions**  
Find outliers in salaries by **comparing each salary to the median**.

```sql
SELECT name, salary,
       PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) OVER() AS median_salary,
       salary - PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) OVER() AS deviation_from_median
FROM employees;
```

**Explanation:**
- **PERCENTILE_CONT(0.5)** calculates the **median salary**.  
- Computes deviation of each salary from the median to detect anomalies.

---

### **Summary of Use Cases:**
1. **Ranking Data:** Rank employees or products based on performance.  
2. **Cumulative Totals:** Track running totals and revenue growth.  
3. **Moving Averages:** Smooth trends using moving averages.  
4. **Percentiles:** Measure relative performance.  
5. **Comparisons:** Analyze year-over-year or month-over-month growth.  
6. **Retention Analysis:** Detect repeat customer behavior.  
7. **Outlier Detection:** Identify anomalies using deviations.

---

