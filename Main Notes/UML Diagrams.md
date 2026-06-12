02-06-2026  19:04

Status:

Tags: [[Tags/LLD]]

# UML Diagrams

## Class Diagrams

### Structure

![[Pasted image 20260602190614.png]]

#### Access Modifiers

`-` : Private
`+` : Public
`#` : Protected

### Associations

There are 2 types of class associations:
1. Class Associations
	- Inheritance
2. Object Associations
	- Simple Association
	- Composition
	- Aggregation

![[class_diagram_relationships.webp]]

#### Class Associations

##### Inheritance
Inheritance represents **is-a** relationship between 2 classes.
It is represented with closed arrow tip head.

Example,
Cow is-an Animal.

#### Object Associations
##### Simple Association
Simple Association represents **has-a** relationship.
It is represented with opened arrow.

Example,
Max has-a House.

##### Aggregation
Aggregation represents **whole-part** relationship. A whole class is composed of many part classes. All the parts **can** exists independently.
It is represented with diamond shape at the side of whole class.

Example,
Sofa, Chair & Fan are parts of a House.

##### Composition
Composition is a stronger form of aggregation. All the parts **can not** exists independently without whole class.
It is represented with filled diamond shape at the side of whole class.


## Sequence Diagram

### Classes
Classes are defined using a simple container.

### Lifeline
Dotted line vertical to a class represents its lifeline inside the application.

### Messages
Communication between objects is depicted using messages.
There are 2 types of messages:
1. Synchronous
2. Asynchronous

#### Synchronous Messages
Sender waits for a response after sending the request with Synchronous Message.
Synchronous Request is represented using solid arrow head. Response are represented with dotted arrow.

#### Asynchronous Messages
Sender doesn't wait for a response after sending the request with Asynchronous Message.
It is represented using solid arrow head.

#### Create Message
Create message instantiate a new object and start its lifeline.

#### Delete Message
Delete message destroys existing object.

#### Found Message
Found message represents a message from unknown source. It is represented using an arrow directed towards a lifeline from a dot.

#### Lost Message
Lost message represents a message lost in communication. It is represented using an arrow directed towards a dot from a lifeline.

### Example

![[Pasted image 20260602202207.jpg]]





# References