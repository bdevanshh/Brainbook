07-06-2026  15:36

Status:

Tags: [[DDIA]]

# Chapter 4 - Storage and Retrieval

### Index
A data structure used to store the data in a particular way that makes it faster to locate for reads. But, the index incur a cost of slower writes.

### Sparse Index
An index which stores only partial data is called Sparse Index.

### Memtable
Memtable is an in-memory data structure used to insert keys in any order, look them up efficiently, and read them back in sorted order.
e.g., red-black tree (sorted map)

### Tombstone
Tombstone is a special deletion record that is used to delete a key and its associated value.

### LSM Storage Engines
Storage engines that are based on the principle of merging and compacting sorted files are often called Log-Structured Merge-tree(LSM) storage engines.

### Bloom Filters
Bloom Filters provides fast but approximate way of checking whether a particular item appears in a particular structure.

### Size-tiered Compaction
Newer and smaller SSTables are merged in to older and larger SSTables.
- Older SSTables get very large, and merging requires a lot of disk space.
- Handle very high write throughput.

### Leveled Compaction
This compaction used fixed sized SSTables and group them into increasing levels like L0, L1.
- Uses less disk space.
- Efficient for reads.

### B-Trees
- Keep the key-value pairs sorted by key.
- Breaks the database into fixed-size block or pages. Each page has a page number.
- Overwrites a page in place.
- For resiliency, some B-Trees implementation also have WAL(Write-Ahead Log) stored on disk.
- The number of references to child pages in one page of the B-tree is called the **branching factor**.

![[Pasted image 20260609163559.png]]

### Random Writes
The pattern of many small, scattered writes is called Random Writes.

### Sequential Writes
the pattern of fewer large writes is called Sequential Writes.

### Write Amplification
The total number of bytes written to disk in a workload and dividing that
by the number of bytes you would have to write if you simply wrote an append-only log with no index, you get the Write Amplification.

### LSM-Trees vs B-Trees

| Point               | LSM-Trees                | B-Trees              |
| ------------------- | ------------------------ | -------------------- |
| Reads               | Slower                   | Faster               |
| Writes              | Faster sequential writes | Slower random writes |
| Write Amplification | Lower                    | Higher               |
| Fragmentation       | Lower                    | Higher               |

### Clustered Index
If the actual data (row, document, vertex) is stored directly within the index structure, it is called a Clustered Index.

### Covering Index
An index which stores some of a table’s columns within the index, in addition to storing the full row on the heap or in the primary-key clustered index is called Covering Index.
Queries which only requires the covered columns can be answered quickly.

### Query Engine
Query engines parse SQL queries, optimize them into execution plans, and execute them against data.

### Storage Format
The storage format determines how the rows of a table are encoded as bytes in a file.

### Table Format
Table Formats specify a file format that defines which files constitute a table along with the table's schema.

### Data Catalog
Data Catalog defines which tables are contained in a database. Catalogs are used to create, rename, and drop tables.

### Column-Oriented / Columnar Storage
Columnar storage store all the values from each column together in disk, instead of storing one row together.

![[Pasted image 20260610164552.png]]

### Query Compilation
The query engine takes the SQL query and generates code for executing it. The query engine then compiles the generated code to machine code (using an compiler like LLVM) and runs it on the column-encoded data that has been loaded into memory.

### Vectorized Processing
The query is interpreted and made fast by processing many values from a column in a batch. A set of predefined operators are built into the database.

Example,
We could pass the product_sk column and the ID of a product to an equality operator, and get back a bitmap. We could then pass the store_sk column and the ID of the store of interest to the same equality operator, and get back another bitmap. Finally, we could pass the two bitmaps to a bitwise AND operator.
![[Pasted image 20260610170837.png]]

### Materialized Aggregates
Materialized Aggregates are a type of materialized view that can be useful in data warehouses. It precomputes the aggregations (such as COUNT, SUM, MIN, MAX, AVG) results.

### OLAP Cube / Data Cube
A data cube is a type of matrialized aggregate which works by creating a grid of aggregates grouped by different dimensions.

![[Pasted image 20260610172250.png]]

### Concatenated Index
Concatenated Index is a type of multi-column index, which combines several fields into one key by appending on column to another. The index definition specifies the order of concatenation.

Example,
A concatenated index for a person's `lastname-firstname` in a phone book can be used to find all the people with a particular `lastname`, or all the people with a particular `lastname-firstname`, but the index is useless for finding all the people with a particular `firstname`.

### Multidimensional Index
Multidimensional Indexes allow us to query several columns at once.

Example,
On an e-commerce website a three-dimensional index on the dimensions `red-green-blue` to search for products in a certain range of colours.

### Full-Text Search
Full-text search allows us to search a collection of text documents (web pages, product descriptions, etc.) by keywords that might appear anywhere in the text.

### Semantic Search
Semantic search includes searching by understanding document concepts and user intentions.

### Vector Embedding
Vector Embedding are vector of floating-point values translated by an embedding model from a text document.

### Multimodal Architecture
A model architecture which can generate vector embeddings for multiple modalities, such as text and images from a single model is called Multimodal Architecture.

### Vector Indexes
#### Flat Indexes
Flat Indexes stores vectors as is. A query reads every vector and measures its distance to the query vector.
- It is are slow but, accurate.

### Inverted File (IVF) Indexes
Inverted File Indexes partition the vector space into centroids. So, the number of vector comparisons are reduced.
- It is faster than Flat Indexes, but gives approximate results.

A query on an IVF index first defines **probes** (number of partitions to check). Queries that use more probes will be more accurate but slower, as more vectors must be compared.

### Hierarchical Navigable Small World (HNSW) Indexes
HNSW Indexes maintain multiple layers of the vector space. Each layer is represented as a graph, where nodes represent vectors and edges represent proximity to nearby vectors.
- HNSW Indexes are approximate.

![[Pasted image 20260611170226.png]]





# References