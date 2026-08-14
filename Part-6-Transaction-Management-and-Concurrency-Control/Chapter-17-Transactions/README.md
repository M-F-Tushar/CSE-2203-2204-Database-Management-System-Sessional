# Chapter 17: Transactions

**Part:** [Part 6 — Transaction Management & Concurrency Control](../README.md)

## Exact Subsections to Read

- **17.1** Transaction Concept
- **17.2** A Simple Transaction Model
- **17.4** Transaction Atomicity and Durability (Commit and abort states)
- **17.5** Transaction Isolation
- **17.6** Serializability (Conflict serializability vs. View serializability, Precedence Graphs)
- **17.8** Transaction Isolation Levels (Dirty reads, non-repeatable reads, phantoms)

## Syllabus Connection

Transaction management (ACID, transaction states, serializability, recoverability).

## Board Exam Pattern Mapping

- **Question 7(a) (2024 Exam):** Describing the different states of a transaction (Active, Partially Committed, Committed, Failed, Aborted) and drawing the complete transaction state transition diagram.
- **Question 7(b) (2024 Exam):** Explaining the **ACID** properties (Atomicity, Consistency, Isolation, Durability) with clear database-related examples.
- **Question 7(c) (2024 Exam):** Drawing a **Precedence Graph** for a given schedule of read/write operations across T1, T2, and T3 to test whether the schedule is conflict serializable (Conflict exists if two operations of different transactions access the same item, and at least one is a write; draw directed edges Ti→Tj and check for cycles).
- **Question 8(a) (2024 Exam):** Defining cascadeless schedules and providing an example of a schedule that is recoverable but not cascadeless (A schedule is cascadeless if a transaction only reads data written by committed transactions; this prevents a single abort from causing a cascade of aborted transactions).
