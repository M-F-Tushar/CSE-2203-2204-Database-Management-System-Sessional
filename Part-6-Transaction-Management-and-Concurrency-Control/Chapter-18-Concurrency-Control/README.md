# Chapter 18: Concurrency Control

**Part:** [Part 6 — Transaction Management & Concurrency Control](../README.md)

## Exact Subsections to Read

- **18.1** Lock-Based Protocols (Shared/Exclusive locks, Two-Phase Locking Protocol (2PL): Growing phase and Shrinking phase)
- **18.2** Deadlock Handling (Deadlock prevention vs. Deadlock detection using Wait-For Graphs (WFG), and recovery)
- **18.3** Multiple Granularity
- **18.5** Timestamp-Based Protocols
- **18.6** Validation-Based Protocols (Optimistic Concurrency Control)

## Syllabus Connection

Concurrency control, locks, deadlocks, and multi-granularity.

## Board Exam Pattern Mapping

- **Question 7(d) (2024 Exam):** Comparing different concurrency control techniques, specifically (i) Shared/Exclusive Locks under **2PL** (Growing phase acquires locks, Shrinking phase releases locks; guarantees serializability but does not prevent deadlocks), (ii) **Timestamp-Based Protocols**, and (iii) **Optimistic Concurrency Control** (Read, Validation, and Write phases).
- **Question 8(d) (2024 Exam):** Explaining how database systems detect and recover from deadlocks, and drawing a **Wait-For Graph (WFG)** where a deadlock cycle exists.
