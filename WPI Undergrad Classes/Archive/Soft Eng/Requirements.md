**Quiz Friday: writing a sample use case**
# Modeling
#### Abstract representation of a system

##### Application domain model
- representation of user's problem
- model of real-world system, as observed and relevant
- elements of real world system
	- ex: wood slide puzzle
	- a grid, a red block

##### Solution Domain Model
- the actual model of the solution
- Model desired system
- produces model that can lead to a number of systems
- rich details
- may change because of technology

# Use Case
Identify discrete transformation of system from one consistent state to another consistent state

##### Focus in answering "What" Questions
- What users should see?
- What can users do?


## Template
```
Participating actor: initiated by actor1

Entry condition
- List Here
- 

Exit Criteria
- Update state of system (not like a function return)

Flow of Events
1. initiating actor preforms some action
2. system responds in kind
3. participating actor preforms some action
4. system responds in kind
```

- use case should include normal behavior, not what is possible

Ex: *Move Piece*
```
Participating actor: initiated by the user

Entry condition
- A piece must be selected
- 

Exit Criteria
- piece has been moved and move counter incremented

Flow of Events
1. User requests to reset the puzzle
2. SlidingPuzzleApp moves piece and refreshes display
```

### Build Use Case
- Identify All actors
- Define Boundaries of the System
- Avoid synonyms
- Aim for completeness


### Represent Modal Behaviors
- Form: generic graphical user interface element