## 4. Relational Query Processor
1. Query Parsing and Authorization
2. 
- A *relational query processor* takes a declarative SQL statement, validates it, optimizes it into a procedural dataflow execution plan, and executes that data flow program on behalf of a client program
- A single-user, single-threaded task 
- Concurrency control involvement: (un)pin buffer pool pages (4.4.5?)
- DML: SELECT, INSERT UPDATE, DELETE, yet ~DDL: CREATE TABLE, CREATE INDEX~ (storage engine/catalog manager)

### 4.1 Query Parsing and Authorization
1. SQL Parser Task
  1. **Check** that the query is correctly specified
  2. **Resolve** names and references
    - **Canonicalize** table names into a fully qualified *four-part name*: server.database.schema.table
  3. **Convert** the query into the internal format
  4. **Verify** that the user is authorized to execute the query
 
### 4.2 
