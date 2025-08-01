# 🧠 **STEP 7: Advanced Query Techniques**

## Subqueries, CTEs, Derived Tables, and Advanced Filtering

These techniques give you **modular**, **composable**, and **powerful** query logic.

---

squence

## 🔹 1. **Subqueries (Nested Queries)**

A subquery is a query **inside** another query.

### 🧾 Types of Subqueries

---

### ✅ **Scalar Subquery** (returns single value)

```sql
SELECT name,
  (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) AS order_count
FROM users u;
```

> For each user, count how many orders they placed.

---

### ✅ **Column Subquery** (returns one column)

```sql
SELECT name
FROM users
WHERE id IN (SELECT user_id FROM orders);
```

> Users who placed orders.

---

### ✅ **Row Subquery** (multiple columns)

```sql
SELECT * FROM products
WHERE (category_id, price) =
  (SELECT category_id, MAX(price) FROM products);
```

> Most expensive product in a category.

---

### ✅ **Correlated Subquery**

References data from outer query — re-evaluated for each row.

```sql
SELECT name
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

> Returns users **only if they have orders**.

---

## ⚠️ Common Subquery Mistakes

- ❌ Using `=` with subquery returning multiple rows
- ❌ Using correlated subqueries where a `JOIN` is better (performance hit)

---

## 🔹 2. **Derived Tables (Inline Views)**

Use a subquery **as a table** in the `FROM` clause.

```sql
SELECT category, avg_price
FROM (
  SELECT category, AVG(price) AS avg_price
  FROM products
  GROUP BY category
) AS category_avg
WHERE avg_price > 100;
```

> Filters product categories by average price.

---

## 🔹 3. **Common Table Expressions (CTEs)**

Write reusable query logic with `WITH` clause.

```sql
WITH category_avg AS (
  SELECT category, AVG(price) AS avg_price
  FROM products
  GROUP BY category
)
SELECT * FROM category_avg WHERE avg_price > 100;
```

> Same as derived table, but **cleaner and more modular**.

---

### 🧱 You can chain multiple CTEs:

```sql
WITH top_orders AS (
  SELECT * FROM orders ORDER BY total DESC LIMIT 10
),
order_users AS (
  SELECT o.*, u.name FROM top_orders o JOIN users u ON u.id = o.user_id
)
SELECT * FROM order_users;
```

---

## 🔁 **Recursive CTEs**

Model **hierarchies** (e.g., org charts, file systems).

```sql
WITH RECURSIVE manager_tree AS (
  SELECT id, name, manager_id FROM employees WHERE id = 1
  UNION ALL
  SELECT e.id, e.name, e.manager_id
  FROM employees e
  JOIN manager_tree mt ON mt.id = e.manager_id
)
SELECT * FROM manager_tree;
```

> Builds tree under employee with `id = 1`.

---

## 🔍 4. **Filtering with IN, EXISTS, NOT EXISTS**

### ✅ IN / NOT IN

```sql
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders);
```

> Users who have placed orders.

⚠️ `NOT IN` fails if subquery returns `NULL`. Use `NOT EXISTS` instead.

---

### ✅ EXISTS / NOT EXISTS (more efficient)

```sql
SELECT * FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```

> ✅ Efficient for large datasets.

---

## ✅ Best Practices

- Use CTEs for **readability and reuse**
- Use `JOIN`s over correlated subqueries when possible
- Use `EXISTS` over `IN` for performance
- Always name derived tables and CTEs clearly

---

## 🛠 Real-World Use Case

```sql
WITH recent_orders AS (
  SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '30 days'
),
user_stats AS (
  SELECT user_id, COUNT(*) AS order_count, SUM(total) AS total_spent
  FROM recent_orders GROUP BY user_id
)
SELECT u.name, us.order_count, us.total_spent
FROM user_stats us
JOIN users u ON u.id = us.user_id
ORDER BY total_spent DESC;
```

> Shows top spenders in the last 30 days — clean and powerful with CTEs.

---

## ✅ Up Next?

Would you like to continue to:

[**Step 8: Transactions, Isolation Levels, and Concurrency?**](./step8.md)
Covering:

- `BEGIN`, `COMMIT`, `ROLLBACK`
- Isolation levels (`READ COMMITTED`, `SERIALIZABLE`, etc.)
- Locking, deadlocks, and performance tips
