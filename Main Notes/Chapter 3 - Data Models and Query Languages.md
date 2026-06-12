04-06-2026  16:33

Status:

Tags: [[DDIA]]

# Chapter 3 - Data Models and Query Languages

### Property Graphs
The property graph (also known as labeled property graph) model, each vertex consists of:
- A unique identifier
- A label (string) to describe the type of object this vertex represents
- A set of outgoing edges
- A set of incoming edges
- A collection of properties (key-value pairs)
Each edge consists of the following:
- A unique identifier
- The vertex at which the edge starts (the tail vertex)
- The vertex at which the edge ends (the head vertex)
- A label to describe the kind of relationship between the two vertices
- A collection of properties (key-value pairs)

### Triple Stores 
The Triple Store model is equivalent to property graph. All information in triple store is stored as subject, predicate(verb), object.
e.g., Jim like Banana.

### Event Sourcing
Using events as the source of truth and expressing every state change as an event is known as Event Sourcing.

### CQRS
The principle of maintaining separate read-optimized representations and deriving them from the write-optimized representation is called Command Query Responsibility Segregation (CQRS).





# References