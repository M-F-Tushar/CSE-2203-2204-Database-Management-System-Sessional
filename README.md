# CSE 2203 & CSE 2204: Database Management System (Theory + Sessional/Lab)
### Chandpur Science and Technology University (CSTU) — Master Study Guide

An exhaustive, chapter-by-chapter and subsection-by-subsection study guide for **CSE 2203: Database Management System (Theory)** and **CSE 2204: Database Management System Sessional (Lab)**, aligned with the university syllabus and mapped directly against previous final exam questions (2023, 2024) and class tests (CT).

## How This Repository Is Organized

The repository is split into **Parts** (major syllabus areas). Each Part is a folder containing one **README.md** (a table of contents / summary for that Part) plus one subfolder per **Chapter**. Each Chapter folder contains its own **README.md** with:

- **Exact Subsections to Read** — the precise textbook sections to study
- **Syllabus Connection** — how the chapter maps to the official course syllabus
- **Final Exam Pattern Mapping** — the exact past-paper/class-test questions tied to that chapter, with model explanations

```
📁 Part 1 → 📁 Chapter 1, 📁 Chapter 2, 📁 Chapter 6 ...
📁 Part 2 → 📁 Chapter 3, 📁 Chapter 4, 📁 Chapter 5 ...
...
```

Click into a **Part** folder to see its chapters. Click into a **Chapter** folder to read the full study notes for that chapter.

## Table of Contents

| Part | Title | Chapters |
|------|-------|----------|
| [Part 0](Part-0-Final-Year-Question-and-Answer/README.md) | Final Year Question and Answer | [Theory](Part-0-Final-Year-Question-and-Answer/Theory/README.md) · [Lab](Part-0-Final-Year-Question-and-Answer/Lab/README.md) |
| [Part 1](Part-1-Database-System-Foundations-and-Conceptual-Design/README.md) | Database System Foundations & Conceptual Design | [Ch.1](Part-1-Database-System-Foundations-and-Conceptual-Design/Chapter-01-Introduction/README.md) · [Ch.2](Part-1-Database-System-Foundations-and-Conceptual-Design/Chapter-02-Introduction-to-the-Relational-Model/README.md) · [Ch.6](Part-1-Database-System-Foundations-and-Conceptual-Design/Chapter-06-Database-Design-Using-the-ER-Model/README.md) |
| [Part 2](Part-2-SQL-and-Application-Design/README.md) | SQL & Application Design | [Ch.3](Part-2-SQL-and-Application-Design/Chapter-03-Introduction-to-SQL/README.md) · [Ch.4](Part-2-SQL-and-Application-Design/Chapter-04-Intermediate-SQL/README.md) · [Ch.5](Part-2-SQL-and-Application-Design/Chapter-05-Advanced-SQL/README.md) · [Ch.8](Part-2-SQL-and-Application-Design/Chapter-08-Complex-Data-Types/README.md) · [Ch.10](Part-2-SQL-and-Application-Design/Chapter-10-Big-Data-NoSQL/README.md) |
| [Part 3](Part-3-Schema-Design-and-Normalization/README.md) | Schema Design & Normalization | [Ch.7](Part-3-Schema-Design-and-Normalization/Chapter-07-Relational-Database-Design-Normalization/README.md) |
| [Part 4](Part-4-Storage-File-Structures-and-Indexing/README.md) | Storage, File Structures & Indexing | [Ch.12](Part-4-Storage-File-Structures-and-Indexing/Chapter-12-Physical-Storage-Systems/README.md) · [Ch.13](Part-4-Storage-File-Structures-and-Indexing/Chapter-13-Data-Storage-Structures/README.md) · [Ch.14](Part-4-Storage-File-Structures-and-Indexing/Chapter-14-Indexing/README.md) |
| [Part 5](Part-5-Query-Processing-and-Optimization/README.md) | Query Processing & Optimization | [Ch.15-16](Part-5-Query-Processing-and-Optimization/Chapter-15-16-Query-Processing-and-Optimization/README.md) |
| [Part 6](Part-6-Transaction-Management-and-Concurrency-Control/README.md) | Transaction Management & Concurrency Control | [Ch.17](Part-6-Transaction-Management-and-Concurrency-Control/Chapter-17-Transactions/README.md) · [Ch.18](Part-6-Transaction-Management-and-Concurrency-Control/Chapter-18-Concurrency-Control/README.md) · [Ch.19](Part-6-Transaction-Management-and-Concurrency-Control/Chapter-19-Recovery-System/README.md) |
| [Part 7](Part-7-Distributed-Databases-and-Architectures/README.md) | Distributed Databases & Architectures | [Ch.20](Part-7-Distributed-Databases-and-Architectures/Chapter-20-Database-System-Architectures/README.md) |

## Suggested Study Flow

1. Start with **Part 1** to build conceptual foundations (data models, relational model, E-R design).
2. Move to **Part 2** for hands-on SQL — this is also the backbone of the CSE 2204 Sessional/Lab.
3. Study **Part 3** (Normalization) right after SQL, since exam questions frequently combine schema design with FD/normal-form analysis.
4. Cover **Part 4–7** in order for the systems-level topics (storage, indexing, query optimization, transactions, concurrency, recovery, distributed systems).

## Exam Source Legend

- **Final Exam** references refer to CSTU semester final theory exams (2023, 2024).
- **CT** references refer to in-semester Class Tests (e.g., CT 04).
- **Sessional/Lab** references refer to CSE 2204 practical exams built around PostgreSQL/SQL.