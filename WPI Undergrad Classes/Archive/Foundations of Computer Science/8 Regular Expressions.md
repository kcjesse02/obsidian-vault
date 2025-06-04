**Regular Expressions:** Expressions that express a regular language, language over alphabet $\Sigma$
- closure under union, concatonation, and star
- DFAs, NFAs, and Regex all can represent the same languages
- inductive data type
![[Screenshot 2023-09-08 at 4.52.16 PM.png]]
made up of these symbols
- concat is the most important operation

Technicality
- we need a regular expression that matches nothing to define the empty language

![[Screenshot 2023-09-08 at 4.53.45 PM.png]]

Shorthand operations
A+ : $A \circ A^*$
## Closure under complement
- you can turn regex into DFAs
- We know DFAs are easy to complement
- and we can turn it back into regex

## DFA -> Regular Expression proof
- suppose a language is accepted by NFA. Then it can be described
- Transform DFA to regular expression
- While there are two or more states, remove states and get an equivalent automata
- keep going until you get one with a start state and an accept state

GNFA: Generalized Nondeterministic finite automaton
- transitions between states are regular expressions
- can only pass if the string belongs to regular expression
- one start state
- one accept state
	- can combine states with epsilon moves
- One arrow from each state to each other state (excluding start and accept)
- only exiting start state
- only entering the accept state


Any DFA can be transformed into equivalent GNFA

### Proof By Induction
How do we remove a state without changing the automaton
- Repair iteratively for all pairs of states
- only do it for ordered states

A language can be represented by DFA if and only if it can be represented by a Regular Expression