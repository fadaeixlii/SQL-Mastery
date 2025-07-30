## 🔍 STEP 4: SELECT — Reading Data from Tables

The `SELECT` statement allows you to **retrieve data** from one or more tables in various ways.

---

## 🔸 1. Basic `SELECT`

```sql
SELECT * FROM users;
```

> Returns **all columns** and **all rows** from the `users` table.

```sql
SELECT name, email FROM users;
```

> Returns only `name` and `email` columns.

---

## 🔸 2. Filtering with `WHERE`

```sql
SELECT * FROM users WHERE id = 5;
```

### 🔸 Comparison Operators

| Operator     | Meaning            |
| ------------ | ------------------ |
| `=`          | Equal              |
| `!=` or `<>` | Not equal          |
| `>` `<`      | Greater / Less     |
| `>=` `<=`    | Greater/less-equal |

```sql
SELECT * FROM products WHERE price > 100;
```

---

### 🔸 Logical Operators

| Operator | Meaning   |
| -------- | --------- |
| `AND`    | Both true |
| `OR`     | One true  |
| `NOT`    | Negate    |

```sql
SELECT * FROM users
WHERE is_active = true AND email_verified = true;
```

---

### 🔸 Special Filters

```sql
-- Null checks
SELECT * FROM users WHERE deleted_at IS NULL;

-- Pattern matching
SELECT * FROM users WHERE email LIKE '%@gmail.com';

-- Value list
SELECT * FROM users WHERE country IN ('US', 'UK', 'DE');

-- Range
SELECT * FROM products WHERE price BETWEEN 10 AND 50;
```

---

## 🔸 3. Sorting Results: `ORDER BY`

```sql
SELECT * FROM users ORDER BY created_at DESC;
```

| Option | Meaning             |
| ------ | ------------------- |
| `ASC`  | Ascending (default) |
| `DESC` | Descending          |

You can order by **multiple columns**:

```sql
SELECT * FROM users ORDER BY role ASC, created_at DESC;
```

---

## 🔸 4. Limiting Results: `LIMIT`, `OFFSET`

```sql
-- Get only 5 rows
SELECT * FROM users LIMIT 5;

-- Pagination
SELECT * FROM users ORDER BY id LIMIT 10 OFFSET 20;
```

> `LIMIT` is supported in most SQL dialects. SQL Server uses `TOP` and `OFFSET FETCH`.

---

## 🔸 5. Aliases with `AS`

```sql
SELECT name AS full_name, email AS contact_email
FROM users;
```

> Useful for renaming columns or making them readable.

---

## 🔸 6. Removing Duplicates: `DISTINCT`

```sql
SELECT DISTINCT country FROM users;
```

> Only returns **unique values**.

---

## 🔸 7. Simple Expressions

```sql
SELECT id, price * quantity AS total_cost
FROM order_items;
```

---

## 🔸 8. Case Expression (IF-ELSE logic)

```sql
SELECT name,
  CASE
    WHEN age < 18 THEN 'Minor'
    WHEN age >= 18 AND age < 65 THEN 'Adult'
    ELSE 'Senior'
  END AS age_group
FROM users;
```

---

## 🔸 9. Comments in SQL

```sql
-- This is a single line comment

/*
  This is a
  multi-line comment
*/
```

---

## ⚠️ Common Mistakes with `SELECT`

| Mistake                          | Problem                             |
| -------------------------------- | ----------------------------------- |
| `SELECT *` in production         | Performance hit, unreadable results |
| Missing `ORDER BY` in pagination | Unstable result order               |
| No filter → too many rows        | Unnecessary network/load            |
| Forgetting `AS` in expressions   | Hard-to-read results                |

---

## ✅ Best Practices

- Use only the columns you need (`SELECT id, name`).
- Always include `ORDER BY` when using `LIMIT`.
- Avoid `SELECT *` in production queries.
- Use meaningful aliases with `AS`.

---

## 🔜 Next Step

**Step 5: Advanced `SELECT` Clauses**

- Aggregation: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`
- `GROUP BY`, `HAVING`
- Use cases like reports, analytics, and rollups
