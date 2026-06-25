13-06-2026  14:49

Status:

Tags: [[DDIA]]

# Chapter 5 - Encoding and Evolution

### Rolling Upgrade
Deploying the new version of a software to a few nodes at a time, monitoring whether it runs smoothly, and gradually deploying to all the nodes.

### Backward Compatibility
Ensures that newer code can read data written by older code.

### Forward Compatibility
Ensures that older code can read data written by newer code.

### Encoding & Decoding
The translation from the in-memory representation to a byte sequence is called **encoding** (also known as serialization or marshaling), and the reverse is called **decoding** (aka parsing, deserialization, or unmarshaling).

### Service Discovery
Service discovery is a problem of a client needing to know the address of the service it's connecting to.

### Load Balancing
Spreading requests across multiple instances of a service which are running on numerous machines is called Load Balancing.

### Hardware Load Balancers
Hardware Load Balancers are specialized pieces of equipment installed in datacenters. They route incoming client request to downstream servers.

### Software Load Balancers
Unlike Hardware Load Balancers which requires special appliances, Software Load Balancers are applications that can be installed on a machine.
E.g., NGINX, HAProxy

### DNS
DNS allows load balancing by assigning multiple IP addressed to a single domain name.
DNS propagation are slow, meaning if the servers are started, stopped, or moved frequently, clients might see stale IP addresses.

### Service Discovery Systems
Service Discovery Systems uses centralized registry to track which service endpoints are available.
E.g., etcd, Apache ZooKeeper

### Service Mesh
In a Service Mesh load balancers are deployed on both client and server as an in-process client library, or as a process, or "sidecar" container. Client connects to their own local load balancer, which connects to the server's load balancers, which routes the connection to the local server process.

### Workflow
A sequence of a steps is called **Workflow**, and each step is called a **Task**.
e.g., Payment processing workflow

**Workflow Engine** runs and  executes a workflow.

### Durable Execution
Durable execution frameworks are a way to provide **exactly-once** semantics for workflows.
e.g., Temporal

### Event-Driven Architecture
The sender send **events** or **messages** to recipient via a **message broker**, which stores the messages temporarily.

### Actor Frameworks
The actor model is a programming model for concurrency in a single process. Logic is encapsulated in **actors**, which communicates with other actors asynchronously.

In Distributed Actor Frameworks this model is used to scale an application across multiple nodes.





# References