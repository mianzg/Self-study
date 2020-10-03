# Architecture of a Database System


## 1. Introduction
## 2. Process Models
## 3. Parallel Architecture: Processes and Memory Coordination
## 4. Relational Query Processor
1. Query Parsing and Authorization
2. Query Rewrite

- A *relational query processor* takes a declarative SQL statement, validates it, optimizes it into a procedural dataflow execution plan, and executes that data flow program on behalf of a client program
- A single-user, single-threaded task 
- Concurrency control involvement: (un)pin buffer pool pages (4.4.5?)
- DML: SELECT, INSERT UPDATE, DELETE, yet ~DDL: CREATE TABLE, CREATE INDEX~ (storage engine/catalog manager)

### 4.1 Query Parsing and Authorization
1. **Check** that the query is correctly specified
2. **Resolve** names and references
    - **Canonicalize** table names into a fully qualified *four-part name*: server.database.schema.table <- context-dependent defaults 
    - Catalog Manager: table registered in sys catalog, cache metadata in internal query  data structures -> **attribute ref correct** (i.e data types of attr)
3. **Convert** the query into the internal format
4. **Verify** that the user is authorized to execute the query
    - e.g row-level ~full security checking~ <- data-value dependent
    - Defer to exe time: shared btw users, not require recompilation when security changes
        - constraint-check (e.g integrity check SET EMP.salary=-1)
        
### 4.2 Query Rewrite
1. Query Rewriter: Simplify and normalize the (internal repr) query without changing its semantics
    - Rely on query and metadata
    - No access to data in the tables
    - Logical component in sys
2. Responsibilities:
    1. View Expansion (traditional; FROM clause)
    ``` 
    while exists Views:
        V := current view
        retrieve view definition from catalog manager
        replace V with tables and preds
        replace any references to V with column ref to tables in V
    ```
    2. Constant arithmetic evaluation
    3. Logical rewriting of predicates (WHERE clause)
        - short-circuit
        - Plausibility of unsatisfiable queries & e.g partition elimination in parallel installations
        - transitivity to add new predicates
    4. Semantic optimization
        - e.g redundant join elimination
    5. Subquery flattening and other heuristic rewrites
        - flatten nested queries -> max expose opportunities for optimizer's **single-block** optimizations
        - crucial for parallel architecture
### 4.3 Query Optimizer
Transform an internal query representation into an **efficient _query plan_** (System R optimizer)

Extensions to Selinger
- Plan space
- Selectivity estimation
- Search algorithms
- Parallelism
- Auto-Tuning

### 4.4 Query Executor
### 4.5 Access Methods
### 4.6 Data Warehouses
### 4.7 Database Extensibility
###  4.8 Standard Practice
