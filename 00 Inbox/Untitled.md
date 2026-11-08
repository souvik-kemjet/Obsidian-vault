

## 1. Where the schema is defined (4 places, in order)
```powershell
Domain/Identity/Account.cs          ① what an Account is (properties)
        │
DAL/Configurations/IdentityConfigurations.cs   ② how it maps to a table
        │
DAL/IlpDbContext.cs                 ③ which tables exist + schema "ilp"
        │   dotnet ef migrations add ...
        ▼
DAL/Migrations/2026..._X.cs         ④ the generated "create/alter table" steps

```

**① The entity**, in `Kemjet.Ilp.Domain/Identity/`: a plain C# class such as `Account`, with `Id`, `Phone`, `Name`, `Status`, and so on. Each property becomes a column.


**② The configuration**, in [IdentityConfigurations.cs](vscode-webview://10h5rc6osusda27kcu2siag15o290o2kjrlomfi2b9c06ive0rlh/Kemjet.Ilp.DAL/Configurations/IdentityConfigurations.cs): the database details for each entity:
```C#
b.ToTable("accounts", ...);                          // table name
b.Property(x => x.Phone).HasMaxLength(16).IsRequired(); // varchar(16) NOT NULL
b.HasIndex(x => new { x.Audience, x.Phone }).IsUnique(); // one account per phone per app
b.HasOne<Account>().WithMany().HasForeignKey(x => x.AccountId); // refresh_tokens → accounts

```

**③ The DbContext**, [IlpDbContext.cs](vscode-webview://10h5rc6osusda27kcu2siag15o290o2kjrlomfi2b9c06ive0rlh/Kemjet.Ilp.DAL/IlpDbContext.cs): the list of tables plus global rules.
- `DbSet<Account> Accounts`, `OtpChallenges` and `RefreshTokens` are the three tables.
- `HasDefaultSchema("ilp")` puts every table in the Postgres schema `ilp`, so the full name is `ilp.accounts`.
- `ApplySnakeCaseNames` turns the C# name `CreatedAt` into the column name `created_at`.

**④ The migrations**, in `Migrations/`: EF _generates_ these from ①–③. Nobody writes them by hand.


---

## 2. The files with long numbers
|File|What it is|
|---|---|
|`20261102123144_InitialIdentity.cs`|**The actual change.** `Up()` creates the `ilp` schema and the 3 tables with their columns, keys, indexes and check constraints. `Down()` undoes it.|
|`20261102123144_InitialIdentity.Designer.cs`|Metadata EF needs about that migration. Never edit it.|
|`IlpDbContextModelSnapshot.cs`|EF's record of **what the whole database looks like now**. It's the nearest thing to a schema document.|

The long number is a **timestamp**: `2026 11 02 12 31 44` means 2 Nov 2026, 12:31:44. It keeps migrations in the order they were created, so every database applies them in the same order.

Think of migrations as **version history for the database**, like git commits for tables:
```powershell
InitialIdentity   → create accounts, otp_challenges, refresh_tokens
(later) AddPermits    → create permits table
(later) AddPhotoToHolder → add a column
```

The database stores which ones it has run in the table `ilp.__ef_migrations`. `dotnet ef database update` runs only the ones that are missing.

---

## 3. Where the queries are
There are no handwritten SQL files. Queries are written in C# (LINQ) in [IdentityRepositories.cs](vscode-webview://10h5rc6osusda27kcu2siag15o290o2kjrlomfi2b9c06ive0rlh/Kemjet.Ilp.DAL/Repositories/IdentityRepositories.cs), and EF translates them to SQL when they run:

```C#
db.Accounts.FirstOrDefaultAsync(a => a.Audience == audience && a.Phone == phone)
```
becomes
```sql
SELECT ... FROM ilp.accounts a WHERE a.audience = @p0 AND a.phone = @p1 LIMIT 1;
```

Inserts happen through `Add(...)` followed by `SaveChangesAsync()`, which runs `INSERT INTO ilp.otp_challenges ...`. That's the `UnitOfWork`.


---

## 4. What happens when Postman calls `/auth/otp/request`

```bash
Postman
  → OtpAuthControllerBase.RequestOtp          (Api: HTTP in/out)
  → RequestOtpHandler.HandleAsync              (BAL: business rules)
       1. SELECT latest OTP for phone          → cooldown check
       2. SELECT COUNT last hour               → hourly cap
       3. SELECT account exists (checkpoint)   → eligibility
       4. create OtpChallenge (code is HASHED, never stored plain)
       5. INSERT INTO ilp.otp_challenges       → SaveChangesAsync
       6. ISmsSender.SendOtpAsync              → dev log / MSG91
  → OtpChallengeRepository / UnitOfWork        (DAL: EF Core)
  → Npgsql → PostgreSQL  ilp.otp_challenges
  ← 202 { challenge_id, ... }

```

## 5. How someone new sets it up, with no diagram needed
```bash
git clone ... ; cd ilp-platform-backend
docker compose -f deploy/compose/docker-compose.lan.yml up -d --build   # empty Postgres + API
$env:ILP_DESIGN_CONNECTION = "Host=localhost;Port=5433;Database=ilp_nl;Username=ilp_dev;Password=ilp_dev_only"
dotnet tool restore
dotnet ef database update --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api

```

The migrations in git rebuild the **exact same tables** on any machine. That's the main reason to use them.
**To see the schema:**
- Run `dotnet ef migrations script --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api`. It prints the full SQL (`CREATE TABLE ...`) without touching any database.
- For an **ER diagram**, connect DBeaver or pgAdmin to `localhost:5433`. Both draw it from the live tables

---
## 6. How the schema changes later
1. Add or change a property in the entity, plus its configuration if needed.
2. Run `dotnet ef migrations add AddSomething --project src/Kemjet.Ilp.DAL --startup-project src/Kemjet.Ilp.Api`. This creates a new timestamped file and updates the snapshot.
3. **Read the generated file.** A reviewer checks it in the PR, especially for anything that drops a column or table.
4. Commit the migration, its Designer file and the snapshot together, using commit scope `db`.
5. Everyone else runs `dotnet ef database update`.
6. **Production** never runs `update` directly. It runs reviewed SQL from `dotnet ef migrations script --idempotent`, which is why the API never migrates itself.