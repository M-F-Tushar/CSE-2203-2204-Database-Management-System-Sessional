# Chapter 7: Relational Database Design (Normalization)

**Part:** [Part 3 — Schema Design & Normalization](../README.md)

## Exact Subsections to Read

- **7.1** Features of Good Relational Designs (Understanding Insert/Update/Delete anomalies and lossy vs. lossless decomposition)
- **7.2** Decomposition Using Functional Dependencies (Definition of Functional Dependency)
- **7.3** Normal Forms (Boyce-Codd Normal Form, Third Normal Form, comparing 3NF and BCNF)
- **7.4** Functional-Dependency Theory (Armstrong's Axioms, Closure of FD sets F+, Closure of Attribute Sets α+, Canonical Cover Fc)
- **7.5** Algorithms for Decomposition (Lossless join test, dependency-preserving 3NF and BCNF decomposition)

## Syllabus Connection

Functional dependencies, normalization, normal forms (1NF, 2NF, 3NF, BCNF), and candidate key identification.

## Board Exam Pattern Mapping

- **Question 3(d) (2024 Exam):** Finding the Candidate Key of a relation schema R(A,B,C,D,E,H) with the functional dependency set F={A→B,C…} by computing the attribute closure of candidate subsets.
- **Question 4(a) (2024 Exam):** Justifying whether **BCNF** is always preferable to **3NF**, using real-world scenarios to explain the trade-off (BCNF eliminates all redundancy based on FDs but is not always dependency-preserving, whereas 3NF allows minor redundancy but always preserves dependencies).
- **Question 4(c) (2024 Exam):** Determining the normal form (1NF, 2NF, 3NF, or BCNF) of a given relation R(A,B,C,D,E,H) under a set of functional dependencies.
- **Question 3(b) (2023 Exam):** Describing Insert, Update, and Delete anomalies using a non-normalized combined relation (e.g., in_dep).
