- fewer objects means fewer interactions
- more objects means more interaction
model the entity objects: where all of the state is

Think of things as classes

##### Class Element Diagrams

- classes have attributes (variables)
- needs a type and a name
- also a multiplicity
	- how many values this thing can have
	- default is 1
- Also has visibility
	- public
	- private
	- protected


### Self Assessment
Classes

**Tournament**
attributes: Rounds[any], champion[0,1], teams[any]


**team**
attributes

**match**
attibutes: teams[2], winner[0,1], 


**Round**
attribute: matches[any], type[1], 


### Aggregation Relationship
peer to peer
- common collection relationship:1..*
- meant to be a peer relationship

Aggregate Relationship
- denotes hierarchy
- different from peer to peer
	- ex: directory to file
- how to tell if there is an aggreggate relationship: is there an operation I would like to preform over all of the collection?
	- compute size by summing the size of each individual file

Composite Relationship
- when an object cannot exist without a container object
- ex: Polygon/PolygonLine
- No purpose for having a PolygoneLine without a Polygone