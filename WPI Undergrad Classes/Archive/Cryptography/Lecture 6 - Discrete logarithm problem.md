$\log_{3}1000 = 3$

$\log_{3}1010 = 3.\_$    $\log_{3}1050 > \log_{3}1010$ 
We know that of two logs with the same base, the one with the larger value is greater than the one with the smaller value

$\log_{11}1010 > \log_{12}1010$
Two logs with the same values but different bases, the one with the smaller base is larger

- we can order our numbers
- 
## Discrete Log Problem
- apply modular reduction
- we can no longer order by value
- no solution garunteed
- no algorithm to efficiently computer 

##### What values produce a solution
$\log_{B}A = x \mod N$
**Remember**
- if N is a prime number, satisfy following requirement: $x * x^{-1} = 1 mod N$
- use Fermat's theorem
	- $x^{N-1} = 1 \mod N$
	- N = 23; x = 6
	- which power will produce a one?
	- by fermat's theorem, should be N-1 = 22
Example: $\mod 13$
*put here*

Multiplicative Cyclic Groups
- create series of numbers of numbers up to N-1 (which produces a one) and then repeat
- you can write any value in the space as a power of a given base
- use cyclic group lookup table to calculate inverse elements
	- among other things
Inverse element in cyclic group
- inverse value = $x^{-1}$
- Find Inverse
	- find the value in the cyclic group: $6\mod 13=2^{5}$
		- use this base column for finding the inverse
	- find the value with the same base for 1: $1 mod 13 = 2^{12}$
	- decompose into multiplication of original value and new one: $2^{12}\mod 13 = 2^{6}*2^{7}$
	- table lookup: $2^{7}= 11$
	- multiply original number with number from lookup: $6*11=66 \mod 13 = 1 mod 13$
	- 11 is the inverse because 11\*6 produces an
- What is the set called
	- $Z_{n}^{*}$ = the set of the bases
	- $|Z_{n}^{*}|$ = the order of the set
	- generator: a base that can be used to find the inverse of all members in the set
	- Q: if given the order, how many generators?
		- num generators = $\Phi(N-1)$
		- or $\Phi(\Phi(N))$, which will always be $\Phi(N-1)$ since N is prime
	- relatively prime numbers to 12: 1, 5, 7, 11
	- lookup table: using the relatively prime numbers of N as powers
		- if you know one generator look at those powers and it will list the 

#### Discrete Log Problem
$\beta = \alpha^{x} \mod N$
find x
- hard problem
- no polynomial time algorithm

### Diffe-Hellman Key Exchange
- uses the discrete log problem
- create session key without exchanging private keys
- generate common key using public keys for session of communication
- 

|     | Pk    | Sk  |
| --- | ----- | --- |
| A   | BetaA | b_a |
| B   | BetaB | b_b |
![[Pasted image 20240930195427.png]]
###### Why are the keys equal
$K = (\alpha ^{b_{B}^{b_{A}}}) = (\alpha^{b_{A}^{b_{B}}}) = (\alpha^{b_{B}*b_{A}}) \mod P$
Can oscar find them?
- needs to find either $b_{a}$ or $b_b$
- $\log_{K} \alpha = {b_{B}*b_{A}}$
- has to solve DLP
- and then has to factor 

In practice
- connect in a session with shared key
- change private keys to generate a new key
- allows for communication to use symmetric key
- does not auth: use digital signatures

## Shanks Algorithm
- a sub-exponential time algorithm for DLP
- Baby step, giant step algorithm
- square root algorithm: reduces given complexity by square root
	- $2^{128}$ to $2^{\frac{128}{2}} = 2^{64}$

How do we do exhaustive search
- x can be 1 to P-1
- iterate throughx values and check if resulting beta is equal to the given
How is Shanks different?
- have more than one beta
- choose value t: inital x value
- choose checkpoints to k: t, 2t, 3t, kt
- check if those are equal to beta
- takes advantage of the group being cyclic
- keep mutiplying by alpha

#### Picking k and t
- checking points should be 1, N-1 and (N-1)/2
- multiply by alpha: rotates counterclockwise: 0, N-2, ((N-1)/2)-1
- keep multiplying by alphas
- $t=\sqrt P$
- 