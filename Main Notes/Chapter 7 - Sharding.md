---
created: 2026-06-23-16:20
links: "[[DDIA]]"
tags:
  - DDIA
---
# Chapter 7 - Sharding

A distributed database distributes data across nodes in two ways:
1. By storing a copy of the database on multiple nodes (*Replication*).
2. By splitting the data into smaller *shards* or *partitions*, and storing them on different nodes (*Sharding*).

A sharded database with single-leader replication may look like this:

![[Pasted image 20260623164901.png]]

Some database like PostgreSQL treats sharding & partitioning differently. **Partitioning** is a way of splitting a large table into several files that are stored on the same machine.

## Pros & Cons

The primary reason for sharding a database is scalability. It is the solution if the volume of data or the write throughput can't be handled by a single node. If read throughput is the problem, use *read scaling* not sharding.

Sharding allows *horizontal scaling (scale-out architecture)*, meaning you can add more machines into the system instead of moving to a bigger system.

**Partition Key** decides which records to put in which shard. All the records with same partition key are placed in the same shard.

Sharding introduces a problems when you need to join records that might be distributed across different shards or to perform a search by a secondary index. Another problem is a write may need to update related records in several shards, which requires a *distributed transaction*.

### Multitenacy

Sharding can be used for *multitenacy*, by using a separate shard for each tenant. This has several advantages:
- *Resource Isolation* : One tenants' operation is less likely to affect performance of other tenants.
- *Permission Isolation* : If there is some bug in access control logic one tenant can't access other tenants' data, if those tenants' dataset are stored in physically separate from each other.
- *Per-tenant backup and restore* : Backing up each tenant's shard separately make it possible to restore it without affecting other tenants.
- *Regulatory compliance* : Laws like GDPR & CCPA can be followed simply by exporting or deleting tenant's shard.
- *Data residence*
- *Gradual schema rollout*

It also have some disadvantages:
- If a tenant's data is not small enough to fit on a single node, we need to further perform sharding.
- Having too many small tenant incur's overhead. So you may group multiple small tenant into a one big shard, but then you have a problem of moving tenants from one shard to another.
- To support features that require data across multiple tenants, it becomes harder to implement.

##  Sharding of Key-Value Data

The goal of sharding is to spread the data and the query load evenly across nodes. If you add or remove node, the load should be rebalanced.

If the sharding is unfair, so that some shards have more data or queries than other, it is called as *skewed*. Skew makes sharding less effective. In the extreme case all the load may be handled by a single shard, this shard is called *hot shard* or *hot spot*. If a key in particular have high load, it is called *hot key*.

To split the database into shards, we need an algorithm that return the shard as the input of a partition key.

### Key Range Sharding

In key range sharding, contiguous range of partition keys are assigned to each shard. Which may be look like this.

![[Pasted image 20260623173456.png]]

Each shard's range of key may not be evenly spaced, because some keys may require more storage than the others. In each shard, keys are stored in sorted order, which makes range queries faster.

The downside is that, if there are a lot of writes in nearby keys, then the shard will be overloaded with writes while other sits idle.

#### Rebalancing key-range sharded data

Some databases allows users to configure an initial set of shards on an empty database, which is called *pre-splitting*.

Later, as the data volumes and write throughput increase, a key-range sharded system grows by splitting an existing shard into two or more shards and distributing it across multiple nodes. If large volumes are deleted, we may need to merge several adjacent shards. This is very similar to a top-level of B-tree.

Shard splitting is an expensive operation, because all of its data needs to be rewritten into new files. A shard that needs splitting is often the one with high load, and the cost of splitting can increase the load, making the shard overloaded.

### Sharding by Hash of Key

Key range sharding is useful if we require nearby keys to be grouped in the same shard. Otherwise, we can hash the partition key before mapping it to the shard. Let's see how to assign shards after hashing the key.

#### Hash modulo number of nodes

The most simple approach is to modulo the hash by the number of nodes.  $\text{hash(key)} \mathbin{\%} \text{No. of nodes}$

The problem with this approach is that when the no. of nodes changes, most of the keys have to be moved from one node to another.

![[Pasted image 20260624164543.png]]

We need an approach that moves as little data as possible.

#### Fixed number of shards

Another approach is to create many more shards than the number of nodes, and assign several shard to each node. A key is then stored in the shard number $\text{hash(key)} \mathbin{\%} \text{No. of shards}$ and  the shard to node mapping can be kept separately by the system.

When new nodes are added or removed, only entire shards are moved between nodes.

![[Pasted image 20260624165142.png]]

This approach work good if you good estimate of how many shards are going to be required. If the number of shards needs to be changed, an expensive resharding needs to take place.

#### Sharding by hash range

If the number of shards can't be predicted in advance, it's better to use a technique which can adapt easily to the workload. A solution is to combine key-range sharding with a hash function so that each shard contains a range of *hash value*. 

![[Pasted image 20260624165716.png]]

A shard can be split just like key-range sharding when it becomes to big, so the number of shards adapts to the volumes of data rather than being fixed in advance.

The downside of this approach is range queries are not efficient, as keys in the range are now scattered across all the shards because of hashing.

#### Consistent Hashing

A *consistent hashing* algorithm is a hash function that maps keys to a specified number of shards in a way that satisfies two properties:
- The number of keys mapped to each shard is roughly equal.
- When the number of shards changes, as few keys as possible are moved from one shard to another.

###  Skewed Workloads and Relieving Hot Spots

Consistent hashing ensures that keys are uniformly distributed across nodes, but it doesn't solve the problem of actual load being uniformly distributed.

Example,
A post by a celebrity in a social network site, can result in large volumes of reads and writes to the same key.

Techniques which shards based on ranges of key/hashes can make it possible to put an individual hot key in a shard by itself.

### Manual vs Automatic Rebalancing

Some systems automatically decides when to split shards and when to move them from one node to another, while other require administrator to configure.

Fully automated shard management can be convenient, but unpredictable. If a system is near its maximum write throughput, the shard-splitting process might be triggered and overload the system.

For this reason, it is preferable to have a human in loop for rebalancing. Manual rebalancing is also useful for preemptively rebalancing if a surge in traffic is expected.

## Request Routing

How do you route the request if want to read or write a particular key? Which IP address and port number you need to connect to?

This problem is called *request routing*, and it is very similar to *service discovery*. The biggest difference between them is that, services running the application code are stateless, and a load balancer can send request to any of the instance. But in sharded database, a request for a key can be handled only by a node that is a replica for the shard containing that key.

For this request routing needs to be aware of the key to a shard and shard to a node mapping. Few approaches for this are as follows:

![[Pasted image 20260624172103.png]]

1. Clients contacts to any node and if that node owns the shard, it can handle the request directly. Otherwise, it forwards the request to the appropriate node, receives the reply, and passes the reply to the client.
2. Client sends requests to a routing tier, which determines the node that should handle the request and forwards it accordingly.
3. Clients are aware of the sharding and the assignment of shards to nodes and the client connect directly to the appropriate node.

There are few problems in each approach:
- How does the system map a shard to a node? A single coordinator is a simplest approach, but it is not fault tolerant. And if the coordinator can failover, there might be split-brain situations.
- How does the component performing routing learns about changes in shards to node mapping?
- How do you handle in flight requests when a shard is moved from one node to another.

Many services rely on separate coordination service like ZooKeeper or etcd to keep track of the shard to node assignments. This coordination service holds the authoritative mapping and other actors subscribe to this information.

![[Pasted image 20260624173218.png]]

## Sharding and Secondary Indexes

Majority of the relational database support secondary indexes. How do you use secondary indexes to query efficiently in sharded database??

### Local Secondary Indexes

In this approach, each shard maintains its own secondary indexes, covering only the records in that shard. It is known as *local index* because when writing, you need to update the secondary index of the shard you are writing.

![[Pasted image 20260625153650.png]]

When reading from local secondary index, if the partition key of a record is already known, we can perform the search on the appropriate shard, but if it is not known, you need to send query to all shards and combine the results.

This makes read queries expensive and doesn't improve query throughput as all the shards has to process every query.

> Better write throughput.

### Global Secondary Indexes

Global secondary indexes covers data in all shards and because the index itself might be big, it is also sharded.

![[Pasted image 20260625154414.png]]

This is kind of index is also knows as *term-partitioned*, which is generalized form of term (keyword in a text) in full-text search.

Global secondary indexes uses term as the partition key, which can be sharded based on contiguous range of terms of hash of the term.

The advantage is that a query with a single condition needs to read from only a single shard to fetch all the ids. With multiple conditions, the system needs to fetch IDs from different shards and calculate their logical AND.

The disadvantage is that when writing, the shard that contains the term for modified record needs to be modified with the original data shard. We can use distributed transaction for atomically updating both the shards.

> Better read throughput.