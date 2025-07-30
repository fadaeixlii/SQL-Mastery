## 🔧 NestJS + TypeORM Migrations Setup (PostgreSQL & MySQL)

### ✅ Step 1: Config DataSource

Create `src/config/typeorm.ts` using a `DataSource` for CLI and app integration:

```ts
import { DataSource, DataSourceOptions } from "typeorm";
import { config } from "dotenv";
config();

export const dataSourceOptions: DataSourceOptions = {
  type: "postgres",
  host: process.env.DB_HOST,
  port: +process.env.DB_PORT,
  username: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_NAME,
  entities: ["dist/**/*.entity{.ts,.js}"],
  migrations: ["dist/migrations/*{.ts,.js}"],
  synchronize: false,
  migrationsRun: false,
};
export const AppDataSource = new DataSource(dataSourceOptions);
```

📌 Source from expert tutorial setups ([Medium][3], [xtendo.org][4], [DEV Community][5])

### ✅ Step 2: Load Into NestJS

In `AppModule`:

```ts
import { TypeOrmModule } from "@nestjs/typeorm";
import { AppDataSource, dataSourceOptions } from "./config/typeorm";

@Module({
  imports: [
    TypeOrmModule.forRoot(dataSourceOptions),
    // other modules...
  ],
})
export class AppModule {}
```

### ✅ Step 3: NPM Scripts

Add to your `package.json`:

```json
"scripts": {
  "typeorm": "ts-node node_modules/typeorm/cli",
  "migration:generate": "npm run typeorm -- -d src/config/typeorm.ts migration:generate src/migrations/$npm_config_name",
  "migration:create": "npm run typeorm -- migration:create src/migrations/$npm_config_name",
  "migration:run": "npm run typeorm migration:run -- -d src/config/typeorm.ts",
  "migration:revert": "npm run typeorm migration:revert -- -d src/config/typeorm.ts"
}
```

This convention matches guides from NestJS & community sources ([DEV Community][5], [constantsolutions.dk][6])

### ✅ Step 4: Generate & Run Migrations

```bash
npm run migration:generate --name=AddProductTable
npm run migration:run
npm run migration:revert
```

Migration class example automatically created:

```ts
export class AddProductTable167XXXXXX implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`CREATE TABLE "product" (...);`);
  }
  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`DROP TABLE "product"`);
  }
}
```

Examples based on NestJS TypeORM guides ([thisdot.co][7])

---

## 📋 Summary Table

| Tool / Feature                | Purpose                                 |
| ----------------------------- | --------------------------------------- |
| `pg_dump` / `pg_restore`      | Backup & restore Postgres databases     |
| `mysqldump` / `mysql`         | Backup & restore MySQL databases        |
| `EXPLAIN` / `EXPLAIN ANALYZE` | Analyze query plans and performance     |
| Index commands                | To speed up queries and optimize access |
| TypeORM + NestJS DataSource   | Standardized migration infrastructure   |
| CLI scripts                   | Generate, run, revert migrations easily |

[3]: https://jeewantha-abayakoon.medium.com/mysqldump-cheat-sheet-5cba8ca47927?utm_source=chatgpt.com "mysqldump Cheat Sheet - Jeewantha Abayakoon (jee1tha)"
[4]: https://xtendo.org/cheatsheet?utm_source=chatgpt.com "Cheat Sheet - xtendo.org"
[5]: https://dev.to/amirfakour/using-typeorm-migration-in-nestjs-with-postgres-database-3c75?utm_source=chatgpt.com "Using TypeORM Migration in NestJS with Postgres Database"
[6]: https://constantsolutions.dk/2024/08/05/nestjs-project-with-typeorm-cli-and-automatic-migrations/?utm_source=chatgpt.com "NestJS with TypeORM, automatic migrations and reused CLI ..."
[7]: https://www.thisdot.co/blog/setting-up-typeorm-migrations-in-an-nx-nestjs-project?utm_source=chatgpt.com "Setting Up TypeORM Migrations in an Nx/NestJS Project"
