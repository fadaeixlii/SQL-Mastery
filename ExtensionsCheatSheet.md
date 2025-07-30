# 📄 SQL Extensions Cheat Sheet: JSON, Full-Text Search (FTS), and PostGIS

---

## 📦 JSON / JSONB (PostgreSQL)

### ✅ Operators

| Operator | Description                      | Example                          |                                 |                    |     |                   |
| -------- | -------------------------------- | -------------------------------- | ------------------------------- | ------------------ | --- | ----------------- |
| `->`     | Get JSON object field (as JSON)  | `data->'user'`                   |                                 |                    |     |                   |
| `->>`    | Get JSON object field (as text)  | `data->>'name'`                  |                                 |                    |     |                   |
| `#>`     | Get nested JSON object (as JSON) | `data#>'{user, address}'`        |                                 |                    |     |                   |
| `#>>`    | Get nested JSON field (as text)  | `data#>>'{user, address, city}'` |                                 |                    |     |                   |
| `?`      | Does key exist                   | `data ? 'active'`                |                                 |                    |     |                   |
| \`?      | \`                               | Any key in array exists          | \`data ?                        | array\['a', 'b']\` |     |                   |
| `?&`     | All keys in array exist          | `data ?& array['a', 'b']`        |                                 |                    |     |                   |
| `@>`     | Contains                         | `data @> '{"role": "admin"}'`    |                                 |                    |     |                   |
| `<@`     | Is contained by                  | `data <@ '{"role": "admin"}'`    |                                 |                    |     |                   |
| \`       |                                  | \`                               | Concatenate (merge two objects) | \`data             |     | '{"new": true}'\` |
| `-`      | Delete key                       | `data - 'foo'`                   |                                 |                    |     |                   |
| `#-`     | Delete nested key                | `data #- '{a,b}'`                |                                 |                    |     |                   |

---

### 🔧 JSON Functions

| Function                 | Description                     |
| ------------------------ | ------------------------------- |
| `jsonb_build_object()`   | Create JSON object              |
| `jsonb_agg()`            | Aggregate rows into JSON array  |
| `jsonb_array_elements()` | Unnest a JSON array             |
| `jsonb_each()`           | Expand object to key-value rows |
| `jsonb_set()`            | Set/replace value in path       |
| `jsonb_pretty()`         | Pretty-print JSON               |

---

## 🔎 Full-Text Search (FTS)

### ✅ Key Concepts

- `tsvector`: Full-text searchable document
- `tsquery`: Query against `tsvector`

---

### 🔧 Core Functions

| Function                     | Description                     |
| ---------------------------- | ------------------------------- |
| `to_tsvector(lang, text)`    | Convert to searchable form      |
| `to_tsquery('word & word')`  | Build a query                   |
| `plainto_tsquery()`          | Parse natural language to query |
| `ts_headline()`              | Highlight matches               |
| `ts_rank()` / `ts_rank_cd()` | Rank results by relevance       |

---

### ✅ Query Example

```sql
-- Search blog posts
SELECT id, title
FROM posts
WHERE to_tsvector('english', title || ' ' || content)
      @@ to_tsquery('nestjs & validation');
```

### 🔍 Indexing for FTS

```sql
CREATE INDEX idx_posts_fts
ON posts USING GIN(to_tsvector('english', title || ' ' || content));
```

---

## 🗺️ PostGIS (Geospatial)

### ✅ Spatial Types

| Type        | Description                  |
| ----------- | ---------------------------- |
| `GEOMETRY`  | 2D space, like points, lines |
| `GEOGRAPHY` | Spherical (Earth-based)      |
| `POINT`     | (x, y) or (lon, lat)         |

---

### 🧮 Common Functions

| Function                   | Description                     |
| -------------------------- | ------------------------------- |
| `ST_MakePoint(lon, lat)`   | Create a point                  |
| `ST_Distance()`            | Distance between two geometries |
| `ST_DWithin(a, b, radius)` | True if distance ≤ radius       |
| `ST_Intersects(a, b)`      | True if geometries overlap      |
| `ST_Contains(a, b)`        | True if a fully contains b      |
| `ST_AsText()`              | Convert to WKT string           |
| `ST_GeomFromText()`        | Parse WKT into geometry         |
| `ST_Transform()`           | Convert between projections     |

---

### 🧭 Example

```sql
-- Find stores within 10km
SELECT name
FROM stores
WHERE ST_DWithin(
  location,
  ST_MakePoint(-73.9857, 40.7484)::geography,
  10000
);
```

### 🧱 Indexing

```sql
CREATE INDEX idx_store_location ON stores USING GIST(location);
```

---

## 🛡️ Tips & Gotchas

- Prefer `JSONB` + GIN indexes for performance.
- Use `plainto_tsquery()` for safer user input in FTS.
- With PostGIS, always cast to `geography` for Earth distance.
