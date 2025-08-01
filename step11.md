# 🧠 **Step 11: Views & Materialized Views**

In SQL, **Views** and **Materialized Views** are powerful tools that let you encapsulate complex queries and present them like tables. They can simplify logic, improve security, and boost performance (when used right).

---

## 🔷 1. What Is a View?

A **View** is a virtual table based on the result of a `SELECT` query.
It does **not** store data — just the query logic.

### ✅ Use Cases

- Abstracting complex joins
- Providing read-only access
- Enforcing security by hiding columns
- Reusing query logic

### 🔸 Example

```sql
CREATE VIEW active_users AS
SELECT id, name, email
FROM users
WHERE is_active = true;
```

You can now:

```sql
SELECT * FROM active_users;
```

> Think of a view like a **named SELECT query**.

---

## 🔷 2. Updating Views

Views **can be updatable** under certain conditions:

- Based on a single table
- No aggregates, `DISTINCT`, `GROUP BY`, etc.

You can also define **INSTEAD OF** [triggers](./step10.md) to make non-updatable views writable.

---

## 🔷 3. What Is a Materialized View?

Unlike regular views, a **Materialized View (MV)** stores the **query result** as actual data.

### ✅ Use Cases

- Expensive aggregations (e.g., analytics)
- Data snapshots
- Cached complex joins or filtered subsets

### 🔸 Example (PostgreSQL)

```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT
    customer_id,
    SUM(total_amount) AS total_spent,
    DATE_TRUNC('month', created_at) AS sale_month
FROM sales
GROUP BY customer_id, sale_month;
```

Query it like a table:

```sql
SELECT * FROM monthly_sales;
```

### 🔄 Refreshing the Data

```sql
REFRESH MATERIALIZED VIEW monthly_sales;
```

> This reloads the MV by re-running its underlying query.

You can also use:

```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_sales;
```

> ✅ Allows read access during refresh (requires unique index)

---

## 🔷 4. Key Differences

| Feature                        | View                   | Materialized View             |
| ------------------------------ | ---------------------- | ----------------------------- |
| Stores data                    | ❌ No                  | ✅ Yes                        |
| Always up-to-date              | ✅ Yes (on each query) | ❌ No (must manually refresh) |
| Fast queries                   | ❌ Depends on query    | ✅ Very fast                  |
| Supports [indexes](./step9.md) | ❌ No                  | ✅ Yes                        |
| Writable?                      | ⚠️ Sometimes           | ❌ No                         |

---

## 🔷 5. Real-World Examples

### 🧾 Example 1: Masked Data for Security

```sql
CREATE VIEW masked_users AS
SELECT id, name, LEFT(email, 3) || '***@***.com' AS email
FROM users;
```

> 🔐 Exposes only partial info to analysts.

---

### 📊 Example 2: Dashboard Reporting (Materialized)

```sql
CREATE MATERIALIZED VIEW daily_user_stats AS
SELECT
    DATE(created_at) AS date,
    COUNT(*) AS new_users
FROM users
GROUP BY date;
```

Schedule refresh with a CRON job or event trigger.

---

## 🔷 6. Best Practices

✅ **Use views for abstraction/security**, not performance
✅ Use **materialized views for heavy aggregations**
✅ Always **refresh MVs when needed**
✅ Use **indexes** on MVs for fast filtering
✅ For read-only analytics, MVs are gold

---

## 🔥 Common Mistakes

| Mistake                              | Why It’s Bad                               |
| ------------------------------------ | ------------------------------------------ |
| Thinking views improve performance   | They don’t unless the base query is simple |
| Forgetting to refresh MVs            | Stale data in dashboards                   |
| Overusing MVs for fast-changing data | Refreshing too often adds overhead         |
| Not indexing MVs                     | Slows down query performance               |

---

## 🔧 Tooling Tips

- PostgreSQL: Use `pg_matviews` to monitor
- MySQL: MVs not natively supported (emulate with triggers)
- SQL Server: Has indexed views (MV-like behavior)
- Materialize (DB): Realtime materialized views

---

## 🧩 Summary

| Concept           | Purpose                                  |
| ----------------- | ---------------------------------------- |
| View              | Abstract queries, hide sensitive data    |
| Materialized View | Precomputed, stored data for fast access |

They are crucial tools in designing **clean**, **performant**, and **secure** data systems.

---

✅ Ready for Step 12: [**Security & Permissions**](./step12.md)?
