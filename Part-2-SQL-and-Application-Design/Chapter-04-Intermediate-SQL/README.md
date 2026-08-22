# Chapter 4: Intermediate SQL

**Part:** [Part 2 — SQL & Application Design](../README.md)
**Textbook:** *Database System Concepts*, 7th Edition — Silberschatz, Korth, Sudarshan

## Exact Subsections to Read

- **4.1** Join Expressions (Natural Join, Join using On, Outer Joins: Left, Right, Full)
- **4.2** Views (View definition, materialized views, updatable views)
- **4.4** Integrity Constraints (Constraints on domain, Primary Key, Foreign Key, Unique, Check, Not Null, referential actions like on delete cascade)
- **4.7** Authorization (SQL privileges: Select, Insert, Update, Delete; Role-Based Access Control)

> In Chapter 3 we learned *basic* SQL. In this chapter, we build on that with more powerful tools: better ways to combine tables (joins), reusable saved queries (views), stronger rules to keep your data correct (constraints), and ways to control *who* is allowed to do *what* to your data (authorization).

---

## 4.1 Join Expressions

So far, whenever we combined two tables, we did it the "long way": list both tables in `from`, and write the matching condition in `where`. SQL's **join expressions** let us do the same thing, but more clearly and safely, directly inside the `from` clause.

```mermaid
graph TD
    J[SQL Join Expressions] --> NJ["**Natural Join**<br/>auto-matches ALL same-named<br/>attributes across both tables"]
    J --> UJ["**Join ... using (A1,...,An)**<br/>matches ONLY the explicitly<br/>listed attributes"]
    J --> OJ["**Join ... on &lt;predicate&gt;**<br/>matches on ANY arbitrary<br/>condition you write"]
    J --> OUT["**Outer Joins**<br/>preserve unmatched rows<br/>by padding with null"]

    style NJ fill:#4a90d9,color:#fff
    style UJ fill:#57a773,color:#fff
    style OJ fill:#c9642a,color:#fff
    style OUT fill:#8e44ad,color:#fff
```

### 4.1.1 The Natural Join

`natural join` does the matching for you automatically: it looks at **every column that has the same name** in both tables, and matches rows where those columns are equal. Each shared column is then shown **only once** in the result (instead of twice).

```sql
select name, course_id
from student natural join takes;

-- equivalent, longhand form:
select name, course_id
from student, takes
where student.ID = takes.ID;
```

> ⚠️ **A trap to watch out for with natural join:** suppose you chain three natural joins together —
> `student natural join takes natural join course`. Because both `student` and `course` happen to have a column called `dept_name`, the join will *also* silently require `dept_name` to match between them. This quietly removes any student who is taking a course **outside their own department** — probably not what you wanted! This happens because natural join blindly matches *every* same-named column, whether you meant it to or not.

**How to fix it — `join ... using`:** this lets *you* choose exactly which shared column(s) must match, and ignores any other same-named columns:

```sql
select name, title
from (student natural join takes) join course using (course_id);
-- dept_name is NOT required to match here — only course_id is
```

### 4.1.2 Join Conditions — `on`

`join ... on <predicate>` lets you write **any condition you like** as the matching rule (similar to a `where` clause), but placed directly inside the `from` clause:

```sql
select *
from student join takes on student.ID = takes.ID;
```

This gives basically the same result as a Cartesian product with a `where` clause, with two small differences:
1. With `on`, both columns are kept in the result (e.g., you'll see both `student.ID` and `takes.ID`), instead of just one.
2. **`on` behaves differently from `where` when used with outer joins** — this is explained in detail below, and it's actually the main reason `on` exists as its own keyword.

### 4.1.3 Outer Joins — Preserving Unmatched Rows

A normal join (also called an **inner** join) only keeps rows that have a match on both sides — any row without a partner is simply dropped. An **outer join** is more forgiving: it keeps those unmatched rows too, and just fills in the missing side with `null`.

```mermaid
graph LR
    subgraph "Left Outer Join"
        L1["ALL rows from LEFT table<br/>+ matching rows from right<br/>(right-only columns = null if no match)"]
    end
    subgraph "Right Outer Join"
        R1["ALL rows from RIGHT table<br/>+ matching rows from left<br/>(left-only columns = null if no match)"]
    end
    subgraph "Full Outer Join"
        F1["ALL rows from BOTH tables<br/>unmatched rows on either side<br/>padded with null"]
    end

    style L1 fill:#4a90d9,color:#fff
    style R1 fill:#57a773,color:#fff
    style F1 fill:#c9642a,color:#fff
```

| Outer Join Type | Keeps unmatched rows from... | SQL Keyword |
|---|---|---|
| **Left Outer Join** | the **left** (first-named) relation only | `left outer join` |
| **Right Outer Join** | the **right** (second-named) relation only | `right outer join` |
| **Full Outer Join** | **both** relations | `full outer join` |
| *(Inner Join)* | *neither* — only truly matching rows | `join` / `inner join` (default) |

**Example — list every student, even the ones who haven't taken any course:**

```sql
select *
from student natural left outer join takes;
-- Student "Snow" (who has taken nothing) still appears,
-- with course_id, sec_id, semester, year, grade all = null
```

**A common trick — finding students who have taken NO course at all** (this is sometimes called an "anti-join"):

```sql
select ID
from student natural left outer join takes
where course_id is null;
```

### `on` vs. `where` with Outer Joins — the Critical Difference

```mermaid
flowchart TD
    A["student LEFT OUTER JOIN takes<br/>**ON** student.ID = takes.ID"]
    B["Non-matching student rows<br/>ARE preserved<br/>(padded with null)"]
    A --> B

    C["student LEFT OUTER JOIN takes<br/>**ON true**, then<br/>**WHERE** student.ID = takes.ID"]
    D["Non-matching student rows<br/>are DISCARDED<br/>(where filters AFTER the outer join<br/>generates the full Cartesian product)"]
    C --> D

    style B fill:#27ae60,color:#fff
    style D fill:#c0392b,color:#fff
```

> **Why this matters:** the `on` condition is checked *while the outer join itself is happening* — it decides which rows get the `null`-padding treatment. A `where` clause, on the other hand, is checked *afterward*, once the join has already produced its result. So if you accidentally put your matching condition in `where` instead of `on`, it can throw away the very unmatched rows the outer join was supposed to keep. This is a small detail, but it's a favorite trick question in exams.

### Combining Join Types × Join Conditions

Here's something useful to know: any **join type** (inner, left outer, right outer, full outer) can be freely combined with any **join condition** (natural, `using`, `on`). They are independent choices.

```mermaid
graph LR
    T["Join Types<br/>inner join · left outer join<br/>right outer join · full outer join"] -.pairs with.-> C["Join Conditions<br/>natural · using(...) · on &lt;predicate&gt;"]
```

---

## 4.2 Views

A **view** is like a "saved query" that behaves as if it were a table. You can `select` from it just like a real table, but it doesn't actually store any rows itself — every time you use it, the database runs the underlying query again to compute the result fresh.

```mermaid
flowchart LR
    U["User writes:<br/>select * from faculty"] --> DB["Database system substitutes<br/>the STORED QUERY DEFINITION<br/>of the view"]
    DB --> R["Query is evaluated against<br/>the REAL underlying table(s)<br/>every time the view is used"]

    style U fill:#4a90d9,color:#fff
    style DB fill:#c9642a,color:#fff
    style R fill:#27ae60,color:#fff
```

### 4.2.1 View Definition — `create view`

```sql
create view faculty as
    select ID, name, dept_name
    from instructor;
```

- Notice this view leaves out the `salary` column entirely. This is a simple but powerful way to add **security** — a clerk can be allowed to query `faculty`, but will never be able to see anyone's salary.
- You can also name the columns of a view explicitly. This is especially useful when your query produces a column without an obvious name, like an aggregate:

```sql
create view departments_total_salary(dept_name, total_salary) as
    select dept_name, sum(salary)
    from instructor
    group by dept_name;
```

- A view can even be built **on top of another view** — the database just keeps substituting definitions until it reaches the real tables.

> **View vs. `with` clause — what's the difference?** A `with`-defined relation only exists for the **single query** it's written in and disappears afterward. A `create view`, however, is saved permanently: it stays until someone explicitly `drop`s it, and it can be reused by many different queries and different users.

### 4.2.2 Materialized Views

By default, a view is **never stored** — every time you query it, the database recomputes it from scratch. A **materialized view** flips this around: its result is **physically saved** on disk, so reading it is much faster. The tradeoff is that the saved copy can go "stale" and needs to be refreshed (this is called **view maintenance**) whenever the underlying tables change.

```mermaid
graph TD
    V["**Ordinary (Virtual) View**"] --> V1["NOT stored"]
    V --> V2["Always up-to-date<br/>(recomputed each access)"]
    V --> V3["Slower for complex/large queries"]

    M["**Materialized View**"] --> M1["Physically STORED"]
    M --> M2["May become stale — needs<br/>'view maintenance' to refresh"]
    M --> M3["Much faster for repeated<br/>reads of large aggregations"]

    style V fill:#4a90d9,color:#fff
    style M fill:#e67e22,color:#fff
```

| Aspect | Virtual View | Materialized View |
|---|---|---|
| Storage | Not stored — recomputed | Physically stored |
| Freshness | Always current | May be stale until refreshed |
| Best for | Simple queries, security masking | Expensive aggregates over large tables |
| Refresh strategies | N/A | Immediate, lazy (on access), or periodic |
| SQL standard support | Yes (`create view`) | ❌ No standard syntax — vendor-specific extensions |

### 4.2.3 Updatable Views

You might expect to be able to `insert`, `update`, or `delete` through a view just like a normal table — but this gets tricky, because the database has to translate your change back onto the *real* underlying table(s), and that's not always possible to do without ambiguity.

```mermaid
flowchart TD
    Q["Is the view UPDATABLE?"] --> C1{"FROM clause has<br/>only ONE relation?"}
    C1 -->|No| BAD["❌ Not updatable"]
    C1 -->|Yes| C2{"SELECT clause has only<br/>plain attribute names<br/>(no expressions/aggregates/distinct)?"}
    C2 -->|No| BAD
    C2 -->|Yes| C3{"Every omitted attribute<br/>can be NULL<br/>(no not-null / no primary-key)?"}
    C3 -->|No| BAD
    C3 -->|Yes| C4{"No GROUP BY / HAVING?"}
    C4 -->|No| BAD
    C4 -->|Yes| GOOD["✅ View is updatable"]

    style GOOD fill:#27ae60,color:#fff
    style BAD fill:#c0392b,color:#fff
```

**For a view to be updatable, SQL requires all four of these conditions to hold:**
1. The `from` clause has **only one** database relation (table).
2. The `select` clause lists **only plain column names** — no expressions, aggregates, or `distinct`.
3. Any column that is **not** listed in `select` must be allowed to be `null` (that is, it isn't marked `not null` and isn't part of the primary key).
4. The query has **no `group by` or `having`** clause.

> Even a view that technically qualifies as "updatable" can behave oddly: if you insert a row through the view that doesn't satisfy the view's own `where` condition, the insert still succeeds on the real table — but the new row simply **won't show up** in the view. If you want SQL to actively **reject** such inserts/updates instead, add **`with check option`** to the view's definition.

---

## 4.4 Integrity Constraints

Integrity constraints exist to stop **accidental** mistakes from corrupting your data — even by users who are otherwise fully authorized to make changes. (This is different from *authorization*, covered in Section 4.7, which is about stopping **unauthorized** access.)

```mermaid
graph TD
    IC[SQL Integrity Constraints] --> D["**Domain / not null**<br/>restricts allowed values<br/>or forbids null"]
    IC --> U["**unique**<br/>declares a superkey<br/>(nulls still permitted)"]
    IC --> CH["**check(predicate)**<br/>arbitrary condition every<br/>tuple must satisfy"]
    IC --> PK["**primary key**<br/>NOT NULL + UNIQUE,<br/>identifies each tuple"]
    IC --> FK["**foreign key ... references**<br/>referential integrity —<br/>values must exist in another relation"]

    style D fill:#4a90d9,color:#fff
    style U fill:#57a773,color:#fff
    style CH fill:#c9642a,color:#fff
    style PK fill:#8e44ad,color:#fff
    style FK fill:#e67e22,color:#fff
```

### 4.4.1–4.4.2 `not null` and Domain Constraints

```sql
name   varchar(20) not null,
budget numeric(12,2) not null
```

`not null` is what's called a **domain constraint** — it simply says "this column is never allowed to hold `null`". Also worth remembering: any column that is part of a primary key is **automatically** treated as `not null`, since SQL never allows a null primary key.

### 4.4.3 `unique` Constraint

```sql
unique (A1, A2, ..., Am)
```

This says that the listed columns, together, must be unique for every row — no two rows are allowed to have the exact same combination of values in all of them. The key difference from a primary key is that `unique` columns **are still allowed to hold `null`** (and since `null` is never considered equal to another `null`, having several nulls doesn't break the uniqueness rule) — unless you also mark the column `not null`.

### 4.4.4 The `check` Clause

`check(P)` makes sure that **every row** satisfies the condition *P* you write. You can think of it as a way to build your own custom validation rule:

```sql
budget numeric(12,2) check (budget > 0)

check (semester in ('Fall', 'Winter', 'Spring', 'Summer'))   -- simulates an ENUM
```

> A `check` is only considered violated if it works out to **`false`**. If it comes out `unknown` (which can happen when a `null` is involved in the comparison), that does **not** count as a violation. (The SQL standard technically allows subqueries inside `check`, but in practice, no major database system currently supports that.)

### 4.4.5 Referential Integrity — `foreign key`

```sql
foreign key (dept_name) references department
```

This makes sure that every `dept_name` value in this table **actually exists** as a primary key value over in the `department` table. By default, a foreign key points at the referenced table's primary key, but you can also name the exact target column yourself: `references department(dept_name)`.

### Referential Actions — What Happens on Violation?

What should happen if someone tries to delete or update a row that other rows still depend on through a foreign key? SQL lets you decide:

```mermaid
graph TD
    V["Referenced row is<br/>DELETED or UPDATED,<br/>would violate FK"] --> Action{"Referential Action<br/>Specified?"}
    Action -->|"none (default)"| REJ["**Reject** the operation<br/>(transaction rolled back)"]
    Action -->|"on delete/update cascade"| CAS["**Cascade**: automatically<br/>delete/update the<br/>matching referencing row(s)"]
    Action -->|"on delete/update set null"| SNULL["Set the referencing<br/>foreign-key column to **null**"]
    Action -->|"on delete/update set default"| SDEF["Set the referencing<br/>foreign-key column to its<br/>**default value**"]

    style REJ fill:#c0392b,color:#fff
    style CAS fill:#e67e22,color:#fff
    style SNULL fill:#4a90d9,color:#fff
    style SDEF fill:#57a773,color:#fff
```

```sql
foreign key (dept_name) references department
    on delete cascade
    on update cascade
```

> **Cascades can chain together:** if table A's foreign key cascades into table B, and B's foreign key cascades into table C, then a single delete in A can end up rippling all the way down to C. If, at any point, a cascade runs into a rule it can't satisfy, the database gives up entirely — the **whole transaction is aborted and rolled back**, as if nothing happened.
>
> **What about nulls in a foreign key?** If the foreign-key column in a row is `null`, that row is treated as automatically satisfying the constraint — SQL doesn't even bother checking it — unless you've also marked that column `not null`.

### Naming & Deferring Constraints

```sql
salary numeric(8,2), constraint minsalary check (salary > 29000)
...
alter table instructor drop constraint minsalary;
```

Giving a constraint a name (like `minsalary` above) means you can remove it later with `alter table ... drop constraint`. You can also mark a constraint as `deferrable` / `initially deferred`, which tells SQL to wait until the **end of the whole transaction** before checking it. This is handy when a multi-step transaction is only valid once *all* its steps are done — for example, inserting two rows that each reference the other.

### Summary Table — All Constraint Types Covered

| Constraint | Scope | Enforces |
|---|---|---|
| `not null` | Single attribute | Value cannot be null |
| `unique(...)` | Attribute set | Values form a superkey (nulls allowed) |
| `check(P)` | Attribute / tuple / table | Arbitrary predicate P must hold |
| `primary key(...)` | Attribute set | Superkey + not null + minimal, exactly one per table |
| `foreign key(...) references` | Cross-table | Referential integrity |
| `create assertion` | Whole database | Arbitrary always-true predicate (not supported by mainstream DBs) |

---

## 4.7 Authorization

Authorization is about deciding **which users** are allowed to perform **which actions**. This is a *security* concern — different from integrity constraints, which protect data correctness rather than access.

```mermaid
graph TD
    A[SQL Authorization / Privileges] --> S["**select**<br/>read tuples"]
    A --> I["**insert**<br/>add new tuples<br/>(optionally restricted to<br/>specific attributes)"]
    A --> U["**update**<br/>modify tuples<br/>(optionally per-attribute)"]
    A --> D["**delete**<br/>remove tuples"]
    A --> ALL["**all privileges**<br/>shorthand for every privilege"]

    style S fill:#4a90d9,color:#fff
    style I fill:#57a773,color:#fff
    style U fill:#c9642a,color:#fff
    style D fill:#8e44ad,color:#fff
```

### 4.7.1 Granting and Revoking Privileges — Discretionary Access Control (DAC)

```sql
grant select on department to Amit, Satoshi;
grant update (budget) on department to Amit, Satoshi;   -- per-attribute update privilege

revoke select on department from Amit, Satoshi;
```

> The keyword `public` means **every current and future user**. Granting a privilege to `public` gives it to everybody.

This style — where the owner of a table decides, user by user, who gets access — is exactly what's called **Discretionary Access Control (DAC)**. The person granting access has full "discretion" to decide who else may use their objects.

### 4.7.2 Roles — Role-Based Access Control (RBAC)

Imagine you had to grant the same set of privileges to every single instructor, one by one — that would get tedious fast. Instead, you can define a **role** once, grant privileges to that role, and then simply give the role to each user who needs it.

```mermaid
flowchart TD
    DBA["Database Administrator"] -->|"create role<br/>grant privileges to role"| R1["Role: instructor"]
    DBA -->|"create role"| R2["Role: dean"]
    R1 -->|"grant instructor to dean"| R2
    R2 -->|"grant dean to Satoshi"| U1["User: Satoshi"]
    R1 -->|"grant instructor to Amit"| U2["User: Amit"]

    U1 -.inherits privileges of.-> R2
    U1 -.inherits (via dean).-> R1
    U2 -.inherits privileges of.-> R1

    style R1 fill:#4a90d9,color:#fff
    style R2 fill:#57a773,color:#fff
```

```sql
create role instructor;
grant select on takes to instructor;

create role dean;
grant instructor to dean;      -- roles can inherit from other roles!
grant dean to Satoshi;          -- Satoshi now has ALL privileges of dean + instructor
```

**In short: a user's total privileges = whatever was granted directly to them, plus everything granted to any role they hold (even indirectly, through another role).**

> **DAC vs. RBAC — a common exam comparison:**
>
> | | Discretionary Access Control (DAC) | Role-Based Access Control (RBAC) |
> |---|---|---|
> | Granularity | Per-user, via `grant`/`revoke` | Per-**role** (organizational function) |
> | Admin overhead | High — repeat grants for every new user | Low — just assign the role |
> | Scales to org changes | Poorly (re-grant everything on hire) | Well (grant/revoke a single role) |
> | SQL mechanism | `grant ... to <user>` | `create role`, `grant ... to <role>`, `grant <role> to <user>` |

### 4.7.3–4.7.4 Authorization on Views and Schema

- A user can only be given privileges on a **view** that don't go beyond what they already have on the **real tables** it's built from. (For example, they can't be granted `update` on a view if they don't already have `update` on its underlying table.)
- Before a user can create a **foreign key** pointing to someone else's table, they first need the `references` privilege on that table. This makes sense because a foreign key **restricts what the owner of the other table can later delete or update**.

### 4.7.5–4.7.6 Privilege Transfer and Cascading Revocation

```sql
grant select on department to Amit with grant option;   -- Amit may now re-grant this to others
revoke select on department from Amit, Satoshi restrict; -- fails if it would cascade
```

```mermaid
graph TD
    DBA2["DBA"] -->|grants| U1b["U1"]
    DBA2 -->|grants| U2b["U2"]
    U1b -->|grants| U4b["U4"]
    U1b -->|grants| U5b["U5"]
    U2b -->|grants| U5b

    style DBA2 fill:#c0392b,color:#fff
```

> **What is "cascading revocation"?** If you revoke a privilege from U1, that revocation also automatically cascades to anyone who only received the privilege *through* U1 (in the diagram above, that's U4). But notice U5 **keeps** the privilege — because U2 *also* independently granted it to U5. The rule is simple: a user keeps a privilege as long as **at least one valid path** still connects them back to the DBA (the root of the authorization graph).

### 4.7.7 Row-Level Authorization

Normal `grant`/`revoke` statements only control access at the level of an entire **table or view** — they can't restrict access to just some rows. Some database systems (like Oracle VPD, PostgreSQL, and SQL Server) go a step further and offer **row-level security**: they automatically attach a hidden condition (like `ID = current_user`) to every query, so, for example, a student can only see their *own* rows in `takes`, not everyone else's.

---

## Syllabus Connection

Intermediate SQL (Joins, Views), integrity constraints, and Access Control/Security (DAC and RBAC).

## Final Exam Pattern Mapping

- **Question 5(a) (2024 Exam):** Explaining database constraints and their role in maintaining data integrity with examples (PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, NOT NULL).
- **Question 5(c) (2023 & 2024 Exams):** Defining a **View** in SQL, explaining its types (virtual vs. materialized), and demonstrating view definition syntax.
- **Question 8(c) (2024 Exam):** Discussing and contrasting **Discretionary Access Control (DAC)** (user-centric permissions managed via GRANT/REVOKE) and **Role-Based Access Control (RBAC)** (permissions assigned to structural organizational roles to simplify administrative overhead).
- **Question 3(a) (2023 Exam):** Distinguishing between a **Natural Join** (implicitly equating all attributes with matching names) and an **Inner Join** (requiring explicit mapping criteria).
