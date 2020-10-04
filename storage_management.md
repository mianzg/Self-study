<!-- GFM-TOC -->
* [The Snowflake Elastic Data Warehouse](#The-Snowflake-Elastic-Data-Warehouse)
     - 

<!-- GFM-TOC -->
## An Evaluation of Buffer Management Strategies for Relational Database Systems
### Buffer Management
1. Motivation: 1) buffer management has been ignored; 2)  hot-set model is inconclusive with limited simulation results

2. Previous Research Focus: Double paging problem. Now: buffer management policies that know how to exploit  the  predictability of database reference behavior
#### Domain Separation Algorithm ("Library")
Pages are classified into **types**, each of which is separately managed in its **associated domain** of buffers. Buffers inside each domain are managed by the **LRU** dicipline.

Simple typle assignment scheme

Limitation:
1. Domain is *static* unable to reflect the different query importance of a page
2. *Not differentiate*  the relative importance btw different types of pages (e.g. index page)
3. Memory partition according to domains
4. A separate mechanism to prevent (thrashing?)[] for load control

Extensions:
1. GLRU: a fixed priority ranking among different groups(domains)
2. Effelsberg-Haerder: Vary dynamically the size of each domain using a working-set-like partitioning scheme

#### "New" Algorithm
Tracks the locality of a query **throught relations**. Buffer pool is subdivided and allocated on a **per-relation basis**

Limitation:
1. Solely on intuition
2. Expensive searching under high mem contention
3. Unclear adoption to multiuser environment
#### Hot Set Algorithm
- A query **behavior model** for RDB sys that integrates advance knowledge on reference patterns into the model.
     - Hot Set: A set of *pages* over which there is a **looping behavior**
     - Hot Point: A discontinuity in the curve of *buffer size* with *page faults* resulted from insufficient allocated memory to hold a hot set 
          - e.g sequential scan: #pages in the inner relation plus one
- Each query is provided with a *separate* list of buffers managed by an LRU discipline. A query is given a local buffer pool of sized equal to its hot set size
     - A new query is allowed to enter if its hot set size doesn't exceed the available buffer space. 
     
Limitation (resulted from LRU)
1. Overallocate memory 
2. Unnecessary with high overhead

### DBMIN Algorithm
#### Query Locality Set Model (QLSM)
**Page reference** patterns classification by *common access methods* and *database operations*
1. Sequential References: In a sequential scan, pages are referenced and processed one after another.
     1. Straight Sequential (SS): A sequential scan is mostly done only once without repetition. *A single page frame* provides all the buffer space that is required. (e.g selection operation on an unordered replation)
     2. Clustered Sequential (CS): A scan may back up a short distance and then start forward again. (e.g merge join)
          - Records in a cluster should be kept in memory at the same time
     3. Looping Sequential (LS): Repeated (e.g. nested loops join)
          - Entire file kept in mem; too large -> MRU replacement
2. Random Reference
     1. Independent Random (IR): A series of independent access. (e.g. index scan via a nonclustered index)
     2. Clustered Random (CR): A locality of reference exists in a series of "random" access
3. Hierarchical Reference: A sequence of page accesses that form a *traversal path* from the root down to the leaves of an index.
     1. Straight Hierarchical (SH)
          - H/SS; H/CS
     2. Looping Hierarchical
          - Closer to the root are more like ly to be accessed
          - access proba inv. prop ith power of the fan-out factor
  

#### DBMIN Algorithm (based on QLSM)
Buffers are allocated and managed on a **per file instance** basis.
- Locality Set: The set of buffered pages associated with a file instance
     - separately managed by a  discipline 
     - A buffer is placed on a *global free list* if it has a page that does not belong to any locality set
- All the buffers  in memory are accessible through a *global buffer table*
Here's pseudocode (for personal understanding)
     ```python
     """
     N = TOT_NUM_BUFFERS
     FILE.l <=> l_ij = MAX_BUFFERS_FILEJ_OF_QUERYI # desired size
     FILE.r <=> r_ij = NUM_BUFFERS_FILEJ_OF_QUERYI  # actual size
     """
     FILE = open(file)
     BM = BufferManager()
     QUERY = get_query()
     
     #initialization
     global_free_list = []
     
     init_global_table()
     for b in buffers:
         b.link(global_free_list)
     BM.receive(FILE.locality_set_size)
     BM.receive(FILE.replacement_policy)
     FILE.init_empty_locality_set()
     FILE.l, FILE.r = FILE.locality_set_size, 0
     
     # Search for page
     page = QUERY.request_page()
     page.search_global_table()
     page.adjust_locality_set()
   
     if page.is_global_table and page.is_local_set:
         update_usage_stat(FILE.replacement_policy)
     elif page.is_global_table? #TODO: same as in mem?
         if page.has_owner:
             QUERY.get_page(page)
         else:
             add_to_locality(page, FILE.locality_set)
             FILE.r += 1
             if FILE.r > FILE.l:
                 free_page = FILE.release_page(FILE.replacement_policy)
                 release_to_global(free_page, global_free_list)
                 FILE.r = FILE.l
                 update_usage_stat(FILE.replacement_policy)
     elif not page.is_global_table:
         free_buffer = allocate_buffer(global_free_list)
         schedule_disk_read(page, from=disk, to=free_buffer)
         
         # Repeat 2nd scenario
         if page.has_owner:
             QUERY.get_page(page)
         else:
             add_to_locality(page, FILE.locality_set)
             FILE.r += 1
             if FILE.r > FILE.l:
                 free_page = FILE.release_page(FILE.replacement_policy)
                 release_to_global(free_page, global_free_list)
                 FILE.r = FILE.l
                 update_usage_stat(FILE.replacement_policy)
     ```
- Local Replacement policies do not cause actual swapping of pages.
1. SR
     1. Straight Sequential (SS)
     2. Clustered Sequential (CS)
     3. Looping Sequential (LS)
2. RR
     1.  Independent Random (IR)
     2.  Clustered Random (CR)
3. HR
     1. Straight Hierarchical(SH)
     2. Looping Hierarchical (LH)

### Evaluation and Comparison
1. Methodology
     1. 
     2. Configuration Model
     3. Statistical Validity
     
2. Algorithms

3. Results
Performance under a workload of a mix of query types

## The Snowflake Elastic Data Warehouse
### Overview
#### Future Improvement
1. Data access performance
2. Core query processing functionality
3. Skew Handling and load balancing strategies
4. Simplify workload management
5. Integration with external systems
#### Related Work
### Design Choice: Storage v.s. compute
- Shared-nothing architectures 
    - P: Scalibilityand commodity hardware
    - C: *Tightly couples compute resources and storage resources*
    - Hete
### Architecture
#### Data Storage
#### Virtural Warehouses
#### Cloud Services
### Feature Highlights
