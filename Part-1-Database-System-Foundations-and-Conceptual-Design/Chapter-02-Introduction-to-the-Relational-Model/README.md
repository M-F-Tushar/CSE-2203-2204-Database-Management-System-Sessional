# Chapter 2: Introduction to the Relational Model

**Part:** [Part 1 — Database System Foundations & Conceptual Design](../README.md)

## Exact Subsections to Read

- **2.1** Structure of Relational Databases (Tuples, attributes, domains, atomic values)
- **2.2** Database Schema
- **2.3** Keys (Superkey, Candidate key, Primary key, Foreign key, Alternate key)
- **2.4** Schema Diagrams
- **2.6** The Relational Algebra (Unary operations: Select σ, Project Π, Rename ρ; Binary operations: Cartesian Product ×, Union ∪, Set Difference −, Set Intersection ∩, Theta Join ⋈θ)

## Syllabus Connection

Relational model, relational algebra, and relational keys.

## Board Exam Pattern Mapping

- **Question 2(c) (2024 Exam):** Justifying the statement: *"All candidate keys are super keys, but not all super keys are candidate keys"* (A candidate key is a minimal superkey; adding any extra attribute to a candidate key still uniquely identifies a row and forms a superkey, but it is no longer minimal).
- **Question 2(d) (2024 Exam):** Finding super keys, candidate keys, alternate keys, and composite keys in a given table (e.g., matching IDs, names, and phone numbers where name is not unique but ID and Phone are unique).
- **Question 3(a) (2023 Exam):** Distinguishing between a **Relation** (the mathematical set of tuples) and a **Relation Schema** (the logical structure defining the attributes and their domains).
- **Question 4(d) (2023 & 2024 Exams):** Writing Relational Algebra Queries for selection, joins, and projections (e.g., retrieving employees in a department holding a specific position, or finding employees earning more than a certain salary threshold).
