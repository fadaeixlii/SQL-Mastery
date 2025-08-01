## 🧱 STEP 2: DATA DEFINITION LANGUAGE (DDL)

---

### 🔹 What Is DDL?

**DDL (Data Definition Language)** consists of SQL commands used to define, modify, and remove the **structure** of database objects like:

- Tables
- Columns
- [Indexes](./step9.md)
- Constraints
- [Views](./step11.md)
- Schemas
- Databases

---

### 📌 Common DDL Commands

| Command    | Purpose                                                         |
| ---------- | --------------------------------------------------------------- |
| `CREATE`   | Create new table/schema/index/view                              |
| `ALTER`    | Modify existing object (add/drop column, change datatype, etc.) |
| `DROP`     | Delete database object permanently                              |
| `TRUNCATE` | Remove all data from a table (structure remains)                |
| `RENAME`   | Rename table or column                                          |

---

### 🔸 1. `CREATE TABLE`

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  age INT CHECK (age >= 0),
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### Explanation:

- `SERIAL` = auto-incrementing integer (PostgreSQL)
- `PRIMARY KEY` = unique and not null
- `UNIQUE` = no duplicate emails
- `CHECK` = constraint on valid ages
- `DEFAULT` = value assigned if none is provided

---

### 🔸 2. `ALTER TABLE`

Used to change table structure:

#### ➕ Add column:

```sql
ALTER TABLE users ADD COLUMN is_active BOOLEAN DEFAULT true;
```

#### ➖ Drop column:

```sql
ALTER TABLE users DROP COLUMN age;
```

#### ✏️ Modify data type:

```sql
ALTER TABLE users ALTER COLUMN name TYPE TEXT;
```

#### ✏️ Rename column:

```sql
ALTER TABLE users RENAME COLUMN name TO full_name;
```

---

### 🔸 3. `DROP TABLE`

Deletes the table **and all its data** permanently:

```sql
DROP TABLE users;
```

> ⚠️ Use with caution — no rollback without [backups](./CheatSheetForBackUps.md).

---

### 🔸 4. `TRUNCATE TABLE`

Deletes **all data**, but keeps the table structure:

```sql
TRUNCATE TABLE users;
```

- Much faster than `DELETE FROM`
- Cannot be rolled back in some RDBMS (depends on transaction support)

---

### 🔸 5. `RENAME TABLE`

```sql
ALTER TABLE users RENAME TO app_users;
```

---

### 🔹 Common Constraints

| Constraint    | Description                                   |
| ------------- | --------------------------------------------- |
| `PRIMARY KEY` | Uniquely identifies a record                  |
| `UNIQUE`      | Prevents duplicates in a column               |
| `NOT NULL`    | Requires a value                              |
| `CHECK`       | Custom condition that must be satisfied       |
| `DEFAULT`     | Provides default value if none is given       |
| `FOREIGN KEY` | Enforces referential integrity between tables |

---

### 🔹 Indexes (Basic Intro)

```sql
CREATE INDEX idx_users_email ON users(email);
```

- Speeds up queries involving the `email` column
- You’ll learn much more about indexes in **Step 9**

---

### 💡 Best Practices

| Tip                                             | Why it matters                         |
| ----------------------------------------------- | -------------------------------------- |
| Always name constraints                         | Helps debugging (especially in errors) |
| Use descriptive names for tables/columns        | Easier maintenance                     |
| Default to lowercase snake_case                 | More portable & conventional           |
| Add timestamps (`created_at`, `updated_at`)     | For audit and record management        |
| Use `uuid` for primary keys in distributed apps | Better for merging across systems      |

---

### ❌ Common Mistakes

| Mistake                                  | Correction                                 |
| ---------------------------------------- | ------------------------------------------ |
| Using `VARCHAR(255)` blindly             | Choose size based on actual data need      |
| Missing `NOT NULL` on required fields    | Leads to nullable fields that shouldn't be |
| Not using `DEFAULT` for common values    | Forces app to always specify them          |
| Relying only on application-level checks | Add `CHECK`, `UNIQUE`, `FOREIGN KEY` in DB |

---

### 🧠 Real-World Example

#### Create a `products` table:

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  price NUMERIC(10, 2) CHECK (price >= 0),
  stock INT DEFAULT 0,
  category_id INT,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

> 🔄 You could combine this with `categories` table and enforce clean referential data.

---

### ✅ Summary

- DDL is used to **define and modify the structure** of your database.
- `CREATE`, `ALTER`, `DROP`, and `TRUNCATE` are core to managing schemas.
- Constraints are crucial for data integrity.
- Structure well = fewer bugs down the line.

---

### 🔜 Next Step

**Step 3: Data Manipulation Language (DML)**
We’ll cover:

- `INSERT`
- `UPDATE`
- `DELETE`
- `RETURNING`, bulk operations, UPSERTS
