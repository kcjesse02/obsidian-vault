prove that for any string $w=w_1+w_2+...w_k$ over an alphabet $\Sigma$ of the string $w$ is regular
- language consisting of one string
### Possible Solution 1
![[Screenshot 2023-09-07 at 10.09.37 AM.png]]
- utilize a trap state
- for any character that is not equal, move to the trap state
- if the string reaches wk (and no more), the accept state will be reached

#### This Solution with NFAs
![[Screenshot 2023-09-07 at 10.12.39 AM.png]]

- if anything other than the correct character is entered, the automaton will produce an error

### Closure under concatenation
1. prove that a single character is a regular language
2. show that this problem is just the concatenation of k characters
3. use closure under concatenation to show that you can chain those simple automaton under


# Regular Operation: Star

Prove if A is regular than so is $A^*$ 

the star is not the power set!
$A = \{1\}$
Power set : {empt, 1}
$A^* = \{\epsilon, 1, 11, 111, ...\}$


solution: just create a looping automaton
- add accepting start state $q_1$ so that the $\epsilon$ will be accepted
	- only moves to previous start state $q$
	- All accepting states have transitions that move back to $q$

![[Screenshot 2023-09-07 at 10.38.50 AM.png|600]]


# Regular Expressions
*Regular Expressions:* Languages over alphabet $\Sigma$
Defined as
- a: a character in $\Sigma$
- $\epsilon$
- $\Phi$ - empty set
- concatonation, union, and star all apply to regular expressions