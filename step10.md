# 🧠 **STEP 10: Stored Procedures, Functions & Triggers**

SQL isn’t just for querying—it also allows you to **encapsulate logic directly inside the database**, improving performance, reusability, and consistency.

---

## 🔷 1. What Are Stored Procedures and Functions?

| Feature          | Stored Procedure                 | Function                       |
| ---------------- | -------------------------------- | ------------------------------ |
| Returns a value? | ❌ No (optional `OUT` params)    | ✅ Yes (must return something) |
| Used in queries? | ❌ No                            | ✅ Yes (can use in `SELECT`)   |
| Use case         | Perform a sequence of operations | Compute and return a value     |

---

## 🔷 2. Stored Procedure Syntax (PostgreSQL example)

```sql
CREATE PROCEDURE transfer_funds(
    sender_id INT,
    receiver_id INT,
    amount DECIMAL
)
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE accounts SET balance = balance - amount WHERE id = sender_id;
    UPDATE accounts SET balance = balance + amount WHERE id = receiver_id;
END;
$$;
```

> Call with:

```sql
CALL transfer_funds(1, 2, 100);
```

---

## 🔷 3. Function Syntax

```sql
CREATE FUNCTION get_user_email(uid INT)
RETURNS TEXT
LANGUAGE sql
AS $$
    SELECT email FROM users WHERE id = uid;
$$;
```

> Use in a query:

```sql
SELECT get_user_email(5);
```

---

## 🔷 4. Input/Output Parameters

```sql
CREATE FUNCTION add(a INT, b INT) RETURNS INT AS $$
BEGIN
    RETURN a + b;
END;
$$ LANGUAGE plpgsql;
```

You can also use:

```sql
OUT result INT
```

to return multiple values.

---

## 🔷 5. Control Flow in Functions/Procedures

You can use:

- `IF ... THEN ...`
- `CASE`
- `LOOP`, `WHILE`, `FOR`
- `RAISE NOTICE 'message'`
- `EXCEPTION WHEN ...`

```sql
IF balance < amount THEN
    RAISE EXCEPTION 'Insufficient funds';
END IF;
```

---

## 🔷 6. Triggers: What Are They?

A **trigger** is a special procedure that runs **automatically** in response to certain events on a table.

### 🔸 When?

- `BEFORE INSERT`
- `AFTER INSERT`
- `BEFORE UPDATE`
- `AFTER DELETE`

---

## 🔷 7. Example: Trigger for Audit Log

### Step 1: Create Audit Table

```sql
CREATE TABLE user_log (
    user_id INT,
    action TEXT,
    changed_at TIMESTAMP DEFAULT NOW()
);
```

### Step 2: Create Trigger Function

```sql
CREATE FUNCTION log_user_update()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO user_log(user_id, action)
    VALUES (NEW.id, 'updated');
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### Step 3: Attach Trigger

```sql
CREATE TRIGGER trg_user_update
AFTER UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION log_user_update();
```

> 🔁 This runs **automatically** on every update!

---

## 🔷 8. Practical Real-World Uses

| Use Case                      | Use                             |
| ----------------------------- | ------------------------------- |
| Logging changes               | Trigger                         |
| Enforcing business rules      | Procedure/Trigger               |
| Reusable calculations         | Function                        |
| Scheduled background jobs     | Procedure (via cron or pgAgent) |
| Transactional logic (banking) | Procedure                       |

---

## 🔷 9. Best Practices

✅ **Keep logic simple** – not everything belongs in the DB
✅ **Wrap in [transactions](./step8.md)** where needed
✅ **Use triggers cautiously** – can be hard to debug
✅ **Avoid side effects** in functions
✅ **Version** your procedures/functions via [migration tools](./MigrationSetup.md)

---

## 🔥 Common Mistakes

| Mistake                            | Why It’s Bad                               |
| ---------------------------------- | ------------------------------------------ |
| Using triggers for too many things | Hidden behavior, hard to trace             |
| Returning sets from stored procs   | Not well supported across all DBs          |
| Calling heavy logic from `SELECT`  | Can slow down queries                      |
| No exception handling              | Causes silent failures or confusing errors |

---

## 🧰 Tools to Help

- **pgAdmin** / **DBeaver**: Browse procedures/functions
- **pg_stat_statements**: See if they are slowing down performance
- **TypeORM / Prisma**: Can call raw functions (not stored procs though)

---

## ✅ Summary

- Stored Procedures = reusable blocks of logic
- Functions = reusable value-returning logic
- Triggers = automatic hooks on INSERT/UPDATE/DELETE
- Keep logic clean, testable, and consistent

---

✅ **Ready for [Step 11: Views & Materialized Views](./step11.md)**
