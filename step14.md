# ✅ Step 14: SQL and ORMs / API Integration

This step focuses on how SQL integrates with application code — especially via **ORMs**, **query builders**, and **parameterized queries** — and how SQL is executed from APIs securely and efficiently.

---

## 🔷 What is an ORM?

An **ORM (Object-Relational Mapper)** is a library that lets you **interact with your database using objects instead of raw SQL queries**. It abstracts the SQL layer and lets you focus on models, relationships, and business logic.

---

### 🛠 Popular ORMs

| Language   | ORMs                         |
| ---------- | ---------------------------- |
| TypeScript | TypeORM, Prisma, Sequelize   |
| Python     | SQLAlchemy, Django ORM       |
| Java       | Hibernate, JPA               |
| Ruby       | ActiveRecord                 |
| PHP        | Eloquent (Laravel), Doctrine |

---

## 🧱 Common ORM Concepts

| Concept                               | Explanation                       |
| ------------------------------------- | --------------------------------- |
| **Entities/Models**                   | Classes that map to DB tables     |
| **Repositories**                      | Handle DB operations for a model  |
| **[Migrations](./MigrationSetup.md)** | Version-controlled schema changes |
| **Relations**                         | One-to-many, many-to-many, etc.   |
| **Query Builders**                    | Programmatic SQL construction     |

---

## ✅ Example with TypeORM (NestJS)

```ts
// user.entity.ts
@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @OneToMany(() => Post, (post) => post.author)
  posts: Post[];
}
```

```ts
// user.service.ts
@Injectable()
export class UserService {
  constructor(@InjectRepository(User) private repo: Repository<User>) {}

  async findById(id: number) {
    return this.repo.findOne({ where: { id }, relations: ["posts"] });
  }
}
```

---

## 🔒 Parameterized Queries

To prevent **SQL injection**, never interpolate raw values into SQL.

**✅ Safe:**

```sql
SELECT * FROM users WHERE email = $1
```

```ts
await db.query("SELECT * FROM users WHERE email = $1", [email]);
```

**❌ Unsafe:**

```ts
db.query(`SELECT * FROM users WHERE email = '${email}'`);
```

---

## 🧰 Query Builders (e.g. Knex.js, Prisma)

Let you build queries programmatically, often used instead of ORMs:

```ts
// Prisma
const users = await prisma.user.findMany({
  where: { isActive: true },
  include: { posts: true },
});
```

```ts
// Knex
knex("users")
  .select("*")
  .where({ is_active: true })
  .join("posts", "users.id", "posts.user_id");
```

---

## 🔗 Raw SQL in APIs

In frameworks like NestJS or Express, raw SQL can be used with `pg`, `mysql2`, `knex`, or via your ORM.

```ts
@Post()
async createUser(@Body() dto: CreateUserDto) {
  await this.db.query('INSERT INTO users (name, email) VALUES ($1, $2)', [
    dto.name,
    dto.email,
  ]);
}
```

---

## 🛡️ Best Practices

| Practice                                     | Why it matters                   |
| -------------------------------------------- | -------------------------------- |
| Use **parameterized queries**                | Prevents SQL injection           |
| Use **migrations**                           | Track schema changes             |
| Prefer **ORM for simple CRUD**               | Fast development, clean code     |
| Use **query builders or raw SQL**            | For complex or optimized queries |
| Log & analyze **[slow queries](./step9.md)** | Optimize performance             |

---

## 🧠 Real-World Use Cases

- **Admin Panel:** ORM-based CRUD using relationships
- **Reports Dashboard:** Raw SQL for optimized JOIN + GROUP BY
- **Analytics:** Use [CTEs](./step13.md) and window functions directly with query builders
- **Filtering APIs:** Prisma + DTOs with dynamic `where` clauses
