# Chapter 19: Recovery System

**Part:** [Part 6 — Transaction Management & Concurrency Control](../README.md)

## Exact Subsections to Read

- **19.1** Failure Classification
- **19.3** Recovery and Atomicity (Log-based recovery, Undo/Redo operations)
- **19.4** Recovery Algorithm (Immediate modification and rollback)

## Syllabus Connection

Database Recovery Management.

## Board Exam Pattern Mapping

- **Question 7(a) (2023 Exam):** Explaining the **Write-Ahead Logging (WAL)** protocol (the log record for an update must be flushed to stable storage before the corresponding dirty database block is written to disk).
- **Question 1 (Practice Exam):** Explaining why undo actions are executed in reverse order (to restore the database to its exact prior state) while redo actions are executed in the forward direction (to repeat history up to the crash point).
