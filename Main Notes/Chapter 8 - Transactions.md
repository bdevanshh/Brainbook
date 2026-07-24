---
created: 2026-06-26-15:02
links: "[[DDIA]]"
tags:
  - DDIA
---
# Chapter 8 - Transactions

**Transactions** are a mechanism for grouping multiple read and write operations into a single unit that either succeeds completely or fails completely. They provide safety guarantees even when faults occur or when multiple clients access the same data concurrently.

## The Meaning of ACID

ACID stands for **Atomicity, Consistency, Isolation, and Durability**.
### Atomicity

Atomicity is not about concurrency (that's Isolation). Instead, it describes what happens if a fault occurs after some writes in a transaction have been processed.
- If a transaction cannot be completed (committed) due to a fault, it is **aborted** and the database must **discard or undo** any writes made so far.
- Without atomicity, partial writes leave the database in an unknown state.
- The defining feature is **abortability** - the ability to abort a transaction on error and have all writes discarded.
- If a transaction aborts, the application can safely retry, knowing nothing was changed.

### Consistency

Consistency in ACID refers to an **application-specific notion of the database being in a "good state."** This is different from replica consistency, consistent snapshots, consistent hashing, or CAP consistency.
- You have **invariants** (statements about your data) that must always be true (e.g., credits and debits must balance in accounting).
- If a transaction starts with a valid database and preserves invariants, the database remains valid after commit.
- Invariants can be enforced via **constraints** (foreign-key, uniqueness, check constraints), **triggers**, or **materialized views**.
- Complex invariants may be the application's responsibility - it often depends on how the application uses the database.

### Isolation

Isolation means that **concurrently executing transactions are isolated from each other** - they cannot step on each other's toes.
- **Serializability**: each transaction can pretend it's the only transaction running. The result is the same as if transactions ran serially (one after another).
- However, serializability has a performance cost. Many databases use **weaker isolation levels** that allow limited interference.
- Weaker isolation levels are prone to race conditions.

![[Pasted image 20260626162842.png]]

### Durability

Durability is the promise that **after a transaction commits successfully, any data written will not be forgotten**, even with hardware faults or database crashes.
- In single-node databases: data is written to **nonvolatile storage** (disk/SSD). Databases use `fsync` to ensure data is actually written.
- Most databases have a **write-ahead log (WAL)** for crash recovery.
- Many use **checksums** to detect corrupted log entries.
- In replicated databases: durability may mean data has been copied to a certain number of nodes.
- **Perfect durability does not exist** - if all disks and backups are destroyed, the data is lost.

#### Replication and Durability
No single technique provides absolute durability guarantees:
- **Disk writes**: Data is inaccessible if the machine dies until repaired.
- **Correlated faults**: Power outages or bugs can knock out all replicas at once.
- **Async replication**: Recent writes may be lost if the leader becomes unavailable.
- **SSD reliability**: 30-80% of SSDs develop bad blocks in 4 years; worn-out SSDs can lose data within weeks/months when disconnected from power.
- **Firmware bugs**: Disk firmware can have bugs (e.g., drives failing after exactly 32,768 hours).
- **Silent corruption**: Data can gradually corrupt without detection.

### Single-Object and Multi-Object Operations
#### Single-Object Operations
Single-object operations are **atomic** operations on a single item (row, document, key-value pair). They are guaranteed to succeed or fail entirely.
- **Reads**: Fetching a single row/document by its key.
- **Writes**: Inserting, updating, or deleting a single object.
> Example: `UPDATE accounts SET balance = balance - 10 WHERE id = 1;`
##### Guarantees
- **Atomicity**: The operation either completes fully or has no effect.
- **Isolation**: Other transactions see either the old value or the new value, never a partial update (no "torn writes").
##### No True Transactions
In many NoSQL databases (like MongoDB, DynamoDB), the only transactions available are single-object transactions. If an operation fails halfway (e.g., network error), the database ensures the object is not left in a corrupted state.
##### Limitations
- Lack **multi-step atomicity**: You cannot perform two updates atomically (e.g., debit from one account and credit to another).
- Lack **isolation across objects**: Concurrent reads might see inconsistent states across multiple objects.

#### Multi-Object Operations
Multi-object operations involve reading or writing multiple objects in a single unit. These are non-atomic by default unless wrapped in a transaction.
##### Why Are They Needed?
- **Foreign keys / Referential integrity**: Deleting a user should also delete their orders.
- **Materialized views / Denormalized data**: Updating a profile should also update the search index.
- **Financial ledgers**: Transferring money requires debiting one account and crediting another.
##### Without Transactions (The Problems)
- **Partial failure**: The debit succeeds, but the credit fails. Money is lost.
- **Dirty reads**: Another transaction reads the debited account but not yet the credited one.
- **Lost updates**: Concurrent overwrites cause one update to silently disappear.

## Weak Isolation Levels

> Even ACID databases use weak isolation levels. Attackers can exploit concurrency bugs by sending highly concurrent requests.

### Read Committed
**Guarantees:**
- No dirty reads (won't see uncommitted data)
- No dirty writes (won't overwrite uncommitted data)

**Details:**
- Writes become visible only after commit.
- Prevents raw anomalies but not lost updates or non-repeatable reads.

**Implementation:**
- Use MVCC or locks to control visibility and concurrency.

### Snapshot Isolation & Repeatable Read
**Motivation:**
- With weaker isolation, a transaction may see different results in later reads due to others' commits (non-repeatable reads/read skew).

**Definitions:**
- Snapshot isolation gives every transaction a consistent snapshot of the database (at transaction start/first read). Writes by others are not visible during the transaction.
- Repeatable read aims for the same effect. MVCC commonly used.

**Prevents:**
- Dirty reads and non-repeatable reads.

**Does NOT prevent:**
- Lost updates
- Write skew (consistent but constraints may be violated)
- Phantoms

### Phenomena under Weak Isolation
- **Dirty Read:** Read uncommitted writes (prevented by Read Committed)
- **Lost Update:** Two transactions overwrite each other (not prevented unless atomic increment or SELECT...FOR UPDATE used)
- **Non-Repeatable Read:** Data changes between two reads in a transaction (prevented by Snapshot Isolation)
- **Write Skew/Phantom:** Transactions independently update/interact with overlapping data, violating a constraint; also, new rows appear/reappear ("phantoms") in repeat SELECTS

### Practical Approaches
- Use atomic update primitives (INCREMENT/ADD) for counters and accumulations.
- Use SELECT...FOR UPDATE for locking (can reduce concurrency).
- Use CAS (Compare-And-Swap) when supported.
- True serializability is safest but can impact performance.

## Serializability

Read-committed and snapshot isolation doesn't prevent some race conditions like write skew and phantoms. Serializable isolation prevents *all* possible race conditions. It is the **strongest** isolation level and generates results as if the transactions were executed *serially*. There three possible techniques to implement serializability:
- Literally executing serially,
- Two-phase locking,
- Optimistic concurrency control techniques.

### Actual Serial Execution

The simplest approach is to eliminate concurrency itself by executing transactions on a single thread. This approach has become somewhat feasible because:
- RAM became cheap enough to keep the active dataset in memory.
- OLTP transactions are short and required only a small number of reads and writes, so it can be executed very quickly on single thread.

However, the throughput of writes are limited to that of a single CPU.

#### Stored Procedures

A transaction may be performing a very complex activity like, booking an airline ticket, which required searching for routes, fares, and available seats, booking a seat, making a payment. If a transaction needs to wait for a input from the user during this process, single threaded approach is not worth it because most of the transaction might be sitting idle in the database till a user make a choice, this use case requires concurrency.

For that reason, single-threaded serial transaction processing is appropriate with small single statement transactions or *stored procedures*.

![[Pasted image 20260702174507.png]]

##### Cons:
- Each database vendor had its own language for stored procedures which are archaic from today's point of view.
- Running code in database is difficult to manage.
- A badly designed procedure might affect multiple application servers.

##### Pros:
- Modern implementations have moved to general-purpose programming languages like Lua and Javascript.
- It is useful for embedding application logic that can't be embedded elsewhere.
- With stored procedures and in-memory data, transactions on single-thread becomes feasible.

#### Sharding

The bottleneck in this approach is the single CPU core limiting the database transaction throughput. To scale to multiple CPU cores, we can shard data in a way so that each transaction needs to write and read data only withing a single shard, then each shard can have its own transaction processing thread. This way, each CPU core have its own shard and transaction throughput scales linearly with CPU cores.

For transactions that needs to access multiple shards, the database must coordinate the transaction across all the shards, which is slower than single-shard transactions and incurs coordination overhead.

#### Summary

- Every transaction must be small & fast, because a slow one will stall all the transaction processing.
- It is appropriate if the active dataset can fit into memory.
- Write throughput is low enough to be handled on a single CPU core, or else transactions needs to be sharded.

### Two-Phase Locking

2PL uses stronger lock requirements to prevent two transactions concurrently accessing the same objects.
- If transaction A has read an object and transaction B wants to write to that object, B must wait until A commits or aborts. (Provides repeatable reads)
- If transaction A has written an object and transaction B wants to read that object, B must wait until A commits or aborts. (Prevents *dirty reads*)

So, this way in 2PL writers block other writers as well as readers and vice versa.

#### Implementation of 2PL

The blocking of readers and writes is implemented using locks on each objects in the database. There are two type of locks: *shared mode locks* and *exclusive mode locks*. It is used as follows:
- To read an object, a transaction must acquire a shared lock on the object. Several transaction can hold shared locks, but if a transaction have a exclusive lock, these must wait.
- To write to an object, a transaction must acquire exclusive lock. No other transactions may hold locks at that time.
- A transaction that reads and then writes must upgrade its lock to exclusive mode.
- A lock is held till the transaction commits or aborts. The first phase is when locks are acquired and the second phase is where locks are released, so does the name "two phase".

#### Cons
- The overhead of acquiring & releasing locks incurs significant overheads.
- Reduces concurrency. E.g., While the whole table is being read, all the other write transactions are blocked.
- Frequently occurring deadlocks causes transactions to be retried.

#### Predicate Locks

A predicate lock belongs to all objects that match a search conditions rather than belonging to a particular object. It also applies to objects that do not yet exists, so it is useful in case of *phantoms* (one transaction changing the search query result of other transaction).

A predicate lock works as follows:
- If transaction A wants to read objects matching a condition, it must acquire a shared predicate lock on the conditions of the query. But if another transaction B currently has an exclusive lock on any object matching those conditions, A must wait until B releases its lock.
- If transaction A wants to modify any object, it must first check whether either the old or the new value matches any existing predicate lock. If a matching lock is held by transaction B, then A must wait until B has committed or aborted.

#### Index-range Locking

Predicate locks do not perform well if there are many locks by a transaction, for that reason most databases with 2PL implement *index-range locking* (or *next-key locking*). It the simplified approximation of predicate locking.

An approximation of search condition that fits greater set of objects than original predicate is attached to an index.

For Example,
A shared lock is attached to an index entry of `room_id` in booking management system, indicating a search for booking of room. When another transaction want to modify a booking for the same room, it will encounter shared lock, and it will wait until the lock is released.

This provides protection against phantoms and write skew, but locks a bigger range of objects than necessary to maintain serializability.

### Serializable Snapshot Isolation

Serializable Snapshot Isolation (SSI) algorithm provides full serializability with only a small performance downside compared to snapshot isolation.

2PL is a *pessimistic concurrency* control mechanism, meaning if something might possibly go wrong, it's better to wait until the situation is safe again before doing anything (like *mutual exclusion* in multithreaded programming).

Serial execution is pessimistic to extreme, because only one transaction can be processed at a time, it holds the exclusive lock to entire database, which is compensated by making transaction execution faster.

SSI is *optimistic concurrency* control technique, meaning that instead of blocking a transaction if something might go wrong, it executes it anyway. When the transaction want to commit, the database checks for the isolation violation and aborts it if necessary.

This performs bad in high contention, because more transactions needs to be aborted and retried, leading to bad performance. Contention can be reduced if the transactions are commutative (can be executed in any order).

SSI is based on snapshot isolation with added algorithm for detecting serializability conflicts among reads and writes.

#### Outdated Premise

In write skew, a transaction reads data from the database, examines the result, and takes an action based on the result. However, the original result from the search query may no longer be up-to-date if another transaction modified the data in the meantime and weaker isolation is used.

So, a transaction takes an action based on a *premise* (a fact that was true at the beginning of the transaction). Later, when it wants to commit, the premise may no longer be true. So, there is a causal dependency between the queries and the writes in the transaction. Serializable isolation must detect situations in which a transaction may have acted on an outdated premise and abort it. How to decide if a query result might have changed?
- Detecting read of a stale MVCC object version.
- Detecting writes that affect prior reads.

#### Detection of Stale MVCC reads

In MVCC a transaction reads from a consistent snapshot of a database, it ignores writes that were made by any other uncommitted transactions when the snapshot was taken. This may allow a transaction to read a data that may be changed before it is committed.

![[Pasted image 20260704174301.png]]

To prevent this the database must track ignored writes and check whether any ignored write have now been committed before committing the original transaction.

#### Detection of writes that affect prior reads

A transaction might modify the data **after** another transaction has read it (not like a uncommitted transaction). 2PL solved this using index-range locks, that is locking all the object that match a search query. SSI also uses the same technique, but doesn't block other transactions.

![[Pasted image 20260704175401.png]]

The database keeps the record of the transactions that read a particular data in the corresponding index (`shift_id` in the example). When a transaction want to write, it must look in the index for any other transactions that have already read the affected data and notify them that their read may no longer be up-to-date.

In the example, transaction 42 notifies transaction 43 that its prior read is outdated and vice versa. Transaction 42 is first to commit and it is successful, when transaction 43 wants to commit, it will be aborted because of the conflicting write from 42 has already been committed.

#### Performance

- If the database keeps track of each transaction's reads and write activity in great detail, the bookkeeping overhead can become significant. Less tracking is faster, but it may abort transactions unnecessarily.
- One transaction doesn't block other transactions as in 2PL. As with snapshot isolation, writers don't block readers, and vice versa. Also long read-only queries can run on a consistent snapshot without requiring any locks.
- Transaction throughput can be scaled to multiple CPU cores, unlike serial execution.
- A transaction that reads and writes data over long period of time is likely to be retried. So, SSI requires read/write transactions to be short, but long read-only transactions are fine.

## Distributed Transactions

When there are multiple nodes involved in a transaction like,
- A transaction touching multiple shards from a sharded database,
- A global secondary index.
It is called a *Distributed Transaction*.

Concurrency control in distributed transaction are similar to those of single-node concurrency control. But, achieving atomicity is challenging.

In single node transactions, transactions commitment is done by first making the transaction writes durable and then appending a commit record to the log on the disk. Before the disk finish writing the commit records, it is still possible to abort the transaction (because of a crash).

In a distributed transaction, simply sending a commit request to all nodes is not good, because some nodes may commit and other may not because of,
- A node detecting a constraint violation or conflict.
- The commit requests might be lost in the network, eventually aborting because of a timeout.
- Some nodes may crash before the commit record is fully written and roll back the transaction on recovery.

![[Pasted image 20260705170226.png]]

This makes some nodes inconsistent with others. Instead, a better approach is to ensure that the nodes involved in the transaction either all commits or aborts. This is known as *Atomic Commitment* Problem.

### Two-Phase Commit

*Two-Phase Commit* is the algorithm for achieving atomic transaction commit across multiple nodes.

![[Pasted image 20260705171033.png]]

2PC uses *coordinator*(or a *transaction manager*) for managing the transaction across multiple database nodes called *participants* in the transaction. When an application want to commit, the coordinator begin the phase 1 by sending *prepare* request to each of the nodes.
- If all participants reply yes, the coordinator sends out a *commit* request in phase 2, and the commit takes place.
- If any participant replies no, the coordinator sends *abort* request to all nodes in phase 2.

Below is the complete break down of the process in more detail:
1. When the application wants to begin a distributed transaction, it requests a globally unique transaction ID from the coordinator.
2. The application begins a single-node transaction on each of the participants with the globally unique transaction ID. All reads and writes are done in one of these single-node transactions.
3. When the application is ready to commit, the coordinator sends a prepare request to all participants with the global transaction ID. If any of these requests fails, the coordinator sends an abort request for that transaction ID to all participants.
4. When a participant receives the prepare request, it makes sure that it can definitely commit the transaction under all circumstances (a crash, constraint violations). The participant surrenders the right to abort the transaction by replying yes, but without actually committing it.
5. When the coordinator has received responses from all prepare requests, it makes a decision to commit or abort the transaction. It also writes that decision to its transaction log on disk. This is called the *commit point*.
6. After the coordinator’s decision has been written to disk, the commit or abort request is sent to all participants. If this request fails, the coordinator must retry forever until it succeeds. Because if the decision was to commit, that decision must be enforced.

The participant's promise is the crucial thing in the algorithm. Because if it promises yes to the coordinator, it will definitely commit the transaction sooner or later, even after recovering from a crash.

#### Coordinator Failure

If the coordinator fails before sending the prepare requests, a participants can abort the transaction. But if the participant received a prepare request and voted yes, it can no longer abort and must wait for the coordinator's reply. A participant in this state is called *in doubt* or *uncertain*.

The participant can't abort of commit meanwhile because, others may not have committed or aborted. The only way 2PC can complete is by waiting for the coordinator to recover. That is why the coordinator logs its decision in transaction log and aborts all in-doubt transactions by reading the log when recovered.

If the coordinator's disk fails and its log its lost, the system can not be recovered. The only option is for an administrator to manually commit or abort the in-doubt transactions.

#### Three-phase commit

2PC is called *blocking* atomic commit protocol because it can become stuck waiting for the coordinator to recover. An alternative to 2PC is *three-phase commit* algorithm, which assumes a network with bounded delays and nodes with bounded response times; in most systems this is not true.

### Distributed Transactions Across Different Systems

There are two types of distributed transactions:

- *Database-internal distributed transactions* : Some distributed database supports internal transaction across nodes of the database. All the nodes participating in the transaction are running the same database software.

- *Heterogeneous distributed transactions* : In a heterogeneous transaction, the participants are two or more technologies, for example, two database from different vendors, message brokers. All the participants must ensure atomic commit.

#### Exactly-once message processing

Heterogeneous distributed transaction allows diverse systems to be a participant in a transaction. For example, a message from a message queue can be acknowledged when the database transaction is successfully committed. If either the message delivery or the database transaction fails, both are aborted. Thus, by atomically committing the message and the side effects of its processing (like sending email), we can ensure that the message is processed exactly once. This is known as *exactly-once semantics*.

This type of distributed transaction is possible only if all systems participating in the transaction uses the same atomic commit protocol. However, if a side effect of processing a message like sending an email doesn't support 2PC, the email might be sent multiple times.

#### XA Transactions

XA (eXtended Architecture) is a standard for implementing 2PC across heterogeneous technologies. It is a C API for interfacing with a transaction coordinator, and not a network protocol. It also assumes that the application uses a network driver or client library to communicate with the participant databases or messaging services. The XA supporting driver exposes callbacks through which the coordinator can ask the participant to prepare, commit or abort.

The transaction coordinator implements the XA API and in practice, it is loaded into the same process as the application issuing the transaction (not separate process). So, if the application process crashes, the coordinator also crashes. Since the coordinator's log is on the application server's local disk, the server must be restarted.

#### Holding locks while in doubt

Why can't the rest of the system operate normally while some transactions are being stuck in doubt?

The problem is with *locking*. Database transaction acquires locks on any rows they modify, to prevent dirty writes as discussed in [[#Read Committed]] and [[#Two-Phase Locking]]. The database cannot release those locks until the transaction commits or aborts.

So, when the coordinator crashes, all the in-doubt transactions holds the locks for the time being. While the locks are held no other transaction can modify these rows and the application might become unavailable until in-doubt transaction is resolved.

#### Recovering from coordinator failure

If the coordinator crashes and is restarted, it should recover its state from the log and resolve any in-doubt transactions. However, *orphaned* in-doubt transactions might occur, meaning the transactions for which the coordinator can't decide the outcome (because of corrupted log).

The only way out is for an administrator to manually decide whether to commit or abort the transactions. Some XA implementations have an emergency escape hatch called *heuristic decisions*, which allows the participant to decide to abort or commit, which breaks the atomicity, but used only for getting out of catastrophic situations.

##### Problems with XA Transactions

A single-node coordinator is a single point of failure for the system, and also the log on it's local disk becomes a crucial part of the durable system state.

The coordinator of an XA transaction could be highly available and replicated, but this would require totally redesigning the application code to make it replicated/restartable because, the coordinator and the participant can only communicate via the application code that invoked the transaction.

Another problem is that since XA needs to be compatible with a wide range of data system, it cannot enforce standardized implementations (like detecting a deadlocks across different system would require a standardized protocol to exchange information on the locks). It also does not work with [[#Serializable Snapshot Isolation]], because that would require a protocol for identifying conflicts across different systems.

### Database-Internal Distributed Transactions

Database-internal  distributed transactions are internal to a system and all the nodes participating in the transaction are running the same database software.

Many uses 2PC to ensure atomicity of transactions across shards, but they don't have same problems like XA transactions. Because the participants are running the same database software, the designer can use any protocols.

Consensus algorithms are commonly used to replicate the coordinator and the database shards, which can tolerate faults by automatically failing over from one node to another, while providing strong consistency guarantees.

### Exactly-Once Message Processing

Exactly-Once message processing in database with transaction can work like this:
1. Every message has a unique ID, and the database has a table of message IDs that have been processed. When broker sends a message, a new transaction on the database is created, which checks if the message ID is already present in the database, if it is you can acknowledge the message to the broker and drop it.
2. If the message ID is not present, you add it to the table and then process the message. When the message is processed, you can commit the transaction.
3. Once the database transaction is successfully committed, you can acknowledge the message to the broker.
4. Once the message has been acknowledged to the broker, you can delete the message ID from the database (knowing that the broker won’t try processing the same message again).

If the message processor crashes,
- before committing the transaction, the message broker will retry processing.
- after committing but before acknowledging the message to the broker, it will retry, but the retry will see the message ID in the database and drop it.
- after acknowledging the message but before deleting the message ID from the database, the message ID will take up some storage space.

Also, uniqueness constraints on the table of message ID will prevent the same message ID from being inserted by two concurrent transactions.

## Summary

| Isolation Level      | Dirt Reads | Read Skew | Phantom Reads | Lost Updates | Write Skew |
| -------------------- | ---------- | --------- | ------------- | ------------ | ---------- |
| <br>Read Uncommitted | ❌          | ❌         | ❌             | ❌            | ❌          |
| Read Committed       | ✅          | ❌         | ❌             | ❌            | ❌          |
| Snapshot Isolation   | ✅          | ✅         | ﹖             | ❌            | ❌          |
| Serializable         | ✅          | ✅         | ✅             | ✅            | ✅          |