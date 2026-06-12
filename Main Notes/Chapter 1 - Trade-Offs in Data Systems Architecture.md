27-05-2026  16:54

Status:

Tags: [[DDIA]]

# Chapter 1 - Trade-Offs in Data Systems Architecture

## Definitions

### Data-Intensive Applications
Applications which have primary challenges in data management such as, storing and processing large volumes of data, managing changes to data, ensuring consistency and concurrency, and availability.

## Operational vs Analytical Systems
### Operational Systems
Systems which reads and modifies data directly in databases, based on the actions performed by the users.
e.g., backend services and data infrastructure

### Analytical Systems
Systems that contains a read-only copy of the data from the operational systems. Optimized for the type of data processing needed for analytics.
e.g, data scientists and business analysts

### Online Transaction Processing (OLTP)
Access patterns of interactive applications typically looking up a small number of records by a key (point query) and then inserting, updating, or deleting based on user's input is called OLTP.

### Online Analytics Processing (OLAP)
Access patterns that include analytical query scans over a huge number of records and calculating aggregate statistic is called OLAP.

### Real-Time Analytics
Systems designed for analytical workloads but embedded into user-facing products are known as product-analytics or real-time analytics. e.g., ClickHouse

### Data Warehouses
A separate database containing read-only copy of the data from all the various OLTP systems is called Data Warehouse. 

### ETL
The Process of extracting data from OLTP databases, transforming into an analysis-friendly schema, cleaning up, and then loading into the data warehouse is knows as extract-transform-load (ETL).

### Hybrid Transactional/Analytical Processing (HTAP)
A single system which offers both OLTP and OLAP system is called HTAP. HTAP internally consists of an OLTP system coupled with separate analytical system, so that you don't need to perform ETL between systems.
e.g, Fraud detection

### Data Lake
Data lake contains files obtained from operational systems via ETL processes. It doesn't impose any particular file format, data model or schema like data warehouses. It can also contains text, images, videos, etc. It is cheaper than relation data storage.

### Systems of Record
A System of Record holds the authoritative of canonical version of data. It is also know as source of truth.

### Derived Data System
Data in derived data system is the result of taking existing data from another system and transforming and processing it in some way. It can be re-created from the original source if lost.
e.g, cache, indexes, materialized views

## Single-Node vs Distributed Systems

### Distributed Systems
A system which involves multiple machines communicating via network is called Distributed System.

### Node
Each of the processes participating in the distributed system is called a Node.

### Microservice Architecture
In microservice architecture, a system is broken down into multiple well-defined service. Each service exposes an API that can be called by clients via the network. It is also called service-oriented architecture (SOA).

### Serverless
Serverless is an approach for deploying services, in which the management of infrastructure is outsourced to a cloud vendor. It is also known as Function As A Service (FAAS).





# References