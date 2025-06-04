If there is an algorithm that solves a problem then there is a Turing machine that solves the problem

Why?
- If there is no turing machine that solves a problem, then no algorithm will solve it


#### Hilbert 10th Problem
- Multivariate polynomial
- Terms: product of variables
- Terms are multiplied by constants
- A Root: number nullifying the polynomial

Question: given a multivariate polynomial with integer coefficients, does it have an integral root?
- can we have an algorithm to solve this?

No algorithm :(
How can we prove there is no algorithm?
- no turing machine can decide if a polynomial has an integer root


### Undecidability
we need to provide a problem that a turing machine can't solve

###### Cardinality
- for finite sets, sets have the same cardinality if they have the same # of elements
- for infinite, they have the same cardinality if there is a 1-1 and an onto function between them
	Ex: even integers and odd integers have the same cardinality because for every even, there is an odd integer

**Countable sets**
a set that has the same cardinality as the natural numbers or is finite
- the rational numbers!

use diagonalization to prove that the real numbers are not countable.
- proof by contradiction

f() is the one to one and onto mapping of the natural numbers to the real numbers

construct a real number that is different for every i, x, for f(i)
- must be "countable" with natural numbers

Consruct x digit by digit
{insert image}

x will be different for all of these numbers...
a function does not cover all real numbers because we get a new real number!

#### the set of turing machines is countable
- can encode every turing machine using a finite binary string(an integer!)
- there is a one-to-one mapping for every turing machine so
- Since it is a binary string, the set of turing machines is a subset of countable numbers

#### The set of languages over binary strings is not countable
