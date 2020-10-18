Data Page Layouts for Relational Databases on Deep Memory Hierarchies
Motivation: bottleneck at I/O -> CPU & MEM (larger mem; IO hiding; complex comp on mem data) : Data requests that miss in the **cache** hierarchy
DSM partitions an n-attr relation vertically into n subrelations
1. max inter-record spatial locality
    - groups a "column"
2. incurs a minimal record reconstruction cost
    - no need to **join** attr values of a particular record
3. orthogonal to other design decisions

F-minipage: presence bit
V-minipage

sotres one offset for each Var-length value, plus one for each of the n mini-pages

In workloads where IO latency dominates execution time, the performance of PAX eventually converges to the performance of NSM.
A Hybrid Page Layout Integrating PAX and NSM

## N-ary Storage Model(NSM)

## Partition Attributes Across (PAX)

To store a relation with degree n, PAX partitions each page into n **minipages** and contains a **page header**. *Fixed*-length attribute and *variable*-length attribute values are stored respectively in **F-minipages** and **V-minipages.** The page header has the number of attrbutes, the attribute sizes, offsets to the beginning of the minipages, the current number of records and the total space available.

Variable-size field requires either a presence bit(null bit) or an additional size field. An entire mini-page must be **compacted** immediately when a valid variable-size value is replaced by a null.

- reduction of cache faults and exe stalls in large scans
- 

## Hybrid Page Layout (HPL)

### Simple: concept, not implementation

An allocation space for fixed-size fields grows from one end of the page, and an allocation space for variable-size fields grows from the other end. 

v-size field: offsets and size -> f-size fields

f-size field: #records/segment = # bytes/cache line, #cacheline/f-size field = #bytes/field value

! single pool of free space

! v-size fields are all allocated from the same pool of free space

Efficiency: unit of growth = segment -> fragmentation loss

### Rigid: new functionality with increasing segment size

#### Read-ahead of cache lines

Employ a **block** of cache lines

#### Poor man's normalized keys

Turn the first few bytes of a normalized key into a fixed-size field

#### Ghost records

**bits** and bitmaps flag for entire ghost("pseudo-del") records which are physically present but logically not valid -> simplify transaction rollback

Segment on bits, cacheline capacity = # ghost bits = # records -> each segment has #ghost bits/8 + #records\* #fields bytes

#### Bit fields for Null

presence bitmaps as additional fixed-size fields

### Incremental

Additional addresses calculations

Partially full segments require substantially less space


B-Tree
Data Structures and Algo
Transactional Tech
Query Processing
Utility Operations
Advanced Key Structures

Allocate space in units of equal size independent of th field size

 - The smallest fixed-size field size determines the record count per segment
 - The largest determines the precision with which the record count per page can be adjusted
