
# 📘 MySQL Common Table Expressions (CTEs)

## 🧠 What is a CTE?

A **Common Table Expression (CTE)** is a temporary named result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. Introduced in **MySQL 8.0**, CTEs allow for modular and more readable SQL queries.

---

## 📌 Syntax

```sql
WITH cte_name (column1, column2, ...) AS (
    SELECT ...
)
SELECT ...
FROM cte_name;
```

- `WITH`: Introduces the CTE.
- `cte_name`: Name of the temporary result set.
- `(column1, column2, ...)`: Optional column names.
- The CTE can be used in the final query.

---

## ✅ Use Case Examples

### 🔹 1. Simplifying Complex Queries

**Problem**: Find departments where the average salary is greater than $60,000.

**Schema**:
```sql
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    department VARCHAR(50),
    salary DECIMAL(10, 2)
);
```

**Query**:
```sql
WITH department_salaries AS (
    SELECT department, 
           SUM(salary) AS total_salary, 
           AVG(salary) AS average_salary
    FROM employees
    GROUP BY department
)
SELECT department, total_salary, average_salary
FROM department_salaries
WHERE average_salary > 60000;
```

---

### 🔹 2. Recursive CTE for Hierarchical Data

**Schema**:
```sql
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    parent_id INT,
    FOREIGN KEY (parent_id) REFERENCES categories(id)
);
```

**Query**:
```sql
WITH RECURSIVE category_hierarchy AS (
    SELECT id, name, parent_id, 1 AS level
    FROM categories
    WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.name, c.parent_id, ch.level + 1
    FROM categories c
    JOIN category_hierarchy ch ON c.parent_id = ch.id
)
SELECT id, name, parent_id, level
FROM category_hierarchy;
```

---

### 🔹 3. Temporary Aggregation

**Schema**:
```sql
CREATE TABLE sales (
    id INT AUTO_INCREMENT PRIMARY KEY,
    salesperson_id INT,
    sales_amount DECIMAL(10, 2)
);
```

**Query**:
```sql
WITH total_sales AS (
    SELECT salesperson_id, SUM(sales_amount) AS total_sales
    FROM sales
    GROUP BY salesperson_id
)
SELECT salesperson_id, total_sales
FROM total_sales
WHERE total_sales > 5000;
```

---

## 🌟 Benefits of CTEs

- ✅ **Readability**: Breaks down complex logic into readable blocks.
- 🛠 **Maintainability**: Modular and easy to update/debug.
- 🔁 **Reusability**: Use the same intermediate result in multiple places.
- 🌳 **Recursion Support**: Perfect for hierarchical or tree-like data.
- 📊 **Temporary Aggregation**: Simplifies intermediate calculations.

---

## 🧾 Conclusion

CTEs are powerful tools in SQL that improve code quality, readability, and maintainability. Whether you're performing aggregations, building hierarchies, or simplifying nested queries, CTEs can make your SQL more expressive and efficient.

> ⚠️ Note: CTEs require **MySQL version 8.0+**.

---
