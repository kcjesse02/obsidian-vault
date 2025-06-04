There are languages that are not regular

**How do we prove?**
- find an example of a language that cannot be represented by finite automaton
- proof by contradiction

Cardinality argument
- Countably many regular languages
- Uncountably many languages

Non-regular language examples
- equal number of 1s and 0s
- palindromes
- any language that requires "counting" or keeping track of previous states for a potentially infinite length
$$L=\{0^{n}1^{n}|n \in \natural\}$$

- impossible with a finite number of states
- an infinite language can be regular
	- stars
	- any number of all 0s

- Any machine recognizing this language must "remember" the number of zeros

## The pigeon hole principle

Supponse there is a DFA M accepting L
consider $W = \{0^{n} | n\}$
let k be the number of states of M
since k is finite, there must exist integers a not equal b 
when M is run on $0^a$ reaches the same state as when M is run on $0^b$
so
$M(0^{a}1^{a}) = M(0^{b}1^{a})$
contradiction: the first should accept, but the second should not

