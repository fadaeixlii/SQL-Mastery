# 🧩 **STEP 6: JOINs and SET Operations**

Understanding JOINs is essential for working with **normalized databases**. You’ll use joins anytime you work with multiple related tables.

---

## 🔷 PART 1: JOINS — Combining Data from Multiple Tables

---

### 🔹 1. INNER JOIN (default join)

Returns only rows that have **matching values in both tables**.

```sql
SELECT users.name, orders.total
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```

> ✅ This returns only users who **have orders**.

---

### 🔹 2. LEFT JOIN (LEFT OUTER JOIN)

Returns **all rows from the left table**, and matching rows from the right. If there's no match, returns `NULL` for right side.

```sql
SELECT users.name, orders.total
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

> ✅ Useful for: "All users, even if they have no orders"

---

### 🔹 3. RIGHT JOIN (RIGHT OUTER JOIN)

Same as `LEFT JOIN`, but reversed.

```sql
SELECT users.name, orders.total
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;
```

> ✅ Not used often — you can usually flip table order and use `LEFT JOIN`.

---

### 🔹 4. FULL OUTER JOIN

Returns **all rows** from both tables. Where no match exists, returns `NULL`.

```sql
SELECT users.name, orders.total
FROM users
FULL OUTER JOIN orders ON users.id = orders.user_id;
```

> 🔴 Not supported in all SQL dialects (e.g., MySQL). Use `UNION` workaround.

---

### 🔹 5. CROSS JOIN

Returns the **Cartesian product** of the two tables (every row with every other row).

```sql
SELECT a.name, b.name
FROM students a
CROSS JOIN teachers b;
```

> ⚠️ Usually avoided unless generating combinations.

---

### 🔹 6. SELF JOIN

A table joined to itself.

```sql
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.id;
```

---

### 🔹 7. USING vs ON

If both tables have a column with the same name:

```sql
-- Same as ON users.id = orders.user_id
SELECT * FROM users JOIN orders USING (id);
```

But `USING` only works when the column name is identical in both tables.

---

### 🔹 Real-World Example

Schema:

- `users(id, name)`
- `orders(id, user_id, total)`
- `products(id, name)`
- `order_items(id, order_id, product_id, quantity)`

✅ Total amount per user:

```sql
SELECT u.name, SUM(o.total) AS total_spent
FROM users u
JOIN orders o ON u.id = o.user_id
GROUP BY u.name;
```

✅ Products in each order:

```sql
SELECT o.id AS order_id, p.name AS product, oi.quantity
FROM orders o
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id;
```

---

## 🔷 PART 2: SET OPERATIONS

---

### 🔹 1. UNION

Combines rows from two queries, removing duplicates.

```sql
SELECT name FROM customers
UNION
SELECT name FROM suppliers;
```

> Returns **unique** names from both tables.

---

### 🔹 2. UNION ALL

Same as `UNION`, but keeps duplicates.

```sql
SELECT name FROM customers
UNION ALL
SELECT name FROM suppliers;
```

---

### 🔹 3. INTERSECT

Returns only rows **common to both queries**.

```sql
SELECT email FROM customers
INTERSECT
SELECT email FROM newsletter_subscribers;
```

> ✅ Who is both a customer and newsletter subscriber

---

### 🔹 4. EXCEPT (or MINUS)

Returns rows from the first query that are **not in the second**.

```sql
SELECT email FROM customers
EXCEPT
SELECT email FROM newsletter_subscribers;
```

> ✅ Customers who did **not** subscribe

---

## ⚠️ Common Mistakes

| Mistake                                         | Why it’s wrong                                 |
| ----------------------------------------------- | ---------------------------------------------- |
| Forgetting `ON` condition in JOIN               | Cartesian product, massive row count           |
| Mixing `LEFT JOIN` with `WHERE col IS NOT NULL` | Cancels the left join                          |
| `UNION` when `UNION ALL` is needed              | Performance hit from unnecessary deduping      |
| Misusing `INTERSECT/EXCEPT` in MySQL            | Not supported — emulate with `JOIN/NOT EXISTS` |

---

## ✅ Best Practices

- Prefer `INNER JOIN` unless you explicitly need all rows from one side.
- Avoid ambiguous column names by using aliases (`u.name`, `o.total`).
- Always define `ON` clauses clearly and avoid Cartesian joins.
- Use `UNION ALL` when duplicates are acceptable (it's faster).

---

## 🔜 Next Step

**Step 7: Subqueries, Derived Tables & Common Table Expressions (CTEs)**

- Writing nested `SELECT` queries
- Using CTEs (`WITH`) for clarity and reuse
- Filtering with `EXISTS`, `NOT EXISTS`, `IN`, `NOT IN`
