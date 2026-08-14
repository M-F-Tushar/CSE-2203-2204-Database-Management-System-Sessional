# Chapter 6: Database Design Using the E-R Model

**Part:** [Part 1 — Database System Foundations & Conceptual Design](../README.md)

## Exact Subsections to Read

- **6.1** Overview of the Design Process (Avoiding Redundancy and Incompleteness)
- **6.2** The Entity-Relationship Model (Entity sets, Relationship sets, attributes)
- **6.3** Complex Attributes (Composite, Multivalued, and Derived attributes)
- **6.4** Mapping Cardinalities (1:1, 1:N, N:1, M:N and participation constraints like total vs. partial)
- **6.5** Primary Key for Weak Entity Sets
- **6.6** Removing Redundant Attributes in Entity Sets
- **6.7** Reducing E-R Diagrams to Relational Schemas (Rules for strong entities, weak entities, and binary/many-to-many relationship sets)
- **6.8** Extended E-R Features (Specialization, Generalization, Attribute Inheritance, and Completeness/Disjointness constraints)

## Syllabus Connection

Entity-Relationship Model, cardinality constraints, and physical/logical conversion.

## Board Exam Pattern Mapping

- **Question 1(d) (2024 Exam):** Drawing an E-R diagram for a Library Management System with Book, Member, Loan, and Publisher as entities and defining their mapping cardinalities.
- **Question 3(c) (2024 Exam):** Designing an E-R diagram for a Hospital Management System with Doctors, Patients, and Log of Tests, ensuring proper cardinality.
- **Question 5(b) (2023 Exam):** Designing an E-R diagram for a University System with Student, Course, and Instructor entity sets and their relationships.
- **Question 5(c) (2023 Exam):** Analyzing an E-R diagram fragment to suggest normalization by removing redundant attributes (e.g., deleting a department location attribute from a Student entity when it is already represented via a relationship to the Department entity).
