A system is composed of interacting objects

##### Common Categorizations

1. Entities
	1. represents persistent information tracked by the system
	2. pure state!
	3. Forms whole core of the application
2. Boundaries
	1. Represents interactions between actors and the system
	2. never have state
	3. form based interaction
	4. Mock-up GUIs will be modeled using boundary objects
3. Controls
	1. Realizes use cases
	2. coordinate activities launched by actors
	3. stateless (ideally)
	4. guide the application between states

##### Global Topology
- Top level entity object: model
	- allows for navigation to all other entity objects
- Top level GUI object: app

