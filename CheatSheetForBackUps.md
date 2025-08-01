## 🛡️ Cheat Sheet: [`pg_dump`, `mysqldump`, `pg_restore`, and `EXPLAIN`](./step15.md)

### ✅ PostgreSQL Backup & Restore (via `pg_dump` / `pg_restore`)

Based on expert guides ([JavaScript in Plain English][1], [Scribd][2])

```bash
# Backup a DB (custom format, compressed)
pg_dump -U <user> -F c -d <dbname> -f backup_file.dump

# Backup all databases in SQL script
pg_dumpall -U <user> > all_databases.sql

# Restore from custom archive
pg_restore -U <user> -d <newdb> backup_file.dump

# Restore SQL script
psql -U <user> -d <dbname> -f all_databases.sql
```

### ✅ MySQL Dump & Restore (`mysqldump`)

Adapted from cheat sheets ([Medium][3])

```bash
# Dump one database
mysqldump -u[user] -p[password] [dbname] > db.sql

# Dump multiple
mysqldump -u root -p --databases db1 db2 > multi.sql

# Dump schema only
mysqldump --no-data -u root -p [dbname] > schema.sql

# Restore
mysql -u [user] -p [dbname] < db.sql
```

### ✅ Query Analysis: [`EXPLAIN / EXPLAIN ANALYZE`](./step9.md)

To inspect how your SQL query is executed:

```sql
EXPLAIN SELECT * FROM orders WHERE total > 100;
EXPLAIN ANALYZE SELECT * FROM users WHERE email = '...';
```

Look for: **Index Scan** vs. **Seq Scan**, cost estimates, actual row counts, latency.

---

## ⚙️ Cheat Sheet: [Index Management Tips](./step9.md)

- `CREATE INDEX idx_name ON table(column);`
- `CREATE UNIQUE INDEX idx_unique ON table(column);`
- Composite indexes: `CREATE INDEX idx_combo ON table(col1, col2);`
- Partial indexes: `CREATE INDEX idx_active ON users(email) WHERE is_active = true;`
- Expression index: `CREATE INDEX idx_lower_email ON users(LOWER(email));`
- To view index usage: use database’s statistics tools (`pg_stat_user_indexes` for Postgres, etc.)

[1]: https://javascript.plainenglish.io/nestjs-typeorm-migrations-in-2025-50214275ec8d?utm_source=chatgpt.com "NestJS & TypeORM Migrations in 2025 - JavaScript in Plain English"
[2]: https://www.scribd.com/document/328414094/PGSQL-CheatSheet-mysql2psql?utm_source=chatgpt.com "PGSQL CheatSheet Mysql2psql | PDF | Postgre Sql | Database Index"
[3]: https://jeewantha-abayakoon.medium.com/mysqldump-cheat-sheet-5cba8ca47927?utm_source=chatgpt.com "mysqldump Cheat Sheet - Jeewantha Abayakoon (jee1tha)"
