# 🚀 STEP 13: SQL Extensions & Advanced Features

---

## 🧩 1. **JSON / JSONB Support** (PostgreSQL)

Modern SQL databases like **PostgreSQL** support structured, nested JSON data in your tables — ideal for flexible, semi-structured content.

### 🔹 Store JSON Data

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT,
  attributes JSONB
);
```

### 🔹 Insert Example

```sql
INSERT INTO products (name, attributes)
VALUES (
  'Laptop',
  '{"brand": "Dell", "ram": "16GB", "features": ["wifi", "bluetooth"]}'
);
```

---

### 🔹 Query JSON Properties

```sql
-- Get all products with 16GB RAM
SELECT * FROM products
WHERE attributes->>'ram' = '16GB';
```

```sql
-- Get all products with wifi feature
SELECT * FROM products
WHERE attributes->'features' ? 'wifi';
```

> Use `->` for JSON object, `->>` for text, `#>` for nested paths.

---

## 🧠 2. **Full-Text Search** (PostgreSQL)

Built-in support for **text search**, tokenization, ranking, and stemming.

```sql
-- Add searchable column
ALTER TABLE articles ADD COLUMN document tsvector;

-- Populate with search text
UPDATE articles
SET document = to_tsvector('english', title || ' ' || content);

-- Search query
SELECT * FROM articles
WHERE document @@ to_tsquery('nestjs & validation');
```

### 🔸 Add [index for performance](./step9.md):

```sql
CREATE INDEX idx_articles_fts ON articles USING GIN (document);
```

---

## 🌍 3. **Geospatial / PostGIS** (PostgreSQL)

PostGIS enables spatial queries like distances, shapes, containment.

```sql
CREATE TABLE stores (
  id SERIAL PRIMARY KEY,
  name TEXT,
  location GEOGRAPHY(Point, 4326)
);
```

```sql
-- Find stores within 10 km of a point
SELECT * FROM stores
WHERE ST_DWithin(
  location,
  ST_MakePoint(51.5074, -0.1278)::geography,
  10000
);
```

---

## 🧱 4. **Arrays**

SQL (especially PostgreSQL) supports native arrays.

```sql
CREATE TABLE tags (
  id SERIAL PRIMARY KEY,
  name TEXT,
  aliases TEXT[]
);

INSERT INTO tags (name, aliases)
VALUES ('typescript', ARRAY['ts', 'type-script']);
```

```sql
-- Query for match
SELECT * FROM tags
WHERE 'ts' = ANY(aliases);
```

---

## 🔁 5. [**Materialized Views**](./step11.md)

Materialized views **store** the results of a query (like a cached view) and can be refreshed.

```sql
CREATE MATERIALIZED VIEW top_customers AS
SELECT user_id, SUM(total) AS spent
FROM orders
GROUP BY user_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW top_customers;
```

---

## 🧪 6. **CTEs (Common Table Expressions)**

Allow temporary result sets with recursive or organized queries.

```sql
WITH recent_orders AS (
  SELECT * FROM orders
  WHERE created_at > NOW() - INTERVAL '7 days'
)
SELECT user_id, COUNT(*) FROM recent_orders
GROUP BY user_id;
```

---

## 🔒 7. **Generated / Computed Columns**

Columns that are calculated automatically.

```sql
CREATE TABLE employees (
  first_name TEXT,
  last_name TEXT,
  full_name TEXT GENERATED ALWAYS AS (first_name || ' ' || last_name) STORED
);
```

---

## ⚡️ 8. [**Extensions (PostgreSQL)**](./ExtensionsCheatSheet.md)

Enable features via extensions:

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";  -- for UUID generation
CREATE EXTENSION IF NOT EXISTS postgis;      -- for geospatial queries
CREATE EXTENSION IF NOT EXISTS pgcrypto;     -- for encryption
```

---

## ❗️Common Pitfalls

| Mistake                                                   | Why It’s a Problem                     |
| --------------------------------------------------------- | -------------------------------------- |
| Using plain `JSON` instead of `JSONB`                     | Slower queries and lack of indexing    |
| Forgetting `REFRESH` on [materialized views](./step11.md) | Data goes stale                        |
| Overusing full-text search                                | Can bloat indexes and hurt performance |
| Not [indexing](./step9.md) JSON/Array columns             | Queries become slow                    |
| Ignoring nullability in computed columns                  | Causes unexpected behavior             |

---

## ✅ Best Practices

- Use **`JSONB`** over `JSON` for performance and indexing.
- Add **[GIN indexes](./step9.md)** for arrays and full-text search.
- Use **CTEs** for large, complex queries and readability.
- Store expensive analytics in [**materialized views**](./step11.md) and **refresh periodically**.
- Limit geospatial usage to where truly needed — it adds complexity.
