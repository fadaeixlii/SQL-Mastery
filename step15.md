# ✅ Step 15: **Database Administration Basics**

This step focuses on **how to maintain, protect, and operate a production-grade database** — covering backup strategies, migrations, schema versioning, scaling, and more.

---

## 🧰 1. Backup & Restore Strategies

Backups are critical for disaster recovery.

### 🔄 Full Backup

- Takes a snapshot of the entire database.
- Can be done via tools like:

  - `pg_dump` / `pg_restore` (PostgreSQL)
  - `mysqldump` (MySQL)
  - SQL Server Management Studio (SSMS)

```bash
# PostgreSQL example
pg_dump -U user -d dbname -F c -f backup_file.dump
pg_restore -U user -d newdb backup_file.dump
```

### 🧩 Incremental / Differential Backup

- Only backs up data changed since the last backup (incremental) or since the last full backup (differential).
- Requires WAL (Write-Ahead Logging) or binary logs.

> 💡 Use tools like **pgBackRest**, **barman**, or **Percona XtraBackup**.

---

## 🧬 2. Migrations and Version Control

Migrations ensure your schema evolves with your codebase.

### 🚀 Tools

| Tool           | DB Support              | Features                           |
| -------------- | ----------------------- | ---------------------------------- |
| TypeORM        | PostgreSQL, MySQL, etc. | CLI-based migration generation     |
| Prisma Migrate | PostgreSQL, MySQL       | Declarative migration system       |
| Flyway         | Most RDBMS              | SQL-based, CLI + CI compatible     |
| Liquibase      | Most RDBMS              | Changelog format, rollback support |

### 📄 Sample Migration (TypeORM)

```bash
typeorm migration:create -n AddUserTable
```

```ts
export class AddUserTable implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`
      CREATE TABLE users (
        id SERIAL PRIMARY KEY,
        name TEXT NOT NULL
      )
    `);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`DROP TABLE users`);
  }
}
```

---

## 📈 3. Performance Monitoring

### 🔍 Key Metrics

- **Slow queries**
- **Locking and blocking**
- **Buffer cache hit ratio**
- **Active connections**
- **Query plan cost**

### 🛠 Tools

- `EXPLAIN`, `ANALYZE` (PostgreSQL)
- `SHOW PROFILE`, `EXPLAIN` (MySQL)
- pgAdmin, pgHero, DataDog, New Relic

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'john@example.com';
```

---

## 🧱 4. Scaling & Replication

### 🌐 Horizontal Scaling

- **Read Replicas**: Offload read operations to secondary DBs
- **Sharding**: Split large tables across databases by ID or geography

### 🔁 Replication Types

| Type                | Description                             |
| ------------------- | --------------------------------------- |
| Streaming           | Real-time binary replication (Postgres) |
| Statement-based     | SQL statements replicated (MySQL)       |
| Logical replication | Custom replication strategies           |

---

## 🔒 5. Security Best Practices

| Area            | Tips                                    |
| --------------- | --------------------------------------- |
| Authentication  | Use strong DB users, rotate credentials |
| Authorization   | Grant minimal roles (`READ ONLY`, etc.) |
| Data at rest    | Enable disk encryption                  |
| Data in transit | Use SSL/TLS connections                 |
| Audit logs      | Log queries and changes                 |
| SQL Injection   | Always use parameterized queries        |

---

## 🧪 6. Safe Schema Changes

In production environments:

- Add columns with default values **carefully** (may lock large tables)
- Use **feature flags** to deploy DB + app changes separately
- Use `NULLABLE` columns first, then backfill, then make `NOT NULL`

---

## 🗃️ 7. Maintenance Routines

| Task                              | Frequency      | Notes                         |
| --------------------------------- | -------------- | ----------------------------- |
| Vacuuming / Autovacuum (Postgres) | Regular        | Avoids bloat                  |
| Reindexing                        | Weekly/Monthly | Keeps index efficient         |
| Analyze Tables                    | Regular        | Updates query planner stats   |
| Monitoring logs                   | Daily          | For anomalies or slow queries |

---

## ✅ Summary: Your DBA Starter Pack

- 🔁 Use automated backups (with verification!)
- 🧬 Track schema changes via migrations
- 🕵️‍♂️ Monitor slow queries and indexes
- 🔒 Enforce tight permissions and connection rules
- 🚦 Plan for scaling via read replicas or sharding
- ⚙️ Keep DB changes safe with deployment hygiene
