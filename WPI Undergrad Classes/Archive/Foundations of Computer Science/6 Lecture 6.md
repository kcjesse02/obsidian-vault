### NFA: Definition of computation
- If there is an error (invalid character) before the entire string is read, reject regardless of the state!

## Are NFAs and DFAs Equivalent
- Yes! Computationally equivalent
	- because everything must be finite for both NFA and DFA
#### Proof
Suppose $M$ is an NFA recognizing language $L$
- Keep track of different branches
- Create $M'$ whose states correspond to all subsets of states from $M$ Power set!!
- start state will be the same
- accepting states: family of all subsets that contain an accepting state

## Closure under Union
- suppose A and B are regular. Prove that their union is regular using NFA

- create both automatons
- add new start states
- add epsilon moves leading to the start states of both automatons
![[Screenshot 2023-09-05 at 10.57.00 AM.png|500]]
### Why?
- since both automatons are intact within this new one
- the paths remain intact
- so a member of the union will be accepted

## Closure under Concatenation
- put epsilon moves from A to B, and vice versa
- assumes that the accepting states of A move to start states of b
![[Screenshot 2023-09-05 at 10.50.22 AM.png]]

$$A \circ B = \{xy | x\in A \quad and \quad y \in B\} = AB$$
