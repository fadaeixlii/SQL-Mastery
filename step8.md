# 🧠 **STEP 8: Transactions & Concurrency Control**

Handling **data integrity**, **atomicity**, and **safe multi-user access** is critical in any real-world application. SQL gives us transactions, locks, and isolation levels to manage this.

---

## 📌 What is a Transaction?

A **transaction** is a sequence of SQL operations that are **executed as a single unit** of work. It must follow the **ACID** properties:

- **A**tomicity: All or nothing.
- **C**onsistency: Brings DB from one valid state to another.
- **I**solation: Concurrent transactions don’t interfere.
- **D**urability: Changes persist even after crash.

---

## 🔹 1. Basic Transaction Syntax

```sql
BEGIN; -- or START TRANSACTION;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

> Transfers 100 from account 1 to account 2.

If something fails in between:

```sql
ROLLBACK;
```

> Undoes all statements since `BEGIN`.

---

## 🔹 2. Transaction Commands

| Command       | Description                    |
| ------------- | ------------------------------ |
| `BEGIN`       | Starts a transaction           |
| `COMMIT`      | Saves all changes              |
| `ROLLBACK`    | Reverts all changes            |
| `SAVEPOINT`   | Set a rollback checkpoint      |
| `ROLLBACK TO` | Revert to a specific savepoint |

---

### 🧾 Using Savepoints

```sql
BEGIN;
  UPDATE products SET stock = stock - 1 WHERE id = 1;
  SAVEPOINT after_stock_update;

  UPDATE payments SET status = 'confirmed' WHERE id = 5;
  -- Something goes wrong...
  ROLLBACK TO after_stock_update;
COMMIT;
```

> Only undoes the payment update.

---

## 🔹 3. Isolation Levels

Isolation determines how one transaction **sees the changes** made by others.

| Level                | Description                                            | Problems Prevented           |
| -------------------- | ------------------------------------------------------ | ---------------------------- |
| **READ UNCOMMITTED** | Can read uncommitted data (dirty reads)                | None                         |
| **READ COMMITTED**   | Only reads committed data                              | Dirty reads                  |
| **REPEATABLE READ**  | Rows read cannot change (phantoms possible)            | Dirty + non-repeatable reads |
| **SERIALIZABLE**     | Full isolation (acts like transactions run one-by-one) | All of the above             |

### ⚠️ Common Anomalies

- **Dirty read**: See uncommitted data
- **Non-repeatable read**: Same query returns different results
- **Phantom read**: New rows appear on re-query

### PostgreSQL Example:

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN;
  -- do work
COMMIT;
```

---

## 🔐 4. Locking

### Implicit Locks

- Acquired automatically during `SELECT FOR UPDATE`, `INSERT`, etc.

```sql
BEGIN;
SELECT * FROM orders WHERE id = 1 FOR UPDATE;
-- row is now locked for updates from other transactions
```

### Explicit Locks

```sql
LOCK TABLE orders IN EXCLUSIVE MODE;
```

---

## ⚠️ 5. Common Pitfalls

- ❌ Forgetting to `COMMIT` or `ROLLBACK` leaves open transactions
- ❌ Long-running transactions block others → deadlocks
- ❌ Assuming `SELECT` is always safe without proper isolation
- ❌ Using SERIALIZABLE without need — hurts performance

---

## 🛠 Real-World Example: Safe Funds Transfer

```sql
BEGIN;

-- Withdraw
UPDATE accounts
SET balance = balance - 100
WHERE id = 1 AND balance >= 100;

-- Check if row was affected (sufficient funds)
-- if not → rollback and return error

-- Deposit
UPDATE accounts
SET balance = balance + 100
WHERE id = 2;

COMMIT;
```

> Ensures atomic and safe transfer.

---

## ✅ Best Practices

- Always **wrap** critical multi-step operations in transactions
- Use **savepoints** for complex flows
- **Use lowest isolation** level that’s safe for your case
- Use **SELECT ... FOR UPDATE** when reading and then updating
- Prefer short transactions to reduce lock contention

---

## 🧭 What’s Next?

Shall we move to:

### **Step 9: Indexes, Performance & Optimization**

Covering:

- B-tree vs. Hash Indexes
- Composite and partial indexes
- `EXPLAIN`, `ANALYZE`, query tuning
- Indexing do’s and don’ts
