# Chapter 14: Indexing

**Part:** [Part 4 — Storage, File Structures & Indexing](../README.md)

## Exact Subsections to Read

- **14.1** Basic Concepts of Indexing
- **14.2** Ordered Indices (Primary/Clustering index vs. Secondary/Nonclustering index, Sparse vs. Dense indices)
- **14.3** B+-Tree Index Files (Structure, properties, node size, and properties of leaf/nonleaf nodes)
- **14.4** B+-Tree Extensions (Insertion and deletion step-by-step algorithms, node splitting and merging)
- **14.5** Hash Indices (Static hashing, hash functions, collision handling, and Dynamic Hashing/Extendible Hashing)

## Syllabus Connection

Indexing (primary/secondary, B+ trees, hashing).

## Board Exam / Class Test Mapping

- **Question 6(b) (2024 Exam):** Defining indexing and comparing a **dense index** (entry for every search-key value) with a **sparse index** (entry for only some search-key values) in terms of structure, lookup speed, and storage overhead.
- **Question 6(c) (2024 Exam) & Question 8(c) (2023 Exam):** Inserting a sequence of key values (e.g., 6, 16, 26, 36, 46) step-by-step into a B+ tree of order 3, detailing every node split and drawing the final tree.
- **Question 6(d) (2024 Exam):** Explaining hash functions, illustrating hash table implementations, and demonstrating how collisions are handled (e.g., overflow chaining or closed hashing).
- **Question 6(c) (2023 Exam):** Comparing static vs. dynamic hashing in file organization.
