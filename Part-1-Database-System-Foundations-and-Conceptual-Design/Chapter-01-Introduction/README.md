# Chapter 1: Introduction

**Part:** [Part 1 — Database System Foundations & Conceptual Design](../README.md)
**Textbook:** *Database System Concepts*, 7th Edition — Silberschatz, Korth, Sudarshan

## Exact Subsections to Read

- **1.1** Database-System Applications
- **1.2** Purpose of Database Systems *(Critical: understand the comparison with file-processing systems)*
- **1.3** View of Data *(Critical: Data Models, Data Abstraction, Instances and Schemas)*
- **1.4** Database Languages (DDL and DML)
- **1.6** Database Engine (Storage Manager, Query Processor, Transaction Manager)
- **1.7** Database and Application Architecture (Two-tier vs. Three-tier architectures)
- **1.8** Database Users and Administrators

> A **database-management system (DBMS)** is a collection of interrelated data (the **database**) plus a set of programs to access, manage, and manipulate that data. Its primary goal is to provide a **convenient** and **efficient** way to store and retrieve information while ensuring the data's safety, consistency, and availability to many simultaneous users.

---

## 1.1 Database-System Applications

All database applications — old and new — share one thing in common: the **data itself** is the central asset, not the program that briefly processes it. A bank without its account data, or a social network without its user connections, would lose nearly all of its value.

Database systems are used to manage collections of data that are:

- **Highly valuable** — the information often *is* the business.
- **Relatively large** — too big to manage manually or in memory alone.
- **Accessed concurrently** — by many users and applications at the same time.

### Representative Application Domains

| Domain | Example Uses |
|---|---|
| **Enterprise Information** | Sales (customers, products, purchases), Accounting (payments, balances), Human Resources (payroll, benefits) |
| **Manufacturing** | Supply-chain management, production tracking, inventory, orders |
| **Banking & Finance** | Customer accounts and loans, credit-card transactions, stock/bond trading |
| **Universities** | Student records, course registration, grades |
| **Airlines** | Reservations and schedules (early pioneers of geographically distributed databases) |
| **Telecommunication** | Call/text/data usage records, billing, prepaid balances |
| **Web-Based Services** | Social media (users, posts, likes), online retail (orders, browsing history), online ads (click tracking) |
| **Document Databases** | News articles, patents, research papers |
| **Navigation Systems** | Points of interest, road/route networks |

### Two Modes of Database Use

```mermaid
graph LR
    A[Database Usage] --> B[Online Transaction<br/>Processing OLTP]
    A --> C[Data Analytics]

    B --> B1["Many users<br/>Small reads/writes each<br/>e.g. booking a seat,<br/>registering for a course"]
    C --> C1["Processes large volumes of data<br/>Finds patterns & builds<br/>predictive models"]
    C --> C2["e.g. loan approval prediction,<br/>ad targeting, demand forecasting"]

    style A fill:#4a90d9,color:#fff
    style B fill:#57a773,color:#fff
    style C fill:#c9642a,color:#fff
```

**Key idea:** Abstraction lets a driver operate a car without knowing how the engine works. Likewise, a DBMS gives users and programmers a simple, abstract view of data while hiding the complex machinery underneath.

---

## 1.2 Purpose of Database Systems

> **Critical topic** — this is one of the most frequently examined comparisons in the course.

To see *why* DBMSs exist, imagine a university keeping student, instructor, and course data in plain **operating-system files**, processed by custom-written application programs (a **file-processing system**). Every new requirement (a new major, a new report) means writing a brand-new program, and the pile of files/programs keeps growing in an uncoordinated way.

### Disadvantages of File-Processing Systems

```mermaid
flowchart TD
    FP[File-Processing System]
    FP --> D1["**Data Redundancy & Inconsistency**<br/>Same data duplicated across files;<br/>updates may not reach every copy"]
    FP --> D2["**Difficulty in Accessing Data**<br/>No program exists for an<br/>unanticipated query; needs new code"]
    FP --> D3["**Data Isolation**<br/>Data scattered across files in<br/>different formats"]
    FP --> D4["**Integrity Problems**<br/>Consistency constraints buried in<br/>scattered application code"]
    FP --> D5["**Atomicity Problems**<br/>A crash mid-operation can leave<br/>data in a half-updated state"]
    FP --> D6["**Concurrent-Access Anomalies**<br/>Simultaneous updates can<br/>overwrite each other (lost updates)"]
    FP --> D7["**Security Problems**<br/>Hard to restrict access to only<br/>parts of the data per user role"]

    style FP fill:#c0392b,color:#fff
```

### Illustrative Example — Concurrent-Access Anomaly

Two clerks both withdraw money from an account with a balance of $10,000 (withdrawing $500 and $100 respectively) at the same time:

1. Both programs **read** the old balance: $10,000.
2. Clerk A computes $10,000 − $500 = $9,500 and writes it back.
3. Clerk B computes $10,000 − $100 = $9,900 and writes it back — **overwriting** Clerk A's update.
4. Final balance is incorrectly $9,900 or $9,500, instead of the correct $9,400.

This is exactly why **transaction management** and **concurrency control** (covered later in the course) are essential.

### DBMS vs. File-Processing System

| Aspect | File-Processing System | Database System (DBMS) |
|---|---|---|
| Redundancy | High — data duplicated per program | Minimized via centralized schema |
| Data access | Needs new program per new query | Flexible ad-hoc queries (SQL) |
| Consistency | Enforced manually in each program | Enforced centrally via constraints |
| Atomicity | Not guaranteed | Guaranteed by transaction manager |
| Concurrency | Anomalies likely | Controlled via concurrency-control manager |
| Security | Ad-hoc, hard to enforce | Fine-grained authorization support |

---

## 1.3 View of Data

> **Critical topic** — Data Models, Data Abstraction, and Instances/Schemas are consistently tested.

A database system provides an **abstract view** of data: users interact with a simplified representation while the system hides how data is physically stored.

### 1.3.1 Data Models

A **data model** is a collection of conceptual tools for describing data, relationships, semantics, and consistency constraints. Four broad categories:

```mermaid
graph TD
    DM[Data Models] --> R["**Relational Model**<br/>Data as tables (relations)<br/>with rows & named columns"]
    DM --> ER["**Entity-Relationship Model**<br/>Entities + relationships<br/>(used for design, Ch. 6)"]
    DM --> SS["**Semi-structured Model**<br/>JSON / XML — items of the<br/>same type may differ in attributes"]
    DM --> OB["**Object-Based Model**<br/>Encapsulation, methods,<br/>object identity"]

    style DM fill:#4a90d9,color:#fff
```

The **relational model** dominates modern practice: data is stored as a set of **tables (relations)**, each with a fixed set of named **columns (attributes)**, and rows represent individual records.

**Example — `instructor` and `department` tables:**

| ID | name | dept_name | salary |
|---|---|---|---|
| 22222 | Einstein | Physics | 95000 |
| 12121 | Wu | Finance | 90000 |
| 76766 | Crick | Biology | 72000 |

| dept_name | building | budget |
|---|---|---|
| Physics | Watson | 70000 |
| Biology | Watson | 90000 |
| Finance | Painter | 120000 |

### 1.3.2 Data Abstraction

Since real storage structures are complex, a DBMS hides this complexity behind **three levels of abstraction**:

```mermaid
graph TD
    V["**View Level**<br/>Multiple partial views tailored<br/>to different user groups<br/>(e.g., registrar sees only student data)"]
    L["**Logical Level**<br/>What data is stored & what<br/>relationships exist among them<br/>(the full schema — DBA's view)"]
    P["**Physical Level**<br/>How data is actually stored:<br/>files, indices, byte layouts"]

    V --> L --> P

    style V fill:#57a773,color:#fff
    style L fill:#4a90d9,color:#fff
    style P fill:#8e44ad,color:#fff
```

- **Physical level** — lowest level; describes low-level storage structures in detail (block layout, indices).
- **Logical level** — describes *what* data is stored and the relationships among data, using relatively simple structures; this is the level database administrators work at.
- **View level** — highest level; each view exposes only the portion of the database relevant to a particular class of user, also acting as a **security mechanism**.

**Physical data independence:** the ability to modify the physical schema (how data is stored) *without* requiring changes to the logical schema or application programs — because the logical level hides physical details.

### 1.3.3 Instances and Schemas

| Concept | Analogy (Programming Language) | Meaning |
|---|---|---|
| **Schema** | Variable declarations + type definitions | The overall, *time-invariant* **design/blueprint** of the database |
| **Instance** | Values held by variables at one moment | The actual data **stored at a particular point in time** |

- **Physical schema** — database design at the physical level.
- **Logical schema** — database design at the logical level (most important — application programs are built against this).
- **Subschema** — a schema at the view level.

Because instances change constantly (inserts/deletes/updates) while the schema stays comparatively stable, a good design keeps application programs dependent only on the **logical schema**, not on how data is stored physically — this, again, is **physical data independence**.

---

## 1.4 Database Languages

A DBMS provides a **Data-Definition Language (DDL)** to define the schema and a **Data-Manipulation Language (DML)** to query and update data. In practice (e.g., SQL), both are part of one unified language.

```mermaid
flowchart LR
    subgraph DBLang["Database Language"]
        DDL["**DDL**<br/>Data-Definition Language<br/>defines schema + constraints"]
        DML["**DML**<br/>Data-Manipulation Language<br/>queries + updates data"]
    end
    DDL -->|"create table,<br/>constraints"| DD[(Data Dictionary<br/>Metadata)]
    DML -->|"select, insert,<br/>update, delete"| Data[(Actual Data)]

    style DDL fill:#4a90d9,color:#fff
    style DML fill:#57a773,color:#fff
```

### 1.4.1 Data-Definition Language (DDL)

The DDL defines the schema **and** the integrity constraints that the system enforces on every update:

- **Domain Constraints** — restricts the values an attribute can take (e.g., integer, date, char(20)). Cheapest to check.
- **Referential Integrity** — a value referenced in one relation must actually exist in another (e.g., every `dept_name` in `course` must exist in `department`). Violations are normally **rejected**.
- **Authorization** — controls who may do what:
  - **read** authorization — may read, not modify.
  - **insert** authorization — may add new data.
  - **update** authorization — may modify, not delete.
  - **delete** authorization — may remove data.

DDL statements are compiled and the result is stored in the **data dictionary**, a special table of metadata ("data about data") consulted by the system before every access.

**Example (SQL DDL):**

```sql
create table department
    (dept_name char(20),
     building  char(15),
     budget    numeric(12,2));
```

### 1.4.2 Data-Manipulation Language (DML)

A DML enables **retrieval**, **insertion**, **deletion**, and **modification** of data.

```mermaid
graph TD
    DML2[DML Types] --> Proc["**Procedural DML**<br/>User specifies WHAT data<br/>is needed AND HOW to get it"]
    DML2 --> Decl["**Declarative / Non-procedural DML**<br/>User specifies only WHAT data<br/>is needed — system decides HOW"]
    Decl --> SQLNote["SQL is the classic example.<br/>Easier to learn & use;<br/>the query optimizer picks<br/>the efficient access plan"]

    style Proc fill:#c0392b,color:#fff
    style Decl fill:#27ae60,color:#fff
```

A **query** is a DML statement requesting information retrieval; loosely, "query language" and "DML" are used interchangeably.

**Example (SQL query)** — find names of all instructors in the History department:

```sql
select instructor.name
from   instructor
where  instructor.dept_name = 'History';
```

**Example (multi-table query)** — instructors in departments with budget > $95,000:

```sql
select instructor.ID, department.dept_name
from   instructor, department
where  instructor.dept_name = department.dept_name
  and  department.budget > 95000;
```

### 1.4.3 Database Access from Application Programs

SQL alone cannot do everything a general-purpose language can (e.g., user I/O, network communication) — it is not Turing-complete for such tasks. So SQL statements are **embedded** inside a **host language** (Java, Python, C/C++) via an application-program interface:

- **ODBC** (Open Database Connectivity) — for C and other languages.
- **JDBC** (Java Database Connectivity) — for Java.

---

## 1.6 Database Engine

The internal architecture of a DBMS is divided into three broad functional components:

```mermaid
flowchart TB
    subgraph Engine["Database Engine"]
        direction TB
        QP["**Query Processor**<br/>DDL Interpreter, DML Compiler<br/>& Query Optimizer, Query<br/>Evaluation Engine"]
        SM["**Storage Manager**<br/>Authorization & Integrity Manager,<br/>Transaction Manager, File Manager,<br/>Buffer Manager"]
        TM["**Transaction Management**<br/>Concurrency-Control Manager<br/>+ Recovery Manager"]
    end
    QP --> SM
    SM --> Disk[("Disk Storage<br/>data files, indices,<br/>data dictionary")]
    TM -.ensures ACID.-> QP
    TM -.ensures ACID.-> SM

    style QP fill:#4a90d9,color:#fff
    style SM fill:#57a773,color:#fff
    style TM fill:#c0392b,color:#fff
```

### 1.6.1 Storage Manager

Bridges the gap between low-level stored data and the application/query layer. It translates DML operations into low-level file-system commands and consists of:

- **Authorization & Integrity Manager** — checks user privileges and integrity constraints.
- **Transaction Manager** — keeps the database consistent despite failures and concurrent access.
- **File Manager** — manages disk-space allocation and file data structures.
- **Buffer Manager** — fetches data from disk into main memory and decides what to cache; crucial since databases are far larger than RAM.

Physical structures it maintains: **data files**, the **data dictionary**, and **indices** (fast lookup structures, analogous to a book's index).

### 1.6.2 Query Processor

Simplifies and speeds up access to data so users needn't understand physical-level implementation:

- **DDL Interpreter** — interprets DDL statements, records definitions in the data dictionary.
- **DML Compiler** — translates DML statements into a low-level **evaluation plan**; also performs **query optimization**, i.e., selecting the *lowest-cost* plan among equivalent alternatives.
- **Query Evaluation Engine** — executes the low-level instructions produced by the compiler.

### 1.6.3 Transaction Management

A **transaction** is a collection of operations forming one logical unit of work (e.g., a bank funds transfer = debit + credit). The transaction manager must guarantee:

```mermaid
graph LR
    ACID["Transaction Properties<br/>(ACID)"] --> At["**Atomicity**<br/>All-or-nothing execution"]
    ACID --> Co["**Consistency**<br/>Moves DB from one<br/>valid state to another"]
    ACID --> Is["**Isolation**<br/>Concurrent transactions don't<br/>interfere with each other"]
    ACID --> Du["**Durability**<br/>Committed changes survive<br/>crashes permanently"]

    style ACID fill:#e67e22,color:#fff
```

- **Recovery Manager** — ensures **atomicity** and **durability**; restores the database to a consistent state after a crash (**failure recovery**).
- **Concurrency-Control Manager** — controls interleaving of concurrent transactions to preserve **consistency** and **isolation**.

---

## 1.7 Database and Application Architecture

Modern computer-system structure heavily shapes database-application design. Applications are commonly split into **two-tier** or **three-tier** architectures.

```mermaid
flowchart LR
    subgraph TwoTier["(a) Two-Tier Architecture"]
        direction LR
        C1[Client:<br/>User + Application] -->|"direct DB<br/>calls / SQL"| S1[Server:<br/>Database System]
    end
```

```mermaid
flowchart LR
    subgraph ThreeTier["(b) Three-Tier Architecture"]
        direction LR
        C2["Client:<br/>User + Application Client<br/>(web/mobile front-end)"] -->|network| AS["Application Server<br/>(business logic)"]
        AS -->|DB queries| DS["Database System"]
    end
```

| Feature | Two-Tier | Three-Tier |
|---|---|---|
| Structure | Client ↔ Database Server | Client ↔ Application Server ↔ Database Server |
| Business logic location | Spread across many clients | Centralized in the application server |
| Security | Weaker — clients access DB directly | Stronger — DB hidden behind app server |
| Performance at scale | Degrades with many clients | Scales better for web-scale traffic |
| Typical use today | Legacy / thick-client desktop apps | Web apps, mobile apps (web browser or app is the thin client) |

**Why three-tier wins for the web:** centralizing business logic in an application server means client machines (browsers, mobile apps) stay simple and interchangeable, security is easier to enforce at one choke point, and the architecture scales to millions of concurrent users — something a direct client-to-database link cannot do safely or efficiently.

---

## 1.8 Database Users and Administrators

### Types of Database Users

```mermaid
graph TD
    U[Database Users] --> N["**Naïve Users**<br/>Use predefined forms/apps<br/>(e.g., student registering<br/>for a class via a web form)"]
    U --> AP["**Application Programmers**<br/>Write the application<br/>programs / interfaces"]
    U --> SU["**Sophisticated Users**<br/>Write queries directly or use<br/>data-analysis tools (analysts)"]

    style U fill:#4a90d9,color:#fff
```

### Database Administrator (DBA)

The DBA is the person with **central control** over both the data and the programs accessing it. Core responsibilities:

| Responsibility | Description |
|---|---|
| **Schema definition** | Creates the original database schema via DDL statements |
| **Storage & access-method definition** | Chooses physical organization and indices |
| **Schema & physical-organization modification** | Adapts schema/design as enterprise needs change |
| **Granting authorization** | Controls which users can access which parts of the data |
| **Routine maintenance** | Backups, ensuring free disk space, monitoring job/query performance |

---

## Lecture Supplement: Additional Concepts from Course Lecture Notes

> The topics below extend the textbook material above with concepts covered in the course lecture slides — most importantly the **Hierarchical** and **Network** data models (referenced in past exam questions but not covered by the textbook excerpt), the **NoSQL model family**, **DCL/TCL**, and a concrete walk-through of the three-tier architecture.

### From Data to Information

In everyday speech *data* and *information* are used interchangeably, but in database theory they are distinct:

> **Data** = raw, uninterpreted facts (e.g., `72`, `Rahim Ahmed`, `CSE`). **Information** = data that has been processed/summarized into a meaningful, decision-supporting form (e.g., *"average class mark = 80"*).

A database's job is not just to store data — it is to make it easy to turn that raw data into useful information (e.g., a retailer analyzing millions of sales rows to find its best-selling product).

### Types of Data by Structure

```mermaid
graph TD
    T[Types of Data] --> S["**Structured Data**<br/>Fixed format, fits neatly into<br/>rows & columns (e.g., a Students table)"]
    T --> SS["**Semi-Structured Data**<br/>Partial organization with labels,<br/>but flexible attributes<br/>(e.g., JSON, XML, HTML)"]
    T --> U["**Unstructured Data**<br/>No predefined format<br/>(e.g., photos, videos, emails, PDFs)"]

    style S fill:#57a773,color:#fff
    style SS fill:#e67e22,color:#fff
    style U fill:#c0392b,color:#fff
```

A JSON document is the classic example of semi-structured data — it uses key–value pairs, but unlike a relational table, different documents of the "same" type can have different sets of attributes:

```json
{ "studentId": "220101", "name": "Rahim Ahmed", "age": 21, "department": "Computer Science", "cgpa": 3.82 }
```

### Redundancy vs. Inconsistency — A Concrete Illustration

The textbook groups **redundancy** and **inconsistency** together, but it helps to see them as cause and effect:

1. **Redundancy** — the same fact (e.g., a student's address) is duplicated in the Admissions file *and* the Accounts file.
2. **Inconsistency** — the student moves from Dhaka to Chattogram. The Admissions Office updates its copy, but the Accounts Office forgets. Now two files disagree, and the system has no way to know which value is correct.

This is precisely why a DBMS centralizes data into **one** authoritative copy instead of many scattered ones.

### Characteristics of a Well-Designed Database

| Characteristic | Meaning |
|---|---|
| **Organized structure** | Similar data grouped into tables/logical structures instead of stored randomly |
| **Related data** | Tables are logically linked (e.g., a shared `dept_name` connects `Students` and `Departments`) |
| **Persistence** | Data outlives the application/process that created it — it is still there the next time the app runs |
| **Shared access** | Many users (students, teachers, admins) use the same database concurrently, each for a different purpose |
| **Controlled redundancy** | Duplication is minimized (not necessarily zero) by storing shared facts once and referencing them |
| **Data integrity** | The system enforces rules so invalid data (e.g., CGPA = 5.30) is rejected |
| **Security** | Different users are restricted to different operations/data based on their role |

### Major Functions of a DBMS

```mermaid
graph LR
    F[DBMS Functions] --> F1[Creating<br/>Databases]
    F --> F2[Storing<br/>Data]
    F --> F3[Retrieving<br/>Data]
    F --> F4[Updating<br/>Data]
    F --> F5[Deleting<br/>Data]
    F --> F6[Security<br/>Management]
    F --> F7[Backup &<br/>Recovery]
    F --> F8[Concurrency<br/>Control]

    style F fill:#4a90d9,color:#fff
```

### Popular DBMS Products

| DBMS | Developed By | Common Applications |
|---|---|---|
| MySQL | Oracle Corporation | Web applications |
| PostgreSQL | PostgreSQL Global Development Group | Enterprise systems, research |
| Oracle Database | Oracle Corporation | Banking, finance, large enterprises |
| Microsoft SQL Server | Microsoft | Business applications |
| SQLite | SQLite Development Team | Mobile apps, embedded systems |
| MariaDB | MariaDB Foundation | Web servers, cloud applications |

### The Missing Data Models: Hierarchical, Network, Object-Oriented & NoSQL

The main body above already introduced the **Relational**, **ER**, **Semi-structured**, and **Object-Based** models. Two *older* models and one *modern family* complete the historical picture — and are a favorite past-exam comparison question:

```mermaid
graph TD
    H["**Hierarchical Model**<br/>Tree structure — each<br/>child has exactly ONE parent"]
    N["**Network Model**<br/>Graph structure — a<br/>child can have MULTIPLE parents"]
    R["**Relational Model**<br/>Tables + keys<br/>(the modern default)"]

    H -->|"evolved into"| N -->|"evolved into"| R

    style H fill:#8e44ad,color:#fff
    style N fill:#e67e22,color:#fff
    style R fill:#27ae60,color:#fff
```

**1. Hierarchical Model** — data organized as a tree (parent → child). Every record has exactly one parent (except the root). Modeled naturally with a `Parent_ID` column:

| Employee_ID | Employee_Name | Parent_ID | Position |
|---|---|---|---|
| 1 | CEO | NULL | Chief Executive |
| 2 | HR Manager | 1 | Manager |
| 5 | HR Officer 1 | 2 | Officer |

```
CEO
├── HR Manager
│   ├── HR Officer 1
│   └── HR Officer 2
└── Sales Manager
    ├── Sales Rep 1
    └── Sales Rep 2
```

*Real-world example:* file systems, company organograms. **Limitation:** cannot naturally represent many-to-many relationships (a file that lives in two folders, an employee who reports to two managers).

**2. Network Model** — generalizes the hierarchy into a **graph**, so a record can have *multiple* parents, directly supporting many-to-many relationships.

```mermaid
graph LR
    S1[Student 1] --> C1[Course 1]
    S1 --> C2[Course 2]
    S2[Student 2] --> C1
    S2 --> C3[Course 3]
    S3[Student 3] --> C2
    S3 --> C3

    style S1 fill:#4a90d9,color:#fff
    style S2 fill:#4a90d9,color:#fff
    style S3 fill:#4a90d9,color:#fff
```

*Real-world example:* airline route networks, complex inventory/bill-of-materials systems. The **relational model** later replaced both by expressing the same many-to-many relationship declaratively — with a `Students` table, a `Courses` table, and an `Enrollment` junction table linked purely by keys (no hard-wired physical pointers).

**3. Object-Oriented Model** — merges object-oriented programming (classes, inheritance, encapsulation) with the relational idea of persistent storage:

```mermaid
classDiagram
    Person <|-- Student
    Person <|-- Doctor
    Person <|-- Engineer
    class Person {
        -Name
        -Age
        +SetName()
    }
    class Student {
        -RollNo
        -Branch
        +SetMarks()
    }
    class Doctor {
        -D_ID
        -Specialist
        +CountOperation()
    }
    class Engineer {
        -E_ID
        -Department
        +Countpage()
    }
```

**4. NoSQL Models** — a modern family of non-relational models designed for scale and flexible schemas:

| NoSQL Type | Example Engine | Data Unit |
|---|---|---|
| **Document Model** | MongoDB | JSON/BSON documents grouped in collections |
| **Key–Value Model** | Redis | Simple key → value pairs |
| **Column-Family Model** | Cassandra | Rows with dynamic, wide sets of columns |
| **Graph Model** | Neo4j | Nodes + edges (relationships as first-class citizens) |

**MongoDB example** — each student is a self-contained document; note how nested `courses` arrays replace a separate join/`Enrollment` table entirely:

```json
{
  "_id": "S1",
  "name": "Rahim",
  "department": "CSE",
  "courses": [
    { "course_id": "C1", "name": "Database", "semester": "Fall 2025" },
    { "course_id": "C2", "name": "Networking", "semester": "Fall 2025" }
  ]
}
```

### Database Languages — Completing the Set (DCL and TCL)

The textbook body above covers **DDL** and **DML** in depth. Two more categories complete the standard four-part classification:

```mermaid
graph TD
    L[Database Languages] --> DDL2["**DDL**<br/>CREATE, ALTER, DROP<br/>defines structure"]
    L --> DML2["**DML**<br/>SELECT, INSERT, UPDATE, DELETE<br/>manipulates data"]
    L --> DCL["**DCL**<br/>GRANT, REVOKE<br/>controls access permissions"]
    L --> TCL["**TCL**<br/>COMMIT, ROLLBACK, SAVEPOINT<br/>manages transaction boundaries"]

    style DDL2 fill:#4a90d9,color:#fff
    style DML2 fill:#57a773,color:#fff
    style DCL fill:#8e44ad,color:#fff
    style TCL fill:#c0392b,color:#fff
```

- **DCL (Data Control Language)** — grants or revokes privileges on database objects (covered in depth in Chapter 4's Authorization section).
- **TCL (Transaction Control Language)** — marks the start/end of a logical unit of work and lets a transaction be rolled back to a savepoint if something goes wrong.

### A Detailed Look Inside the Database Engine

Zooming into the **Query Processor** and **Storage Manager** boxes from the textbook diagram earlier, the full internal pipeline looks like this:

```mermaid
flowchart TB
    subgraph Users2["Users & Tools"]
        NU["naive users"] --> AI["application<br/>interfaces"]
        AP2["application<br/>programmers"] --> APR["application<br/>programs"]
        SU2["sophisticated<br/>users"] --> QT["query<br/>tools"]
        DBA2["DBAs"] --> AT["administration<br/>tools"]
    end
    AI --> CL["compiler<br/>& linker"]
    APR --> CL
    QT --> DMLQ["DML queries"]
    AT --> DDLI["DDL interpreter"]

    subgraph QueryProc["Query Processor"]
        CL --> OBJ["application<br/>program object code"]
        DMLQ --> DMLC["DML compiler<br/>& organizer"]
        OBJ --> QEE["query evaluation<br/>engine"]
        DMLC --> QEE
    end

    subgraph StorageMgr["Storage Manager"]
        BM["buffer<br/>manager"]
        FM["file<br/>manager"]
        AIM["authorization &<br/>integrity manager"]
        TXM["transaction<br/>manager"]
    end

    QEE --> BM
    QEE --> FM
    DDLI --> AIM
    QEE --> TXM

    BM --> Disk[("disk storage:<br/>data, indices,<br/>data dictionary,<br/>statistical data")]
    FM --> Disk

    style QueryProc fill:#4a90d9,color:#fff
    style StorageMgr fill:#57a773,color:#fff
```

- **DDL Interpreter** — processes `CREATE`/`ALTER`/`DROP`, updating the schema and data dictionary.
- **DML Compiler & Organizer** — translates `SELECT`/`INSERT`/`UPDATE`/`DELETE` into an executable plan.
- **Compiler and Linker** — turns embedded-SQL application programs into executable object code linked against DBMS libraries.
- **Query Evaluation Engine** — actually runs the low-level plan and returns results.
- **Buffer Manager** — moves pages between disk and RAM, caching hot data.
- **File Manager** — allocates and organizes the physical database files on disk.
- **Authorization & Integrity Manager** — the enforcement point for privileges (DCL) and constraints.

### Three-Tier Architecture — A Concrete Request Walk-Through

The textbook already contrasts two-tier vs. three-tier structurally. Here is *exactly* what happens, step by step, when a student clicks **"View Results"** on a university web portal — useful for exam questions that ask you to "trace" a three-tier request:

```mermaid
sequenceDiagram
    participant Browser as Client (Browser)
    participant App as Application Server
    participant DB as Database Server

    Browser->>App: GET /results/220101 (no SQL — just a URL)
    App->>App: student_id = "220101"
    App->>DB: SELECT CourseCode, Grade FROM Result WHERE StudentID = '220101'
    DB-->>App: (CSE201, A), (CSE203, B+)
    App->>App: Convert rows to JSON
    App-->>Browser: { "student":"220101", "results":[...] }
    Browser->>Browser: Render results as a webpage
```

The key exam-worthy takeaway: **the client never sees or writes SQL** — only the application server talks to the database. This separation is exactly what makes the three-tier model more secure and scalable than the two-tier model.

### A Fourth User Category: Specialized Users

In addition to **Naïve Users**, **Application Programmers**, and **Sophisticated Users** described earlier, lecture material adds a fourth category:

- **Specialized Users** — build advanced, domain-specific database applications beyond ordinary business processing, such as systems for scientific research, AI/knowledge bases, computer-aided design (CAD), multimedia databases, and geographic information systems (GIS).

---

## Syllabus Connection

Concepts of database systems, schemas, instances, levels of data abstraction, and database architectures.

## Board Exam Pattern Mapping

- **Question 1(a) (2024 Exam):** Differentiating a DBMS from file-based systems and listing three advantages.
- **Question 1(b) (2024 Exam):** Differentiating **Data** (raw, uninterpreted facts), **Information** (processed, context-rich data), and a **Database** (structured, persistent, shared collection of related data).
- **Question 1(c) (2023 & 2024 Exams):** Comparing the **Hierarchical Model** (tree-structured, parent-child), **Network Model** (graph-structured, multiple parents), and **Relational Model** (table-structured using keys).
- **Question 3(a) (2024 Exam):** Distinguishing between a **Database Schema** (the overall design or blueprint of the database) and a **Database Instance** (the actual data stored at a particular moment in time).
- **Question 1(c) (2023 Exam):** Explaining the **Three Levels of Abstraction** (Physical, Logical, and View levels) and how they achieve physical data independence.
- **Question 1.12 & 1.18 (Practice Exercises / Exams):** Explaining why a **Three-Tier Architecture** (with an intermediate Application Server containing business logic) is superior for web-scale applications compared to a **Two-Tier Architecture** (where the client talks directly to the database server).
