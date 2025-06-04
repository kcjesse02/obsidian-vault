- Goal: mathematical model of a computer
	- We want to capture what a computer can do
- Do DFAs capture what computers can do?
	- CFGs?


### Turing Machines
- before computers
	- model what a human can do with paper and pencil

#### Elements
Infinite Tape
- Discrete slots each containing an alphabet
- A finite portion contains the input
- the rest of the tape has blank symbols
- only a finite number of symbols on the tape
Finite Number of States
- Accept State
- Reject State
Tape Head
- current index it is trying to read or write
- Can reador write to tape
Controller
- governs operations of the tape


This is a good mathematical model for what a computer can do!!

## Definition
7-tuple
1. Q: states
2. $\Sigma$: input alphabet not containing the blank sigmas
3. $\Gamma$: tape alphabet. $\Sigma \subseteq \Gamma$ 
4. $\delta$: the transition function
5. $q_0$: start state
6. $q_{accept}$: single accepting state
7. $q_{reject}$: single reject state

- Input: everything that is not blank



##### Benefits
- Elegant and simple to define Mathematically

- Illustrates the limits of computation
	- DFAs only illustrate finite memory lists
	- PDAs only illustrate stack memory
- Counter Programs and Lambda calculus


##### Configuration
Description of what is currently going on in the turing machine
- content of the tape
- location of the head
- the state

Notation: 00001 $q_1$ 10101 -> infinite blank symbols
- the state is always written to the left of the symbol it is on
- so it is on the "one"

##### Movement between configurations
reads a b, replaces it with c, and then moves to the right

##### Accepting states
- configuration is accepting if it includes the accepting state $q_{accept}$
- 






