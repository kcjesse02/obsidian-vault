## Construction Proof
- Prove something has the required properties
- EX: if r is rational so is ${r}^{2}$
	- $r$ is rational r=a/b -> ${r}^{2} = \frac{{a}^{2}}{{b}^{2}}$


# Computational Model
What to expect a computational model to have
- states
- inputs and outputs
- Alphabet


# Finite Automata
- inputs - all possible combinations of the alphabet
- assumes inputs are also always finite
![[Screenshot 2023-08-28 at 5.38.42 PM.png]]
This one checks if a number has two consecutive 1 binary digits!

States: q1, q2, q3
transitions
Start State
- every state must have a transition corresponding to each member of the alphabet (transitions can cover multiple characters)
Accept/Nonaccepting States
- Accept
- Why?
- Describes the legal output
- Behavior is captured through if input ends with accepting/non-accepting state
	- essentially true/false
Alphabet: {0, 1}
- Output is always 0 and 1

Finite Automata always halt

### Definition
- a finite automata M is a 5 tuple
- $\left(Q , \Sigma , \delta , q , F\right)$
	- Q = finite set of states
	- $\Sigma$ = finite alphabet
	- $\delta$ = transition function: $\delta = Q \cross \Sigma$
	- q = starting state
	- F = final state


This automata is looking for two consecutive ones

Transition functions can be described in a table


### inputs
- Automaton makes a single pass over input $w = w_{1} , w_{2} , w_{n}$

## Computational process
Input: finite string
Output: accept or reject

Steps:
- begin at start state
- read input symbols
- Follow inputs
- Accept if ends with Accept state, reject otherwise


Language recognized by finite automaton M: Set of all strings resulting M to end in an accepting state

Denote with L(M)

### Check
What are the five components
- start state
- accepting state
- transitions
- Language
- state