- structurally, a collection of methods


![[Screenshot 2023-11-07 at 1.18.31 PM.png]]

- shields the client from knowing anything from the data representation of the provider

## Quiz Question
**Entity-Boundary-Controller**
- identify class diagrams (like UML)
	- Identify Entity Classes
	- Controller Classes
	- Boundary Classes
- Attributes
- Methods

- Use cases are provided

### Sample
a counter app

Use cases
- increment
- decrement

Boundary
- UI
	- up button
	- down button
	- value field
	- redraw(model: Model)
Model
- count: int

Controller
- updateValue(model: Model, delta:int)

## Design and Testing
- a good design can be tested in units
	- Control Objects are ideal for testing
	- Entity Objects must be tested
	- Boundary Objects cant be tested

### Testing
- create model
- do something to the model
- check if a value is what it is expected to be with the `expect()` function
- run `npm test` to run your tests
- (create a file for your tests :))

## Interfaces
- enable shared behavior without shared structure
- to decouple classes that would otherwise be tangled