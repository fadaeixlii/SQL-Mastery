## 🔒 STEP 12: SECURITY & PERMISSIONS

---

### 🔐 1. Users, Roles & Grants

In most RDBMS, you control access via **users** and **roles**:

#### ✅ PostgreSQL Example:

```sql
-- Create role and user
CREATE ROLE readonly;
CREATE USER app_user WITH PASSWORD 'secret';

-- Assign role to user
GRANT readonly TO app_user;

-- Grant permissions
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;
```

> You can create **roles with specific privileges** and assign them to multiple users.

---

### ✅ 2. Granular Permission Control

**Table-level:**

```sql
GRANT SELECT, INSERT ON users TO app_user;
```

**Column-level:**

```sql
GRANT SELECT (id, name) ON users TO readonly;
```

**Schema-level:**

```sql
GRANT USAGE ON SCHEMA analytics TO app_user;
```

**Function-level:**

```sql
GRANT EXECUTE ON FUNCTION update_inventory() TO manager_role;
```

---

### ✅ 3. View-based Security

Use **[views](./step11.md)** to expose only certain columns or filtered rows:

```sql
CREATE VIEW public_users AS
SELECT id, name
FROM users
WHERE is_active = true;

GRANT SELECT ON public_users TO readonly;
```

> Clients using `readonly` role can only access `public_users`, not full `users` table.

---

### ✅ 4. Row-Level Security (PostgreSQL)

A powerful feature that restricts data returned based on policies:

```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Policy allowing only user's own orders
CREATE POLICY user_orders_policy
ON orders
FOR SELECT
USING (user_id = current_setting('app.current_user_id')::int);
```

Set the current user ID in each session:

```sql
SET app.current_user_id = 42;
```

> Perfect for multi-tenant apps or per-user data isolation.

---

### ✅ 5. Encryption & Auditing

- **TLS/SSL**: Secure traffic between database and clients.

- **Column-level encryption**: Use `pgcrypto` in PostgreSQL:

  ```sql
  UPDATE users
  SET ssn = encrypt('123‑45‑6789'::text, gen_salt('bf'));
  ```

- **Audit logs**: Track changes using extensions like `pgaudit` or custom [triggers](./step10.md).

---

## ⚠️ Common Security Pitfalls

| Mistake                                | Why It’s Dangerous                       |
| -------------------------------------- | ---------------------------------------- |
| Using superuser or admin for app       | Risky if code has bugs or is compromised |
| Granting excess privileges to API user | Opens attack surface                     |
| Not setting up TLS/SSL                 | Exposes data in transit                  |
| Exposing sensitive columns via views   | May leak personal or confidential data   |
| Not rotating credentials or keys       | Risk of credential compromise            |

---

## ✅ Best Practices

1. Use **principle of least privilege**: only grant what’s needed.
2. Use **roles** rather than assigning per user.
3. Use **[views](./step11.md) and schemas** to abstract real tables.
4. Leverage **row-level security** for fine-grained filtering.
5. Enable **encrypted connections**.
6. **Rotate secrets** (`passwords`, `keys`) regularly.

---

## 🚀 Summary Table

| Feature                | Technique/Command                     | Benefit                             |
| ---------------------- | ------------------------------------- | ----------------------------------- |
| Role-based access      | `GRANT SELECT ON table TO role`       | Least-privilege control             |
| Per-column exposure    | `GRANT SELECT (id, name)`             | Limits sensitive data exposure      |
| Row-based filtering    | Row-Level Security (PostgreSQL)       | Multi-tenant or user-based security |
| View-level restriction | Views with restricted columns or rows | Safe dataset sharing                |
| Auditing & encryption  | `pgcrypto`, audit logs, SSL/TLS       | Compliance and data protection      |

---

✅ Ready for [**Step 13: SQL Extensions & Advanced Features**](./step13.md) (e.g. JSONB, full-text search, geospatial)?
