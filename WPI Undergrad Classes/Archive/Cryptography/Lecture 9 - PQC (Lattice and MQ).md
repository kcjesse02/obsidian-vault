NIST Post-Quantum Crypto Competiton
- 2016-2024
- Two types: Public-Key, and Digital Signatures
- Cystals Kyber is the winner for PK
- Crystals Dilithium is winner for DS

# Lattice-Based Schemes
- the winning scheme is Lattice Based

What is special about Lattice-Based Crypto
- Based on hardness of lattice problem
- ![[Screenshot 2024-11-11 at 6.08.30 PM.png]]
	- Lattice based crypto uses matrix multiplication and linear algebra operations

## Vector Spaces
- $x = < x_{1}, x_{2}, ... x_{n}>$
- Basic Operations
- set of all real vectors denoted as $\mathbb{R}^{n}$
- Inner product: $<x, y> = x_{1}*y_{1} + ... + x_{n}*y_{n}$
- Orthogonal vectors: inner product is o
- length of vectors: add squares of components and sqrt
- Triangle Inequality: $||x+y|| \leq ||x|| + ||y||$
- Scaling: multiply all components by a constant
- linear independance
	- a set of vectors $b_{1}, ... , b_{m}$ in $\mathbb{R}^{n}$ where the equasion $a_{i}$ implies all $a_{i} are 0$
	- write all vectors in the space can be described as $Basis * Constant$
	- 
- the linear independant set of vectors is called the Basis subspace
- a Matrix of all of these vectors is called the Basis matrix


## The Gram-Schmidt Process
- every subspace V has a large number of possible basis matricies
- it is often prefered to find a basis with nice properties (containing orthogonal vectors)
- Gram-Schmidt is an
![[Screenshot 2024-11-11 at 6.30.15 PM.png||400]]
![[Screenshot 2024-11-11 at 6.30.35 PM.png]]
- this is a 2D space, so we only have to run this on the two vectors that w 
## Lattices
- Subspace of the vector subspace.
- All components are integers. Discrete version of vector subspace
	- called lattices because the space consists of discrete points, not a graph
	- ![[Screenshot 2024-11-11 at 6.40.53 PM.png]]
- makes finding the Basis more difficult, since you can't multiply the basis by irrational numbers
	- Can mutiply fractional numbers by multiplying space by the denominator
	- Gram-Schmit does not work on lattices


## Successive Minima
- what is your shortest non-zero vector
- Successive Minima: smallest radius such that n-dime
- 
- this is a hard problem: smallest vector problem
	- easier in low dimensions, becomes very complicated in higher dimensions
### LLL Algorithm
- like GS for lattices
- not always possible to find orthogonal vectors in the lattice space

## Shortest Vector Problem
- which exact vector is the shortest in the space?
- Approximate-SVP: find the smallest constant to multiply
- there will be at least two shortest vectors

## Closest Vector Problem


## Learning with error problem
- its easy to solve a linear system of equations
	- ![[Screenshot 2024-11-11 at 7.05.54 PM.png||400]]
- Regev Scheme
	- add an error term
	- ![[Screenshot 2024-11-11 at 7.06.20 PM.png||400]]
	- e is a vector with the same length as s
	- introduces two unknown vectors
	- there is a space of possible solutions now
	- e is like the private key

#### Encryption Scheme
![[Screenshot 2024-11-11 at 7.10.07 PM.png]]

Secret key: s
Public Key: Matrix A, vector b = As+e

Encryption
- choose random small vector r
- ciphertext: $c = rA + \frac q 2 * (\sigma, 0, ..., 0) \in Z^{n}_{q}$
- sigma is one bit of additional error in the system

Decryption
- inner product $y = <c, s>$
- Correctness: Output 0 if $|y| < \frac{q}{4}$, else output 1
- matches output of $\sigma$
- 
# MQ Type Sheme
Multivariate Public Key Crypto (MPCK)
 Problem
 - Satisfiability Problem
	 - system with m equations and n unknowns
	 - how do we find a solution that satisfies all equations
	 - specifically for boolean equations with xor
	 - How do you pick coefficients such that you know the solution (secret key)

Oil and Vinegar Scheme OV
- separate oil terms and Vinegar terms

UOV
- unbalance number of O and V terms
- more V variables

LUOV
- lifted UOV
![[Screenshot 2024-11-11 at 7.34.27 PM.png]]

Vinegar Vars: $x_{1}, ..., x_{v}$
Oil Vars: $x_{v+1}, ..., x_{n}$
- V terms are the secret key
- O terms are public

