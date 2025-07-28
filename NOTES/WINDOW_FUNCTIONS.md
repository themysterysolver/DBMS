# 🪟 SQL Window Functions Cheat Sheet

---

## ✅ What Are Window Functions?

- **Window functions** perform calculations **across a set of rows related to the current row**, without collapsing rows (unlike `GROUP BY`).
- They are used with the `OVER()` clause, which defines the *"window"* of rows to operate on.
- Window functions compute values over a group (window) of rows that are related to the current row.
- They do not group or filter rows like `GROUP BY` —instead, they ***add extra information*** to each row.
- All window functions use the `OVER()` clause.

---

## 📌 Common Window Functions

| Function         | Description                               |
|------------------|-------------------------------------------|
| `ROW_NUMBER()`   | Unique row number per partition/order     |
| `RANK()`         | Ranking with possible gaps                |
| `DENSE_RANK()`   | Ranking without gaps                      |
| `NTILE(n)`       | Divides rows into *n* buckets             |
| `LEAD()`         | Looks at the **next** row                 |
| `LAG()`          | Looks at the **previous** row             |
| `SUM()`, `AVG()` | Can act as window functions with `OVER()` |

> ✅ These are called **Window Functions** (also known as **Analytic Functions** in some databases like Oracle).

---

## 🧠 Syntax Template

```sql
<function_name>() OVER (
    PARTITION BY <column>
    ORDER BY <column>
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```
## 🔹 What is `OVER()`?

- The `OVER()` clause defines the **window (range of rows)** that a window function should operate over.
---

### ✅ Syntax:
```sql
<window_function>() OVER (
    PARTITION BY <column>
    ORDER BY <column>
)
```
### 🧠 Notes:
- `OVER` is a required keyword in window functions.
- `PARTITION BY` divides rows into groups (optional).
- `ORDER BY` determines the sequence of rows within each partition (optional but recommended for ranking).

---

# 🧩 1. ROW_NUMBER()

- Gives a unique sequential number to each row based on the ORDER BY.  
- It ignores ties—no two rows can have the same number.

```sql
SELECT name, salary,
       ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num
FROM Employee;
```

| name  | salary | row_num |
|-------|--------|---------|
| Alice |   9000 |       1 |
| Bob   |   8000 |       2 |
| Carol |   8000 |       3 |
| Dave  |   7000 |       4 |

---

# 🧩 2. RANK()

Gives ranks to rows based on the ORDER BY, but ties get the same rank, and skips the next rank(s).

```sql
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS rank
FROM Employee;
```

| name  | salary | row_num |
|-------|--------|---------|
| Alice |   9000 |       1 |
| Bob   |   8000 |       2 |
| Carol |   8000 |       2 |
| Dave  |   7000 |       4 |

---

# 🧩 3. DENSE_RANK()

Similar to `RANK()`, but without skipping numbers after ties - maintains a consecutive ranking sequence.

## Key Characteristics:
- Assigns the same rank to tied values (like `RANK()`)
- **Does not skip** subsequent ranks after ties
- Creates a "dense" ranking where all rank values are consecutive integers

```sql
SELECT name, salary,
       DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM Employee;
```

| name  | salary | row_num |
|-------|--------|---------|
| Alice |   9000 |       1 |
| Bob   |   8000 |       2 |
| Carol |   8000 |       2 |
| Dave  |   7000 |       3 |

---

| Function       | Handles Ties? | Skips Numbers? | Sequence Example |
|----------------|---------------|----------------|------------------|
| `ROW_NUMBER()` | No            | No             | 1, 2, 3, 4       |
| `RANK()`       | Yes           | Yes            | 1, 2, 2, 4       |
| `DENSE_RANK()` | Yes           | No             | 1, 2, 2, 3       |

---

# 🧩 4. LEAD()

Accesses data from subsequent rows in the result set without a self-join. Returns the value from a specified offset row "ahead" of the current row.

## Key Features:
- **Forward-looking** window function
- Returns NULL when looking beyond the last row
- Often used for comparing current row with next row
- Supports optional offset (default 1) and default value parameters

```sql
SELECT name, salary,
       LEAD(salary) OVER (ORDER BY salary DESC) AS next_salary
FROM Employee;
```

| name  | salary | next_salary |
| ----- | ------ | ------------ |
| Alice | 9000   | 8000         |
| Bob   | 8000   | 8000         |
| Carol | 8000   | 7000         |
| Dave  | 7000   | NULL         |

---

# 🧩 5. LAG()

Accesses data from previous rows in the result set without a self-join. Returns the value from a specified offset row "behind" the current row.

## Key Features:
- **Backward-looking** window function
- Returns NULL when looking before the first row
- Often used for comparing current row with previous row
- Supports optional offset (default 1) and default value parameters

```sql
SELECT name, salary,
       LAG(salary) OVER (ORDER BY salary DESC) AS prev_salary
FROM Employee;
```

| name  | salary | prev_salary |
| ----- | ------ | ------------ |
| Alice | 9000   | NULL         |
| Bob   | 8000   | 9000         |
| Carol | 8000   | 8000         |
| Dave  | 7000   | 8000         |

---

### 🔍 PARTITION BY – Quick Summary
- `PARTITION BY` divides the result ***set into groups***.
- The window function (like RANK(), ROW_NUMBER(), etc.) resets within each group. 📌
- Think of it like GROUP BY for window functions — but it doesn’t collapse rows.

#### ✅ Syntax:

```sql
<window_function>() OVER (
  PARTITION BY <column>
  ORDER BY <column>
)
```

#### 📋 Example: Ranking within departments
```sql
SELECT name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM Employee;
```

#### 🧾 Output:

| name  | department | salary | dept\_rank |
| ----- | ---------- | ------ | ---------- |
| Alice | HR         | 9000   | 1          |
| Bob   | HR         | 8000   | 2          |
| Carol | IT         | 9500   | 1          |
| Dave  | IT         | 8800   | 2          |

> Each department has its own ranking, starting from 1.


---
## 🔎 Why Are They Useful?

Window functions help in:

- ✅ Ranking (e.g., Top N per group)

- ✅ Running totals

- ✅ Moving averages

- ✅ Gap analysis

- ✅ Comparing values across rows (LEAD() / LAG())

- ✅ Percentiles and percent ranks
