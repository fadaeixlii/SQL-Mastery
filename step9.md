# 🧠 **STEP 9: Indexes, Performance & Optimization**

Indexes are one of the **most powerful tools** to make your queries faster—but they can also **hurt performance** if used poorly. In this step, we’ll cover:

---

## 🔷 1. What is an Index?

An **index** is a **data structure** (usually a B-tree) that helps the database find rows **faster**, similar to how a book index helps find pages.

### Analogy:

> Without index: Read every page
> With index: Jump directly to what you need

---

## 🔷 2. Types of Indexes

### ✅ B-Tree (default)

- Balanced tree for equality and range searches
- Great for: `=`, `<`, `>`, `BETWEEN`, `ORDER BY`

```sql
CREATE INDEX idx_users_email ON users(email);
```

### ✅ Hash

- Fast for equality (`=`) but **not** for range queries
- Often used internally

### ✅ Unique Index

- Enforces uniqueness

```sql
CREATE UNIQUE INDEX idx_unique_email ON users(email);
```

### ✅ Composite Index

- Multiple columns

```sql
CREATE INDEX idx_name_age ON users(last_name, age);
```

> Good for queries filtering on both: `WHERE last_name = 'Smith' AND age = 30`

> Only **leftmost columns** are used: e.g. `WHERE age = 30` won't use the above index

### ✅ Partial Index (Postgres)

- Indexes **only rows** matching a condition

```sql
CREATE INDEX idx_active_users ON users(email) WHERE is_active = true;
```

### ✅ Expression Index

- Index on computed values

```sql
CREATE INDEX idx_lower_email ON users(LOWER(email));
```

### ✅ Full-Text Index

- For searching human-readable content

```sql
-- MySQL
CREATE FULLTEXT INDEX ft_idx ON articles(content);
```

### ✅ [Full-Text Index](./ExtensionsCheatSheet.md)

- For searching human-readable content

```sql
-- MySQL
CREATE FULLTEXT INDEX ft_idx ON articles(content);
```

---

## 🔷 3. When Indexes Help

- WHERE filters:

  ```sql
  SELECT * FROM users WHERE email = 'x@example.com';
  ```

- JOINs on indexed keys
- ORDER BY + LIMIT
- Aggregates with GROUP BY
- Foreign keys (automatically indexed)

---

## 🔷 4. When Indexes Hurt

- On small tables (full scan is fast enough)
- Tables with **frequent writes** (INSERT/UPDATE/DELETE)
- Large indexes on rarely-used columns
- Over-indexing → bloats memory & slows inserts

---

## 🔷 5. Analyzing Performance

### 🔍 [EXPLAIN](./CheatSheetForBackUps.md) / EXPLAIN ANALYZE

```sql
EXPLAIN SELECT * FROM users WHERE email = 'x@example.com';
```

- Shows **query plan**
- Look for: **Index Scan**, **Seq Scan**, **Filter**, **Cost**

### 🔎 pg_stat_statements (PostgreSQL)

Tracks slow queries.

```sql
SELECT * FROM pg_stat_statements ORDER BY total_time DESC LIMIT 5;
```

---

## 🔷 6. Query Optimization Techniques

✅ Use only required columns:

```sql
SELECT id, name FROM users; -- not SELECT *
```

✅ Filter early, project late:

```sql
SELECT id FROM users WHERE age > 30;
```

✅ Watch OR + functions:

```sql
-- Bad (no index used)
WHERE LOWER(email) = 'x@example.com'

-- Better
WHERE email = 'x@example.com' OR email = 'X@example.com'
```

✅ Use LIMIT/OFFSET or keyset pagination

✅ [Denormalize](./Normalization.md) cautiously for performance

---

## 🔷 7. Maintenance

### 🧹 Vacuum (PostgreSQL)

Reclaims dead tuples.

```sql
VACUUM ANALYZE;
```

### 📊 Analyze

Rebuilds query planner stats.

```sql
ANALYZE users;
```

### 🧼 Reindex

```sql
REINDEX INDEX idx_users_email;
```

---

## 🔷 8. Real-World Examples

### Example 1: Speed up login

```sql
-- BEFORE: slow
SELECT * FROM users WHERE email = 'x@example.com' AND password = 'hashed';

-- Add index
CREATE INDEX idx_login ON users(email);

-- AFTER: fast, uses index
```

---

### Example 2: Paginated UI

```sql
-- Good: can use index
SELECT * FROM users ORDER BY created_at DESC LIMIT 10 OFFSET 20;

-- Better: keyset pagination
SELECT * FROM users WHERE created_at < '2024-07-01' ORDER BY created_at DESC LIMIT 10;
```

---

### 🔥 Common Mistakes

| Mistake                            | Why It’s Bad                            |
| ---------------------------------- | --------------------------------------- |
| Indexing every column              | Slows writes, wastes space              |
| Using functions on indexed columns | Breaks index usage                      |
| Relying on OFFSET 100000           | Slow even with index                    |
| Ignoring query plans               | Blind guessing leads to bad performance |

---

## ✅ Best Practices Summary

- Add indexes only where needed
- Use composite indexes wisely
- Avoid functions in WHERE unless indexed
- Watch query plans
- Clean up unused or bloated indexes

---

## 🚀 Next Step: [Step 10 — Stored Procedures, Functions & Triggers](./step10.md)

This covers:

- Writing reusable SQL logic
- Automating actions on insert/update
- Encapsulating logic inside the DB
