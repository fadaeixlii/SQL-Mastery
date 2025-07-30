## 🔸 What is UPSERT?

**UPSERT** = **Insert** + **Update**

It’s used when:

- You want to **insert** a row if it doesn’t exist.
- But if it **does exist** (based on a constraint like a primary key or unique field), then just **update it instead**.

This avoids the need to check first with `SELECT`, which simplifies code and improves performance in concurrent environments.

---

## 🔁 Basic Syntax

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...)
ON CONFLICT (conflict_target)
DO UPDATE
SET column1 = EXCLUDED.column1, ...
```

---

## 📌 Example

```sql
INSERT INTO users (id, email, name)
VALUES (1, 'admin@example.com', 'Admin')
ON CONFLICT (id)
DO UPDATE SET name = EXCLUDED.name;
```

### 🧠 What’s happening here:

- PostgreSQL tries to **insert** a user with:

  - `id = 1`
  - `email = 'admin@example.com'`
  - `name = 'Admin'`

- If a user with `id = 1` **already exists**, the `INSERT` causes a **conflict** (probably because `id` is a `PRIMARY KEY`).

- Then, **instead of throwing an error**, PostgreSQL **updates** the `name` field.

---

## 🔍 What is `EXCLUDED`?

The keyword `EXCLUDED` refers to the **values you tried to insert** but that caused a conflict.

So in the example:

```sql
SET name = EXCLUDED.name;
```

It means: “Set the new value of `name` to the one I just tried to insert.”

---

## 🧱 `ON CONFLICT` Target Options

You can specify:

- A **column name** (like `ON CONFLICT (email)`)
- A **unique constraint name** (`ON CONFLICT ON CONSTRAINT constraint_name`)
- Or use `DO NOTHING` if you want to skip updating.

### ➕ With `DO NOTHING`:

```sql
INSERT INTO users (id, email, name)
VALUES (1, 'admin@example.com', 'Admin')
ON CONFLICT (id) DO NOTHING;
```

This will skip the insert if a conflict happens (i.e., it does nothing).

---

## ✅ Best Practice Use Cases

- Syncing external data sources.
- Preventing duplicate inserts.
- Bulk upserts (e.g., logs, analytics, user profiles).
- Ensuring atomic insert/update operations (no race conditions).

---

## 🧠 Final Tip

Always ensure the `ON CONFLICT (...)` matches a unique constraint or primary key; otherwise, PostgreSQL won’t know what counts as a “conflict.”
