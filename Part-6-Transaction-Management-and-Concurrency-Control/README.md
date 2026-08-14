# Part 6: Transaction Management & Concurrency Control

This Part covers how a DBMS guarantees correctness (ACID) when multiple transactions run concurrently, and how it recovers from failures.

## Chapters in This Part

| Chapter | Title | Focus |
|---------|-------|-------|
| [Chapter 17](Chapter-17-Transactions/README.md) | Transactions | ACID, transaction states, serializability, isolation levels |
| [Chapter 18](Chapter-18-Concurrency-Control/README.md) | Concurrency Control | Locking (2PL), deadlocks, timestamp/validation protocols |
| [Chapter 19](Chapter-19-Recovery-System/README.md) | Recovery System | Failure classification, WAL, undo/redo logging |

## Why This Part Matters

This is one of the densest theory Parts — expect diagram-based questions (transaction state diagrams, precedence graphs, wait-for graphs) that require careful step-by-step reasoning, not just definitions.
