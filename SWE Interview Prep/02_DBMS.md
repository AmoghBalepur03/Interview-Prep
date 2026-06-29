# Database Management Systems (DBMS)

A **DBMS** is software that stores, manages, and retrieves data while enforcing integrity, concurrency, and security. This note covers the full DBMS interview surface: the abstraction levels, ER/EER modeling and relational mapping, relational algebra, functional dependencies and normalization, SQL, transactions and concurrency control, and indexing — plus NoSQL and the CAP theorem.

---

## 1. Why a DBMS? (vs. File Systems)

Plain file processing suffers from:
- **Data redundancy and inconsistency** — same data duplicated across files.
- **Difficulty accessing data** — every new query needs a new program.
- **Data isolation** — data scattered across files/formats.
- **Integrity constraints** — hard to enforce across files.
- **Atomicity** — partial updates on failure.
- **Concurrency control** — concurrent access corrupts data.
- **Security** — hard to restrict access per user.

A DBMS solves all of these centrally.

---

## 2. Levels of Abstraction

- **Physical level** (lowest) — *how* data is actually stored: record placement on disk, format/encoding, access structures (B-trees, hash tables), physical pointers/offsets.
- **Logical level** — *what* data is stored and the relationships among it: entities, attributes, relationships; integrity constraints; the schema design.
- **View (external) level** (highest) — *how* data is presented to users; supports **security** (hide sensitive fields), **simplification** (show only relevant data), and **customization** (per-user views).

### Data Independence
- **Physical data independence** — change the physical schema (reorganize on disk) without affecting the logical schema.
- **Logical data independence** — change the logical schema (add an attribute) without affecting views (unless they depend on it).

### Schemas
- **Physical schema** — design at the physical level (storage, files, indexing).
- **Logical/Conceptual schema** — structure and relationships, independent of physical storage.

---

## 3. ER Model (Entity-Relationship)

- **Entity** — a distinctly identifiable real-world object (physical: student, book; conceptual: course, department). **Entity set** — a collection of entities of the same type sharing attributes. *Entity* → specific object (Student Raj, ID=101); *entity set* → all students.
- **Attribute** — a property describing an entity (becomes a column). **Domain/value set** — permitted values.
  - **Composite attribute** — divisible into sub-parts (address → street, city, zip; phone number). Map each **leaf sub-attribute** to a column.
  - **Multivalued attribute** — holds multiple values for one entity. Create a **separate table** with the owner's primary key plus the attribute.
  - **Derived attribute** — computable from others (age from DOB).
- **Relationship** — an association among entities. **Relationship set** — a set of relationships of the same type.
- **Attributes on relationships** — a property of tuples in the relationship set (or model the attribute's values as an entity set participating in the relationship).

### Degree of a Relationship
Number of entity types involved:
- **Unary (recursive)** — one entity type related to itself (an employee *manages* other employees; "manager"/"worker" are **roles**). Implemented with a self-referencing foreign key.
- **Binary** — two entity types (Customer *places* Order).
- **Ternary** — three (Supplier supplies Product to Store).
- **N-ary** — N entity types (Professor teaches Course in Department during Semester).

### Binary Relationship Cardinality
- **One-to-one** — each instance of A ↔ exactly one of B (person ↔ passport). Add a foreign key with a **UNIQUE NOT NULL** constraint.
- **One-to-many** — one A ↔ many B, each B ↔ one A (teacher ↔ courses). Put the FK on the many-side (course), with a NOT NULL constraint if total.
- **Many-to-many** — many A ↔ many B (courses ↔ students). Use an **association table** with a **composite primary key** of both FKs.

### Cardinality and Participation
- **Cardinality constraint** — min/max instances of B associated with each A (e.g., Customer-Account (1, N)).
- **Total participation** — every instance must participate (every Account must have a Customer); **double line** in ER diagrams.
- **Partial participation** — only some instances participate (some Professors advise); **single line**.

### Keys
- **Super key** — a set of attributes whose values uniquely determine each entity.
- **Candidate key** — a **minimal** super key.
- **Primary key** — a chosen candidate key. A relation may or may not have one.

### Weak Entity Sets
Entities that **lack a primary key of their own** and depend on a **strong/owner** entity for identification (e.g., `Dependents` rely on `Employee`). They are **existence-dependent**. A weak entity has a **partial key (discriminator)** that uniquely identifies it *relative to its owner*. Example: `Payment` (weak) under `Loan` (strong); `payment-number` is the discriminator; primary key = `(loan-number, payment-number)`. The identifying relationship is a **total, one-to-many** relationship from owner to weak entity.

### Aggregation
A higher-level abstraction that lets you treat a **relationship set as an entity set** — needed when a relationship participates in another relationship. Example: `Department - Manages → [Project - Works_On - Employee]`. Differs from a ternary relationship when the outer relationship has its own descriptive attributes/constraints.

---

## 4. EER Model (Enhanced ER)

### Superclass / Subclass and IS-A
- **Superclass** — general entity type; **subclass** — more specific type derived from it. Forms an **IS-A** relationship (a SECRETARY *is an* EMPLOYEE). A subclass entity cannot exist without the superclass; a superclass member may belong to zero, one, or many subclasses; subclasses **inherit** all superclass attributes and relationships.

### Specialization (top-down)
Dividing a superclass into subclasses by distinguishing characteristics. Specific attributes are unique to a subclass (TypingSpeed → SECRETARY).
- **Total** — every superclass entity must belong to some subclass (double line).
- **Partial** — a superclass entity may belong to no subclass (single line).
- **Disjoint** — an entity is in only one subclass.
- **Overlapping** — an entity can belong to multiple subclasses.

### Generalization (bottom-up)
Combining entity types with common features into a superclass (CAR, TRUCK → VEHICLE). Usually **total** since the superclass is formed by merging subclasses.

### Predicate-defined vs. User-defined
- **Predicate-defined (condition-defined)** — membership determined by a condition on an attribute (`SalaryType = 'Salaried'`).
- **Attribute-defined** — all subclasses defined by the same attribute (`JobType`).
- **User-defined** — no automatic condition; users assign membership manually.

### Lattice vs. Hierarchy
- **Hierarchy (single inheritance)** — each subclass has one superclass.
- **Lattice (multiple inheritance)** — a shared subclass has multiple superclasses (SALARIED_ENGINEER ⊂ SALARIED_EMPLOYEE and ENGINEER); inherits from all.

### Unions / Categories
A subclass formed from the **union** of multiple superclasses (a vehicle-registration OWNER can be PERSON, BANK, or COMPANY). 
- **Shared subclass (intersection)** — member must exist in **all** superclasses.
- **Category (union)** — member must exist in **at least one** superclass.

---

## 5. Mapping EER → Relational Model

- **Strong entity** — one table; include all simple attributes (flatten composites first); choose a primary key.
- **Weak entity** — table with W's simple attributes + owner's PK as FK; composite PK = (owner PK + partial key); use `ON DELETE CASCADE`.
- **Unary relationship** — self-referencing FK; cardinality decides uniqueness/NULLs (1:N manager→subordinate; 1:1 self-FK + UNIQUE; M:N association table).
- **1:1 binary** — (A) FK in one table with UNIQUE; (B) merge into one relation if both total (risk of redundancy); (C) cross-reference table.
- **1:N binary** — FK on the **N-side** referencing the 1-side PK; relationship attributes go on the N-side; NOT NULL if total.
- **M:N binary** — association table; PK = combination of both FKs (or surrogate + UNIQUE on the pair); relationship attributes here.
- **Multivalued attribute** — new relation with the attribute + owner PK as FK; PK = (attribute + owner PK).
- **N-ary relationship** — one association table with FKs to all participants; PK = combination of all FKs.
- **Generalization/specialization** — separate table for superclass + each subclass (subclass tables hold specific attributes + FK to superclass). Alternative: subclass-only tables with all attributes (more redundancy).
- **Shared subclass** — single relation with a type attribute (sparse NULLs), or with Boolean type flags.

### Integrity Constraints in SQL
- **NOT NULL**, **PRIMARY KEY** (unique identifier), **CHECK** (validate condition), **FOREIGN KEY** (links tables), **UNIQUE**.
- **Cascading actions** — `ON DELETE/UPDATE CASCADE` propagate changes for referential integrity.
- **Deferred constraints** — checked at commit (useful for interdependent inserts).

---

## 6. Relational Algebra

| Operation | Notation | Meaning |
|---|---|---|
| **Select** | $\sigma_p(r)$ | Tuples of $r$ satisfying predicate $p$ (using $\land, \lor, \lnot, =, \ne, >, \ge, <, \le$) |
| **Project** | $\pi_{A_1,\dots,A_k}(r)$ | Keep listed columns; duplicates removed (relations are sets) |
| **Union** | $r \cup s$ | Tuples in $r$ or $s$ (same arity, compatible domains) |
| **Set Difference** | $r - s$ | Tuples in $r$ but not $s$ |
| **Cartesian Product** | $r \times s$ | All combinations; used to define joins: $\theta\text{-join} = \sigma_{\text{cond}}(R \times S)$ |
| **Rename** | $\rho_X(E)$ | Name the result of $E$ as $X$ (optionally rename attributes) |
| **Natural Join** | $R \bowtie S$ | Combine tuples on common attributes; omit duplicate columns |
| **Division** | $R \div S$ | "For all" queries: values in $R$ related to **all** values in $S$ |

**Division example** — `Supplies(supplier_id, product_id) ÷ Products(product_id)` → suppliers who supply **every** product.

**Aggregate functions** — `avg, min, max, sum, count`. Notation: $G_1,\dots,G_n\, \mathcal{G}\, F_1(A_1),\dots,F_n(A_n)(E)$ where $G$ are grouping attributes and $F$ are aggregate functions.

**Joins:** inner (matching only), left/right outer (keep one side, NULL-fill), full outer (keep both, NULL-fill).

**Updates (algebraic semantics):** delete `r ← r − E`; insert `r ← r ∪ E`; update via generalized projection, e.g., raise balances by 5%: `Account ← π_{acc_no, name, balance*1.05}(Account)`. In practice DBMSs use SQL `UPDATE/DELETE/INSERT`.

---

## 7. Views

A **view** is a virtual relation derived from base tables — used when not all users should see the full logical model.

```sql
CREATE VIEW all_customer AS
  (SELECT branch_name, customer_name FROM depositor NATURAL JOIN account)
  UNION
  (SELECT branch_name, customer_name FROM borrower NATURAL JOIN loan);
```

**Updates through views** must translate to base-table updates. Limitations: some updates can't be translated (don't align with the view's filter), or are ambiguous (inserting a customer without knowing if it's a loan or account customer) — the system may reject or insert NULLs.

---

## 8. Functional Dependencies (FDs)

An FD $X \to Y$ on relation $R$ means: whenever two tuples agree on all attributes of $X$, they agree on all attributes of $Y$. If $X \to$ (all attributes of $R$), then $X$ is a key.

### Armstrong's Axioms (sound and complete)
1. **Reflexivity** — if $Y \subseteq X$ then $X \to Y$.
2. **Augmentation** — if $X \to Y$ then $ZX \to ZY$.
3. **Transitivity** — if $X \to Y$ and $Y \to Z$ then $X \to Z$.

Derived rules: **Union** ($X\to Y, X\to Z \Rightarrow X\to YZ$), **Decomposition** ($X\to YZ \Rightarrow X\to Y, X\to Z$), **Pseudotransitivity** ($W\to X, XY\to Z \Rightarrow WY\to Z$).

### Attribute Closure $X^+$
The set of all attributes determined by $X$:
1. Initialize $X^+ = X$.
2. For each FD $B_1\dots B_m \to C$ with all $B_i \in X^+$, add $C$.
3. Repeat until no change.

Use closure to test if $X$ is a super key ($X^+$ = all attributes) and to find candidate keys.

### Closure of an FD Set & Equivalence
$F^+$ = all FDs derivable from $F$ via Armstrong's Axioms. Two sets $F, G$ are **equivalent** iff $F^+ = G^+$.

### Minimal (Canonical) Cover
An FD set $F$ is minimal if (1) every FD has a single attribute on the right, (2) no FD is redundant, (3) no left-hand attribute is redundant.

Steps:
1. **Singleton RHS** — split $X \to abc$ into $X\to a, X\to b, X\to c$.
2. **Remove redundant FDs** — drop $X\to A$ if $A \in X^+$ computed without that FD. (Example with `a→b, c→b, d→a, d→b, ac→b`: `ac+ = acb` even without `ac→b`, so it's redundant.)
3. **Reduce LHS** — for each attribute in a composite LHS, drop it if the rest still determines the RHS.

---

## 9. Normalization (added detail)

Normalization decomposes large relations to eliminate redundancy and anomalies.

### 1NF — First Normal Form
Every column holds **atomic (indivisible)** values; no repeating groups. All attributes depend on the primary key.

### 2NF — Second Normal Form
In 1NF **and** no **partial dependency** — every non-key attribute is **fully functionally dependent** on the *whole* (composite) primary key, not part of it.

> **Example.** `Enrollment(StudentID, CourseID, CourseName, InstructorName, Grade)` with PK `(StudentID, CourseID)`. `CourseName, InstructorName` depend only on `CourseID` → partial dependency. Decompose into `Course(CourseID, CourseName, InstructorName)` and `Enrollment(StudentID, CourseID, Grade)`.

### 3NF — Third Normal Form
In 2NF **and** no **transitive dependency** — a non-key attribute must not depend on another non-key attribute.

> **Example.** `Employee(EmpID, EmpName, DeptID, DeptName, DeptHead)` with PK `EmpID`. `DeptName, DeptHead` depend on `DeptID` (non-key) → transitive. Decompose into `Employee(EmpID, EmpName, DeptID)` and `Department(DeptID, DeptName, DeptHead)`.

A relation is in 3NF if for every FD $X \to A$: $X$ is a super key, **or** $A$ is a prime attribute (part of some candidate key). 3NF decomposition is always **lossless and dependency-preserving**.

### BCNF — Boyce-Codd Normal Form
For **every** non-trivial FD $X \to A$, $X$ must be a **super key**. Algorithm: if $R$ is not in BCNF and $X \to A$ is a violating FD, decompose into $R - A$ and $XA$; recurse.

> **Worked example.** $R(A,B,C,D)$ with $A\to B,\; B\to C,\; C\to D$. Closure $A^+ = ABCD$, so $A$ is the only candidate key. $B\to C$ and $C\to D$ violate BCNF (B, C are not super keys). Decompose on $B\to C$: $R_1(B,C)$ and $R_2(A,B,D)$... continuing yields BCNF relations, **but** the FD $C\to D$ is **lost** — BCNF is not always dependency-preserving (the key trade-off vs. 3NF).

### 4NF — Fourth Normal Form
Removes **multivalued dependencies (MVDs)**. An MVD $X \twoheadrightarrow Y$ means $X$ independently determines a *set* of $Y$ values. A relation is in 4NF if it is in BCNF and for every non-trivial MVD $X \twoheadrightarrow Y$, $X$ is a super key.

> **Example.** `R(Student, Course, Hobby)` where `Student →→ Course` and `Student →→ Hobby` (courses and hobbies are independent). Storing every combination causes redundancy. Decompose into `(Student, Course)` and `(Student, Hobby)`.

### Decomposition Correctness
- **Lossless decomposition** — joining the pieces recovers exactly $R$. Rule: $R \to (R_1, R_2)$ is lossless if $R_1 \cap R_2$ is a super key of $R_1$ or $R_2$.
- **Lossy decomposition** — the natural join produces spurious/extra or missing rows (common attribute isn't a super key).
- **Dependency preserving** — all original FDs are enforceable on the decomposed relations without joining.

---

## 10. SQL (added detail)

### Statement Categories
| Category | Commands |
|---|---|
| **DDL** (Data Definition) | `CREATE, ALTER, DROP, TRUNCATE` |
| **DML** (Data Manipulation) | `INSERT, UPDATE, DELETE` |
| **DCL** (Data Control) | `GRANT, REVOKE` |
| **TCL** (Transaction Control) | `COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION` |
| **DQL** (Data Query) | `SELECT` |

### Querying

```sql
SELECT col1, col2, AGG(col3)
FROM   table
WHERE  predicate                 -- filter rows (before grouping)
GROUP BY col1, col2
HAVING AGG(col3) > 100           -- filter groups (after aggregation)
ORDER BY col1 DESC
LIMIT  10 OFFSET 20;
```

Logical evaluation order: `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT`.

### Joins
| Join | Result |
|---|---|
| **INNER** | Only matching rows |
| **LEFT (OUTER)** | All left rows; NULLs for unmatched right |
| **RIGHT (OUTER)** | All right rows; NULLs for unmatched left |
| **FULL (OUTER)** | All rows from both; NULLs where unmatched |
| **CROSS** | Cartesian product |
| **SELF** | Table joined to itself (aliases) |

### Subqueries
- **Non-correlated** — inner query runs once, independent of the outer.
- **Correlated** — inner query references the outer row; runs per outer row.

```sql
-- Correlated: employees earning above their department average
SELECT e.name
FROM   employee e
WHERE  e.salary > (SELECT AVG(salary) FROM employee
                   WHERE dept_id = e.dept_id);
```

### CTEs and Set Operators

```sql
WITH dept_avg AS (
  SELECT dept_id, AVG(salary) AS avg_sal FROM employee GROUP BY dept_id
)
SELECT e.name FROM employee e JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```

Set operators: `UNION` (distinct), `UNION ALL` (keep duplicates), `INTERSECT`, `EXCEPT`/`MINUS`.

### Window Functions
Compute across a set of rows **without collapsing** them:

```sql
SELECT name, dept_id, salary,
       ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn,
       RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,
       DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS drnk,
       LAG(salary)  OVER (PARTITION BY dept_id ORDER BY salary)      AS prev_sal,
       LEAD(salary) OVER (PARTITION BY dept_id ORDER BY salary)      AS next_sal
FROM employee;
```

- `ROW_NUMBER` — unique sequential number.
- `RANK` — ties share a rank, leaves gaps; `DENSE_RANK` — ties share, no gaps.
- `LAG`/`LEAD` — access previous/next row's value.

### Views, Triggers, Stored Procedures
- **View** — saved query (see §7).
- **Trigger** — code that runs automatically on `INSERT/UPDATE/DELETE` (auditing, enforcing rules).
- **Stored procedure** — precompiled routine stored in the DB (encapsulates logic, reduces round-trips).

---

## 11. Transactions

### ACID Properties
- **Atomicity** — all operations complete, or none do.
- **Consistency** — moves the DB from one valid state to another (constraints/triggers hold).
- **Isolation** — concurrent transactions don't see each other's intermediate state.
- **Durability** — committed changes survive failures.

### Types of Failures
- **System crash** — hardware (power, disk) or software (OS/DBMS) failure.
- **Transaction failure** — logical error (division by zero, overdraft, duplicate-key) or input error.
- **Local/application errors** — data not found, constraint violation (e.g., FK violation).

### Isolation Levels (and the anomalies they prevent)
| Level | Dirty Read | Non-repeatable Read | Phantom |
|---|---|---|---|
| **Read Uncommitted** | possible | possible | possible |
| **Read Committed** | prevented | possible | possible |
| **Repeatable Read** | prevented | prevented | possible |
| **Serializable** | prevented | prevented | prevented |

---

## 12. Concurrency Control

### Advantages of Concurrency
Increased throughput, better resource utilization, reduced waiting time, minimized response time, scalability, support for long-running transactions, multi-core utilization.

### Schedules
A **schedule** orders the operations of concurrent transactions, preserving each transaction's internal order.

**Conflicts:** WR (reading uncommitted — dirty read), RW (unrepeatable read), WW (overwriting uncommitted — lost update).

- **Equivalent schedules** — same effect on the DB for any starting state.
- **Serializable schedule** — equivalent to some **serial** execution.
- **Conflict serializable** — can be transformed into a serial schedule by swapping **non-conflicting** operations.

**Precedence graph** — nodes = transactions, directed edges = conflicts. The schedule is **conflict serializable iff the graph is acyclic**.

### Recovery-Related Schedule Classes
- **Recoverable** — if $T_j$ reads data written by $T_i$, then $T_i$ commits **before** $T_j$. Otherwise a rollback of $T_i$ after $T_j$ commits is impossible.
- **Cascading rollback** — one transaction's failure forces rollbacks of dependent transactions (wasteful).
- **Cascadeless** — transactions only read data written by **already committed** transactions (avoids cascading rollback).

### Locking Protocols (added detail)
**Two-Phase Locking (2PL)** guarantees conflict serializability:
- **Shared (S) lock** — for reads (multiple readers allowed).
- **Exclusive (X) lock** — for writes (exclusive).
- **Growing phase** — acquire locks (no releases); **shrinking phase** — release locks (no acquisitions).
- **Strict 2PL** — hold all **exclusive** locks until commit/abort → prevents cascading rollback; **Rigorous 2PL** holds all locks until commit.
- 2PL can still **deadlock** (handled by detection via a wait-for graph or prevention via timestamps — wait-die / wound-wait).

**Timestamp ordering** — each transaction gets a timestamp; operations are ordered by timestamp; out-of-order conflicting operations cause rollback. Deadlock-free but can cause starvation.

**MVCC (Multi-Version Concurrency Control)** — writers create **new versions** instead of overwriting; readers see a consistent **snapshot** and never block writers (and vice versa). **Snapshot isolation**: each transaction reads the latest committed version as of its start. **PostgreSQL** uses MVCC (old row versions cleaned by `VACUUM`); write-write conflicts are detected at commit.

---

## 13. Indexing

An **index** is an auxiliary data structure (tree or hash) mapping key values → physical row locations, enabling fast retrieval. Without it, a query must scan every data block (full table scan).

### How It Works
The index stores **key values** + **pointers** (disk addresses). A `WHERE` on an indexed column searches the sorted index (binary-search-like), then follows the pointer directly to the row.

### Classification

**By uniqueness/role:**
| Type | Description |
|---|---|
| **Primary** | Auto-created on the primary key; unique & not null; usually clustered; one per table |
| **Unique** | Every value unique; enforces entity integrity |
| **Secondary** | On non-PK columns; many per table; optimizes common filters |

**By storage organization:**
| Type | Description | Pros | Cons |
|---|---|---|---|
| **Clustered** | Defines the **physical sort order** of rows; *the data is the index* | Very fast range queries (contiguous) | Only **one** per table |
| **Non-Clustered** | Separate structure with keys + pointers; data not sorted by it | Many per table; fast point lookups | Slower range scans (random jumps) |

**By structure:**
| Type | Best For | Worst For |
|---|---|---|
| **B-Tree / B+ Tree** | Everything — range queries, sorting, partial matches | — |
| **Hash** | Exact-match equality, O(1) | Range queries, sorting, prefix matches |
| **Bitmap** | Low-cardinality columns; AND/OR in data warehouses | High-cardinality columns |
| **Composite** | Queries filtering on the columns or a **left prefix** (an index on (A,B,C) serves A, or A+B, or A+B+C) | Filtering only on a non-prefix (e.g., only B) |

**Dense vs. sparse:** a **dense** index has an entry per record; a **sparse** index has entries for only some records (points to blocks; common). **Multi-level indexing** builds a hierarchy when the index is too large for memory.

### B-Tree vs. B+ Tree (added detail)
- **B-Tree** — keys and data pointers in **all** nodes (internal and leaf).
- **B+ Tree** — keys in internal nodes for routing; **all data pointers in the leaves**, and leaves are **linked** in a sorted chain. This makes **range queries** and ordered scans efficient (follow the leaf links) and keeps internal nodes small (higher fan-out, shallower tree). B+ trees are the standard for relational indexes.

### Trade-offs
- **Advantages:** faster queries, efficient sorting, faster joins, fewer I/O operations.
- **Disadvantages:** extra storage, slower writes (index maintenance on every insert/update/delete), maintenance overhead, need to choose the right index.

---

## 14. NoSQL and CAP (added detail)

### SQL vs. NoSQL
| | SQL (Relational) | NoSQL |
|---|---|---|
| Schema | Fixed, predefined | Flexible/dynamic |
| Scaling | Vertical (mostly) | Horizontal (sharding) |
| Consistency | Strong (ACID) | Often eventual (BASE) |
| Queries | Powerful joins, ad-hoc | Simple access patterns |
| Best for | Complex relationships, transactions | Massive scale, simple/flexible data |

### NoSQL Types
- **Key-value** (Redis, DynamoDB) — O(1) by key; sessions, caching, leaderboards.
- **Document** (MongoDB, CouchDB) — JSON-like documents; catalogs, profiles.
- **Wide-column / column-family** (Cassandra, HBase) — row-key + sorted columns; write-heavy, time-series.
- **Graph** (Neo4j, Neptune) — nodes/edges; social networks, fraud detection.

### CAP Theorem
In a distributed system you can only guarantee **two** of:
- **Consistency** — every read sees the most recent write (or an error).
- **Availability** — every request gets a (non-error) response.
- **Partition tolerance** — the system keeps working despite network partitions.

Since networks fail, **P is mandatory** → choose **CP** (MongoDB, HBase, Zookeeper — may reject during a partition) or **AP** (Cassandra, DynamoDB — always respond, eventual consistency).

### BASE vs. ACID
**BASE** = **B**asically **A**vailable, **S**oft state, **E**ventual consistency — the NoSQL philosophy favoring availability and partition tolerance over strict consistency, in contrast to ACID's strict guarantees.

---

## 15. Quick Interview Checklist

- **Super key ⊇ candidate key ⊇ primary key**; candidate = minimal super key.
- **Closure $X^+$** decides keys and FD redundancy.
- **3NF** is lossless + dependency-preserving; **BCNF** is stricter but may lose dependencies.
- **Lossless decomposition:** common attribute must be a super key of one piece.
- **Conflict serializable ⇔ acyclic precedence graph.**
- **Strict 2PL** prevents cascading rollback; 2PL can deadlock.
- **MVCC** = readers don't block writers (Postgres snapshot isolation).
- **Clustered index** = physical order (one per table); **B+ tree** links leaves for range scans.
- **CAP:** partition tolerance is non-negotiable → trade C vs. A.
