## 🧱 STEP 1: SQL & RDBMS FUNDAMENTALS

---

### 🔹 What is SQL?

**SQL (Structured Query Language)** is a declarative programming language used for:

- **Creating**, **reading**, **updating**, and **deleting** data in a **relational database**.
- Managing **database schemas**, **access controls**, and **transactions**.

---

### 🔹 What is an RDBMS?

**RDBMS (Relational Database Management System)** is a system that:

- Stores data in **tables** (rows and columns).
- Enforces **relationships** between data using **primary/foreign keys**.
- Provides tools for **querying**, **securing**, **backing up**, and **maintaining** data.

Examples:

- PostgreSQL
- MySQL
- SQL Server
- SQLite
- Oracle

---

### 🔹 Core Concepts in Relational Databases

#### 1. **Tables**

- Like a spreadsheet: rows (records) and columns (fields).
- Each row is a **record**, each column is an **attribute**.

#### 2. **Schema**

- A collection of tables, relationships, views, stored procedures, etc.
- Can be thought of as a **namespace** for your tables.

#### 3. **Primary Key**

- A column or combination of columns that uniquely identifies each row.
- Must be **unique** and **not null**.

#### 4. **Foreign Key**

- A reference to a primary key in another table.
- Creates a **relationship** between tables.

#### 5. **Constraints**

- Rules to enforce data integrity.

  - `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, `FOREIGN KEY`, etc.

#### 6. **Relationships**

- One-to-One
- One-to-Many
- Many-to-Many

Examples:

- One customer can have many orders → One-to-Many.
- One user has one profile → One-to-One.
- A student can enroll in many courses, and a course can have many students → Many-to-Many.

---

### 🔹 What is a Relational Model?

- Proposed by Edgar F. Codd in 1970.
- Organizes data into **relations (tables)**.
- Operations like `SELECT`, `INSERT`, `UPDATE`, and `DELETE` work on tables.

---

### 🔹 Properties of a Good Relational Design

#### 🔸 1. **Normalization**

Process of reducing redundancy:

- **1NF (First Normal Form)** – atomic values.
- **2NF** – no partial dependencies.
- **3NF** – no transitive dependencies.
- Often goes up to **BCNF**, **4NF**, etc.

#### 🔸 2. **ACID Properties (Transactions)**

Ensures **data integrity**:

- **Atomicity** – All or nothing
- **Consistency** – Data must be valid
- **Isolation** – Transactions don’t interfere
- **Durability** – Once committed, stays

---

### 🔹 Basic Lifecycle of SQL in an App

1. **User performs an action** (e.g., submits a form)
2. Backend **generates an SQL query**
3. Query is **executed against RDBMS**
4. **RDBMS returns** result
5. Backend **returns processed result** to frontend

---

### 🔹 Real-World Analogy

| SQL/DB Concept | Real-World Example                             |
| -------------- | ---------------------------------------------- |
| Table          | Excel spreadsheet                              |
| Row            | One record in a spreadsheet                    |
| Column         | A field like "name", "age", etc.               |
| Primary Key    | Unique ID like a National ID                   |
| Foreign Key    | Reference to another entity (userId in orders) |
| Schema         | Folder with related files                      |

---

### 🧠 Common Beginner Pitfalls

| Mistake                                 | Fix                                         |
| --------------------------------------- | ------------------------------------------- |
| Using natural keys instead of surrogate | Prefer `id` as an integer primary key       |
| Storing multiple values in one column   | Use separate rows or a join table           |
| Ignoring constraints                    | Use constraints to enforce data rules       |
| No indexes                              | Add indexes on frequently searched columns  |
| Over-normalizing                        | Normalize only up to 3NF for most use cases |
