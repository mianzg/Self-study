# Architecture of a Database System
<!--GFM-TOC-->
[1. Introduction](#1-introduction)
[2. Process Models](#2-process-models)
    [2.1 Uniprocessors and Lightweight Threads](#2-1-)
[3. Parallel Architecture](#3-parallel-architecture-processes-and-memory-coordination)
[4. Relational Query Processor](#4-relational-query-processor)
<!--GFM-TOC-->

## 1. Introduction
## 2. Process Models
- An OS Process combines an OS program execution unit (a thread of control) with an address space private to the process
- An OS Thread is an OS program execution unit without additional private OS context and without a private address space
    - full access to mem under multi-threaded OS Process
    - k-threads
- A Lightweight Thread Package is an application-level construct
    - scheduled in user-space
    - P: faster thread switches; C: blocking operation
- A DBMS Client is the software component that implements the API used by application programs to communicate with a DBMS
- A DBMS Worker is the thread of execution in the DBMS that does work on behalf of a DBMS Client 
 Approaches of mapping DBMS workers onto OS threads or processes
### 2.1 Uniprocessors and Lightweight Threads
1. Two Simplifying Assumptions
    1. OS Thread Support: for k-threads; small overhead; inexpensive context switches
    2. Uniprocessor Hardware: single machine with a single CPU
#### DBMS Process Model
1. Process per worker
- The OS scheduler manages the timesharing of DBMS workers
- In-mem shared data structures ([lock table]() and [buffer pool]()) -> ⬇️adv of address space separation
2. Thread per worker
3. Process pool

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

- Transform an internal query representation into an **efficient _query plan_** (System R optimizer) 
    - Query plan: A dataflow diagram that pipes table data through a graph of query operators
        - e.g System R -> machine code
        - e.g INGRES -> **intepretable query plan** -> cross-platform portability
        
1. Extensions to Selinger (Sys R)
    |          | Sys R                            | Current                                          |
    |----------|----------------------------------|--------------------------------------------------|
    |Plan Space| "left-deep" & postpone Cartesian | "bushy" trees and early use of Cartesian products|
    |Selectivity estimation| on simple table and index cardinalities| sampling techniques ("joining" the histograms on the join columns); TPC-DS |
    |Search Algorithms| DP | Goal-directed "top-down";  Heuristic search |
    |Parallelism  | |Intra-query; Hong-Stonebraker 2-phase optimization; 
    |Auto-Tunining| | Plan costs by "what-if" analyses|
    
     lower the number of plans; incr mem consumption
2. Query Compilation and Recompilation
    1. Static precompilation
        - stores execution plan for subsequent exe statements
        - useful for form-driven/canned queries over predicatble data
    2. Cache
        - dynamic query exexution plans in the query plan cache, similar to 1
    3. Recompilation/Reoptimization
        - When an index is dropped, any plan that used that index must be removed from the stored plan cache
        - Subtleties diverge but evolve due to competition for all-type customers. Predicatable performance (IBM); Self-tuning (Microsoft)
### 4.4 Query Executor
On a fully-specified query plan: 1) low-level op-codes 2)**recursion on dataflow representation** 
```java
class iterator {
    iterator &inputs[] # edges in the dataflow graph
    void init();
    tuple get_next(); # Go to next node(i.e. operator)
    void close();
    }
class operator(iterator){
    super(operator) 
}
```
Any subclass of iterator can be used as input to any other
#### Single-threaded Iterator Architecture 
**Couple dataflow with control flow**
```gen_next()``` 
- Only a single DBMS thread is needed to execute an entire query graph, and queues or rate-matching between iterators are not needed
- efficient for single-system(non-cluster) query execution to achieve performance in *time to query completion* 
- Parallelism: exchange iterator with "pull-style" ```get_next()```

#### Mem Allocation for in-flight data
1. Tuple descriptor:
    - Definition: A tuple descriptor is an **array of column references**, where each column reference is composed of a referece to a **tuple** somewhere else in memory, and a **column offset** in that tuple
    ```
    class TupleDescriptor {
        array colRef = [[Tuple1, Offset1], ..., [TupleN,OffsetN]]
    }
    ```
    - Each iterator is pre-allocated a fixed number of tuple descriptors, one for each of its inputs, and one for its output
2. Memory allocation for Tuples
Problem: Iterator has no dynamic mem allocation
Solution: support tuple descriptors that reference both
    1. BP-tuples: reside in pages in the *buffer pool*
        - incr the **pin count** of the tuple's page, and decr when descriptor is cleared
        - decr
    2. M-tuple: allocate space for a tuple on the mem heap
        - copy columns from the buffer pool and/or eval expressions in the query specification
            - always copy immediately 
        - bottleneck
    ``` python
    # TODO?
    class iterator():
        def __init__(self):
            iterator &inputs[] # edges in the dataflow graph
            TupleDescriptor self.td = BPTuples()
    void init();
    tuple get_next(); # Go to next node(i.e. operator)
    void close();
    }
    ```
#### Data Modification Statements

### 4.5 Access Methods
### 4.6 Data Warehouses
### 4.7 Database Extensibility
###  4.8 Standard Practice
