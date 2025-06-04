# Properties of Regular Languages

if Language 1 and Language 2 are regular, the union will be regular

If A is regular, so is the complement of A!

**How to prove:** proof by construction. Since A is regular, we know there is a finite automaton, so we flip the non-accepting states to accepting and vice versa.
- transitions will stay the same
- A single example does not constitute a proof by construction!


## Regular Operations
- Union: $A \cup B$
- Concatonation: $A \dot{} B$
- Star $A^*$
	- will always be infinite unless input is empty string or empty language
![[Screenshot 2023-09-01 at 10.00.10 AM.png|600]]


### Closure under union

- finite automatons do a single pass from left to right
- Describe the finite automaton that will accept the Union
	- Number of States: A in first automaton, B in second. The union automaton will have AxB states!
![[Screenshot 2023-09-01 at 10.03.00 AM.png]]
# Nondeterminisim
- new model of finite automata
- easier to work with
- Generalization of deterministic finite automaton
	- in deterministic finite automaton, there is a transition for every character in the alphabet
- multiple transitions per character possible, zero as well
- epsilon moves - empty string

**Computations on a given input can have multiple outcomes**
- 'accepts' if at least one outcome for input accepts