---
created: 2026-06-16-15:23
links: "[[DDIA]]"
tags:
  - "#DDIA"
---
# Chapter 6 - Replication

**Replication** means keeping a copy of the same data on multiple machines that are connected via a network.

Replications improves,
- Read throughput by allowing multiple machines to server read requests,
- Increase availability & durability,
- Reduce latency times by keeping data geographically close to the users.

Data that does not change can be replicated easily by copying it on nodes. The difficulties in replication arises when the data changes.

Algorithms discussed for replicating changes in the data,
- Single-leader
- Multi-leader
- Leaderless

## Single-Leader Replication

Each node that stores a copy of the data is called a **Replica**.

Single-Leader Replication works as follows,
1. One of the replica is designated as Leader. When clients want to write to the database, they send request to the leader, which applies the change to its local storage.
2. Leader sends the data change to all of its followers (read replicas, secondaries) as a part of **replication log** or **change stream**. Followers apply the change in the same order as they were processed on the leader.
3. Reads can be processed by either the leader or follower, but writes are accepted only by the leader. 

![[Pasted image 20260616154609.png]]

### Synchronous vs Asynchronous Replication

In synchronous replication the leader blocks write, until all the followers accepts to the change.

The advantage of synchronous replication is that all the followers are guaranteed to have an up-to-date copy of the data that is consistent with the leader's.

The disadvantage is that if the synchronous follower doesn't respond, the write cannot be processed. One single node crash would cause the entire system to halt.

Instead of making all the followers synchronous, one of the followers is synchronous and the others are asynchronous. This guarantees that the up-to-date copy of the data is on at least two replicas. This configuration is called **semi-synchronous**.

In some systems majority of replicas are updated synchronous, this is called **quorum**. Majority quorum are often used in eventual consistency systems.

In fully asynchronous leader-based replication, the writes are not durable. But, the leader can continue processing writes, even if all its followers fall behind.

### Adding New Followers

We need to add new followers time to time for replacing faulty followers or for increasing replicas. And new follower will need to have consistent copy of the leader's data.

Simple file copy would result in inconsistent state, because the files are constantly changing by writes. We can lock the whole database for copying the file but, that will incur downtime. Instead we can follow this process,
1. Take a consistent snapshot of the leader's database without locking the entire database.
2. Copy the snapshot to the new follower node.
3. The follower request's the leader for all the changes that happened after the snapshot. So, the snapshot should be associated with leader's replication log.
4. After the follower *caught up* by processing all the backlog, it can continue to process data changes from the leader as they happen.

### Handling Node Outages

To handle follower or leader failures, while maintaining high availability, leader-based replication uses below techiniqes,

#### Follower Failure: Catch-up Recovery

On the local disk, each of the follower maintains a log of the data change it have received from the leader. If a follower crashes and restarted, the follower can request the leader for all the data change that occurred after the last transaction in the log.

If the database have high write throughput, the follower might have lot of changes to catch up, which will increase the load on both the follower and the leader.

Leader periodically needs to clean its replication log after all the followers have processed the change. If the follower is unavailable for long time, leader have choices:
- Retain the log until all the followers catch up (at the risk of disk space running out),
- Delete the log, and restore the follower from the backup

#### Leader Failure: Failover

When the leader fails, a new leader must be elected, client need to be reconfigured to send their writes to the new leader, and the other followers needs to start consuming data change from the new leader. This is called **Failover**.

An automatic failover might look like this:
1. Determine the leader has failed.
2. Choosing a new leader.
3. Reconfigure the system to use the new leader.

Things that might go wrong in failover:
- In asynchronous replication, the new leader may not have received data changes from the old leader. In which case, the data is discarded, although clients believed it to be durable.
- Two nodes might believe that they are the leader. This is called **split brain**. Now both of the nodes will process writes and conflicts will arise, which will corrupt the data. Safety systems are used to shut down a node if two leaders are detected.
- The timeout for deciding if the leader has failed can be tricky. Longer timeout means more time for recovery. But, shorter times might generate unnecessary failovers.

Guarding against split brain by limiting or shutting down old leaders is known as **Fencing**. Because there are no easy solution for this, most of the team perform manual failover.

The most important thing with failover is selecting the most up-to-date follower for the new leader. In synchronous & semi-synchronous replication, this would be the follower that the old leader waited before acknowledging. In asynchronous replications, we can choose a follower with the highest log sequence number.

### Implementation of Replication Log

#### Statement-based Replication
The leader logs every write request (statement) that it executes and sends that statement log to its followers. The follower executes the statement as if it was sent by a client.

This approach is also know as **state machine replication**.

Problems with statement-based replication:
- Statement calls that are non-deterministic, can produce different result on each replica.
- If the statement relies on some existing data, it must be executed in exactly the same order on each replica, or else all the replica might generate different results.
- Statements that have side-effects may results in different side effects occurring on each replica. 

#### Write-ahead Log Shipping
In B-Tree write-ahead log was needed to make it robust, every change was first written to WAL so that the tree can be restored to a consistent state after crash.

We can use the exact same log to build replica on another node, besides of writing the log to disk, the leader will also send it to its follower over the network.

Problems with write-ahead log shipping:
- The storage engine and replication are tightly coupled, because the WAL contains details of which bytes were changed in which disk blocks. If the database decides to change its storage format, it can't do that without downtime. 

#### Logical (row-based) Log
This kind of replication maintains a separate log format, which allows the replication log to be decoupled from the storage engine. It is called logical log to make it distinguish from the storage engines's physical data representation.

A logical log contains a sequence of records describing operations happened in the database at a row level:
• For an inserted row, the log contains the new values of all columns.
• For a deleted row, the log contains enough information to uniquely identify the
row that was deleted (primary key or the old values of all columns, if the table doesn't have primary key). 
• For an updated row, the log contains enough information to uniquely identify the
updated row, and the new values of all columns (or all columns whose
values have changed).

This is used by MySQL, they call it *binlog*. PostgresSQL implements logical replication by decoding the physical WAL.

The Logical Log can be kept backward compatible, allowing database upgrades with minimal downtime.

### Problems with Replication Lag

Single-leader replication requires writes to go through single leader node, but read-only queries can go to any replica. This *read-scaling* architecture, you might be tempted to increase the number of followers and distribute the traffic. But, this requires asynchronous replications, because with fully synchronous replication a single node failure can halt the entire service.

Inconsistency state that might arise in followers when asynchronous replication is used, but if you stop writing to the database and wait for a while, the followers will eventually catch up and become consistent with the leader. This effect is knows as **eventual consistency**.

The delay between a write happening on the leader and it being reflected on a follower is called **Replication Lag**.

Replication Lag introduces inconsistency which produces some problem.

#### Reading your own writes

How can we make sure that the user can see the changes after updating some data. Because with asynchronous replication, the new data may not yet have reached to replica.

![[Pasted image 20260617183541.png]]

To solve this problem we required **read-after-write consistency** or **ready-your-writes consistency**, which guarantees the user to see the updates after reloading the page, but not the other users.

We might solve this problem with these approaches:
- When reading something that the user may have modified, read it from the leader or synchronously updated follower. For this, we need to know if something might be modified or not, without querying it. E.g., the user's profile might be modified only by the owner, so read the user's profile from the leader and other profiles from the followers.
- If most of the thing are modifiable by the user, use some other criteria like the last update time. E.g,. Till one minute after the last update time read from the leader, then from the follower.
- Client can maintain a timestamp of their last write, and the client request will be only served by a replica which is up-to-date at least till that timestamp.

Problem that might arise:
- If replicas are distributed across regions, any request that needs to be served by the leader must be routed to the leader's region, which increases latency.
- For *cross-device read-after-write consistency*, we need to centralize the metadata related to timestamps or last update times.

#### Monotonic Reads

When the user reads from different replicas, one replica which have little lag will return newer data, and a second replica with greater lag will return older data. The user will see things moving backward in time.

![[Pasted image 20260617190033.png]]

**Monotonic Read** provides guarantee for this type of problems. It guarantees that when a user made several reads in sequence, they will not see time go backward.

We can achieve monotonic reads by making sure that each user make their read requests from the same replica (Choose the replica based on the hash the user's ID).

#### Consistent Prefix Reads

![[Pasted image 20260617191046.png]]

**Consistent Prefix Reads** guarantees that if a sequence of writes happens in certain order, anyone reading those writes will see them appear in the same order.

This problem typically arise in sharded databases when one shard is slower than the other, but in normal databases this anomaly can't happen.

### Solution for Replication Lag

If the application is fine with eventual consistency, it's all good. But, if you required strong consistency you need to write complex application code. For example, routing certain read requests to leader.

More simpler approach is to choose a database that provides strong consistency guarantees for replicas, such as linearizability, and support ACID transactions.


## Multi-Leader Replication

Multi-Leader replication addresses the downside of single-leader configuration, which process all the writes from a single node.

There can be multiple leader nodes which can accept writes and forwards the data change to other nodes. This is called **multi-leader** configuration or **bidirectional replication**.
Each leader acts as a follower to other leaders.

Synchronous multi-leader replication works similarly like a synchronous single-leader replication.

![[Pasted image 20260618161558.png]]

### Geographically Distributed Operation

Database with replicas in several **region** is known as *geographically distributed*, *geo-distributed* or *geo-replicated* setup.

![[Pasted image 20260618162456.png]]

Single-Leader replication vs Multi-Leader replication in geo-distributed databases:

| Point            | Single-Leader                                                                                      | Multi-Leader                                                                                                                                             |
| ---------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Performance      | Every write must go to the leader's region, resulting in higher latency                            | Writes can be processed by each region's leader and replicated asynchronously across region                                                              |
| Regional Outage  | If the region with the leader becomes unavailable, leader will be transferred to different region. | Each leader can operate independently of the other.                                                                                                      |
| Network Problems | It is very sensitive to problems in the inter-region links.                                        | Asynchronous replication can tolerate network problems better.                                                                                           |
| Consistency      | Better consistency guarantees.                                                                     | Weaker consistency because, each leader's writes are individually fine but may violates the constraint when taken together with write on another leader. |

### Topologies

If we have multiple leaders, we need to replicate data across leaders using a network topoloy.

A **Replication Topology** describes the communication path along which writes are propagated from one node to another.

![[Pasted image 20260618164548.png]]

In circular and start topology, a write may need to pass through several nodes before it reaches all nodes. Therefore, these topologies forward the data they receive along with their own data. For preventing infinite replication loop, replication log tag each writes with the identifiers of all the nodes it has passed through.

Problems with different topologies:
- In the circular & star topology, failure of a single node can interrupt the flow of replication messages between other nodes.
- All-to-All topology also have issues in which some network link may be faster than the others, resulting in some messages overtaking others.

![[Pasted image 20260618165412.png]]

To prevent problem of causality, we need to sync messages so that insert messages are processed before update message. This can be done using *version vectors*.

### Sync Engines and Local-First Software

Multi-leader replication is useful for an application that need to continue to work even when offline. It is also useful for *real-time collaboration features*.

Example 1,
A Calendar app might continue to work offline and it will sync the data with your other device when its next online.

In this case, every device has a local database replica that acts as a leader, and there is asynchronous multi-leader replication process (sync) between replicas of the calendar.

Example 2,
In real-time collaborations software like google drive, each tab in your web browser that has opened the shared file is a replica, and any updates are asynchronously replicated to devices of the other user who have opened the same file.

When multiple users change the data concurrently, conflict resolution logic is needed to merge the data. A software library that supports this process is called **Sync Engine**.

An application that allows a user to continue editing a file while offline (which may be implemented using a sync engine) is called **Offline-First**.

The term **Local-First Software** refers to collaborative apps that are not only offline-first but are also designed to continue working even if the developer who made the software shuts down all of their online services.

#### Pros and Cons of Sync Engine

- Having data locally makes the UI response quicker.
- Users can work even when offline.
- A sync engine combined with a reactive programming model can be used to display edits from other users in real time.
- Sync engines are not suitable if the user have access to a very large amount of data.

### Dealing with Conflicting Writes

If you are using multi-leader replication then conflicts will arise, both in geo-distributed databases and local-first sync engines.

![[Pasted image 20260619170955.png]]

Conflict resolution techniques:

#### Conflict Avoidance

The first technique is to avoid conflicts from occurring. This can be done by making application process all writes from a single leader, although the database as a whole in multi-leader. This can't be implement in local-first sync engines because, it will process the write offline.

Example 1,
An application where user can edit only their own data, we can ensure that all the writes of a particular user routes to the same region and its leader. Different users can have different home region, but from user's pov, the configuration is single-leader.

But if we want to change the leader for some reason (regional outage, user's location changes), there is now risk that the user may perform write while the process of changing the leader is going on. Therefore, conflict avoidance breaks down if the leader needs to be changed.

#### Last Write Wins

If conflicts can’t be avoided, the simplest way of resolving them is to attach a timestamp to each write and to always use the value with the most recent (greatest) timestamp.

This is approach is called *last write wins* because the write with the greatest timestamp can be considered the last one. In case of concurrent writes, one of the writes is randomly chosen and other is discarded. This method eventually achieves consistent state on all the replicas, but at the cost of data loss.

#### Manual Conflict Resolution

If discarding some of the writes is not desirable, we can solve conflicts manually. Similarly like, merge conflicts in git

In a database however stopping replication because of the conflict like git is not practical. Databases typically stores all the concurrent written value for record. The value are called *siblings*. In the next query, the database will return all the values. Conflict resolution then can take place in application code or by asking the user.

Problems:
- The database will now returns multiple values instead of a single value, which will make the data awkward.
- Application complexity increases, as the developer now needs to write conflict resolution UI. Its better to merge automatically than bothering the user.
- Merging sibling needs to be done correctly otherwise, anomaly like below can happen where even if the user removed products from cart, they still can see them.
- If multiple nodes observer the conflict and concurrently resolves it, the conflict resolution process can produce more conflict.

![[Pasted image 20260619173954.png]]

#### Automatic Conflict Resolution

For many applications, the best way for conflict resolution is to use an algorithm that automatically merges concurrent writes into a consistent state. Automatic conflict resolution ensures that all replicas converge to the same state meaning, all the replicas that have processed the same set of writes have the same state. Combining eventual consistency with a convergence is know as **Strong Eventual Consistency**.

##### Conflict-free Replicated Datatypes & Operational Transformation

CRDT & OT are used to implement automatic conflict resolution.

![[Pasted image 20260619180330.png]]

- OT records the index at which characters are inserted or deleted. It transforms the index of each operation to account for concurrent operations that have already been applied (like in the case of `!` transformed to index 4 ).
- CRDTs give each character a unique, immutable ID and use those to determine the positions of insertion/deletions. For insertions we need to pass the IDs of new character and existing character after which we need to add it.

List and arrays can easily be implemented because, they just have different element types than characters, similarly key-value maps.

## Leaderless Replication

Leaderless replication works without electing a replica as a leader. It was used by Amazon's in-house Dynamo system (not DynamoDB, it uses single-leader replication), Cassandra and ScyllaDB. All the replicas can process reads and writes in leaderless replication.

### Writes

Leaderless configuration have no such thing as failover, when some node go down, the system works similarly as before.

For writing, the client send writes to some number of replicas, let's say 3 in parallel. A replica might be unavailable so it misses it. The system might be satisfied with 2 out of 3 replica processing successful write, and send OK response to the client.

When the unavailable nodes comes back, and if it starts to serve read requests it might give *stale* results. For avoiding stale data, client also sends read request to several replicas in parallel, and uses the one with greatest timestamp.

![[Pasted image 20260620171509.png]]

### Catching up on missed writes

The replication system ensures that unavailable nodes catch up all the missed writes by using techniques like below:

#### Read Repair
When a client makes a read request from several nodes in parallel, it can detect any stale responses and write newer value back to the replica. This work well for system values that are read often.

#### Hinted Handoff
If one replica is unavailable, another replica may store the writes on its behalf in the form of *hints*. When the replica comes back, hints are sent it. This handoff process makes replicas up-to-date without any read request.

#### Anti-Entropy
A background process periodically looks for differences in the data between replicas and then copies any missing data from one replica to another.

### Quorums

If there are `n` replicas, every write must be confirmed by `w` nodes to be considered successful, and we must query at least `r` nodes for each read. As long as `w + r > n`, we expect to get an up-to-date value when reading. Reads and writes that follows these `r` and `w` values are called **quorum read and writes**.

The quorum condition, `w + r > n`, allows the system to tolerate unavailable nodes as follows,
-  If `w < n`, we can still process writes if a node is unavailable.
- If `r < n`, we can still process reads if a node is unavailable.
- With `n = 3`, `w = 2`, `r = 2`, we can tolerate one unavailable node.
- With `n = 5`, `w = 3`, `r = 3`, we can tolerate two unavailable nodes.

![[Pasted image 20260620172946.png]]

#### Quorum Consistency

Quorums are not majorities, it matters only that the set of nodes used by the read and write operations overlap in at least one node.

We can set `w + r <= n` (unsatisfied quorum), in which less number of successful responses are required for the operation to succeed. With lower `w` and `r`, you might read stale values, but the system becomes highly available if synchronous replication is used.

consistency properties of quorums:
- If a node carrying a new value fails, and its data is restored from a replica carrying an old value, the number of replicas storing the new value may fall below `w`, breaking the quorum condition.
- If a read is concurrent with a write operation, the read may or may not see the concurrently written value.
- If a write succeeded on some replicas but failed on others, and overall it failed, it is not rolled back on the replicas where it succeeded. Meaning subsequent reads may or may not return the value from that write.
- If two writes occur concurrently, one of them might be processed first on one replica, and the other might be processed first on another replica, creating a conflict. 

In leader-based replication, it was possible to observe the replication lag by looking at the followers current position in the replication log using their tags and subtracting it from the leader's current position. However, in leaderless replication writes are not applied in a fixed order, making monitoring hard.

### Single-Leader vs Leaderless Replication

Single-leader replication provides strong consistency guarantees that are difficult or impossible to achieve in a leaderless system, reads can also return stale values in asynchronous single-leader replication.

Problems with Single-leader replication:
- Read throughput is limited by the leader's capacity to handle requests. (because asynchronously updated follower may return stale value)
- If the leader fails, we need to wait for the failover to complete before accepting any requests.
- The system is sensitive to the performance problems on the leader. If the leader is slow, users will see increased response times.

Leaderless replication is more resilient to these types of problems. The client can use responses from faster replicas to respond and ignore unavailable nodes.

Problems with Leaderless replications:
- When a replica stores *hints* about other replica and the replicas becomes available, the hinted trade-off process puts additional load on the system.
- The more replicas you have, the bigger quorum is required, which increases the chance of hitting slower nodes, increasing response times.
- A large scale network interruption may disconnect a client from large number of replicas, making it impossible to form a quorum. 

Quorum reads and writes provides fault-tolerance and high likelihood of reading up-to-date data at the cost of low resilience against network interruptions.

### Multi Region Operations

Leaderless replication can be used to implement multi region operations. We can choose from variety of consistency level like,
- Quorum across the replicas in all regions,
- A separate quorum in each of the region,
- Quorum only is the client's local region. (Which may provide stale results, but avoids slow cross-region requests)

### Detecting Conflicting Writes

Conflict may arise in leaderless replication as with multi-leader replication. If a replica just overwrote the value with new value, problems as shown in the image might arise. This needs to be addressed using various conflict resolution techniques.

![[Pasted image 20260621182217.png]]

The system may use LWW (last write wins) method, in which each write is tagged with a timestamp, but the timestamp doesn't tell whether two value are actually conflicting (a value is overwriting another or actually concurrent) because, they might arrive in different orders.

#### The happens-before relation and concurrency

If some operation B builds upon an operation A, meaning B’s operation must have happened later. Then we can say that B is *causally dependent* on A.

An operation A *happens before* another operation B if B knows about A. So, we can simply say that two operations are *concurrent* if neither happens before the other.

Thus we need an algorithm that can detect if operations are concurrent. If one operation happened before another, the later can simply overwrite the earlier operation.

#### Capturing the happens-before relationship

An algorithm that determines whether two operations are concurrent or whether one happened before another might look like this:
- The server maintains a version number for every key, and increments the version number every time that key is written, and stores the new version number along with the value written.
- When a client reads a key, the server returns all values that have not been overwritten (siblings) as well as the latest version number.
- When a client writes a key, it must include the version number from the prior read, and it must merge together all values that it received in the prior read (e.g., using CRDTs).
- When the server receives a write with a particular version number, it can overwrite all values with that version number or below.

Here the version numbers determine whether two operations are concurrent or not.

Example,
![[Pasted image 20260621184124.png]]

![[Pasted image 20260621184154.png]]

#### Version Vectors

The scale the algorithm used previously across multiple replica, instead of maintaining single version number, we need to use version number per replica as well as per key.

Each replica increments its own version number when processing writes, and also keeps track of the version number it has seen from each of the other replicas.

The collection of version numbers from all the replicas is called **version vector**.