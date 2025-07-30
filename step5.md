# 🧠 STEP 5: Advanced SELECT — Aggregates, GROUP BY, HAVING

---

## 🔶 1. Aggregate Functions

These functions **reduce** rows into a single summary value:

| Function  | Description               |
| --------- | ------------------------- |
| `COUNT()` | Number of rows            |
| `SUM()`   | Total of numeric values   |
| `AVG()`   | Average of numeric values |
| `MIN()`   | Smallest value            |
| `MAX()`   | Largest value             |

### ✅ Examples:

```sql
SELECT COUNT(*) FROM users;
-- Total number of users

SELECT AVG(price) FROM products;
-- Average price of all products

SELECT MIN(created_at), MAX(created_at) FROM orders;
-- First and last order dates
```

---

## 🔶 2. GROUP BY — Aggregation by Category

```sql
SELECT country, COUNT(*) AS user_count
FROM users
GROUP BY country;
```

> ✅ This groups all users by `country`, then counts how many per group.

### Example: Total sales per category

```sql
SELECT category, SUM(price * quantity) AS total_sales
FROM products
GROUP BY category;
```

---

## ⚠️ Rules of `GROUP BY`:

- All **non-aggregated columns must be in GROUP BY**.
- `GROUP BY` must come **before HAVING**, `ORDER BY`, `LIMIT`.

---

## 🔶 3. HAVING — Filter After Grouping

While `WHERE` filters **rows before grouping**, `HAVING` filters **groups after aggregation**.

### Example: Countries with more than 100 users

```sql
SELECT country, COUNT(*) AS user_count
FROM users
GROUP BY country
HAVING COUNT(*) > 100;
```

---

### Example: Products with average rating < 3

```sql
SELECT product_id, AVG(rating) AS avg_rating
FROM reviews
GROUP BY product_id
HAVING AVG(rating) < 3;
```

---

## 🔶 4. GROUP BY Multiple Columns

```sql
SELECT country, city, COUNT(*) AS users
FROM users
GROUP BY country, city;
```

> You can group by multiple levels (e.g., first country, then city).

---

## 🔶 5. Combining GROUP BY with ORDER BY

```sql
SELECT category, COUNT(*) AS product_count
FROM products
GROUP BY category
ORDER BY product_count DESC
LIMIT 5;
```

> Most common usage in reports: **Top 5** of something.

---

## 🔶 6. Using Expressions in GROUP BY

```sql
SELECT DATE(created_at) AS signup_date, COUNT(*) AS users
FROM users
GROUP BY DATE(created_at)
ORDER BY signup_date;
```

---

## ⚠️ Common Mistakes

| Mistake                                | Why it's a problem                        |
| -------------------------------------- | ----------------------------------------- |
| Selecting a column not in `GROUP BY`   | SQL error — must be aggregated or grouped |
| Using `WHERE` to filter aggregates     | Wrong — use `HAVING`                      |
| Forgetting alias for calculated column | Output is unclear or unreadable           |
| Grouping too broadly or narrowly       | Can return incorrect summary              |

---

## ✅ Best Practices

- Always alias your aggregate columns (`AS total_sales`)
- Use `HAVING` for any condition on aggregates (e.g., `HAVING COUNT(*) > 10`)
- Always sort (`ORDER BY`) when generating ranked reports

---

## 🔜 Next Step

**Step 6: Joins and Set Operations**

- `INNER`, `LEFT`, `RIGHT`, `FULL OUTER` joins
- Self joins
- `UNION`, `INTERSECT`, `EXCEPT`
- Real-world schema examples (users, orders, products)
