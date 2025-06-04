Elliptic Curves - Algebreic Geometry problem

Relationship between line and a given equation
- all points on the line satisfy the equation


common EC: $y^{2}=x^{3}+ax+b\mod P$
![[Screenshot 2024-11-04 at 6.10.12 PM.png|| 600]]
- points on the curve satisfy equation
- pick parameters a, b, P
- curve gives us a group structure
	- Addition Operation is the operation
		- Geometric Definition
			- choose a line equation
			- there should be three intersecting points A, B, and C
			- C = A+B
		- Algebreaic Definition
			- find the intersection between two lines and reflect it
			- find the intersection: $\frac {x-x_{1}} {x_{1}-x_{2}} = \frac {y-y_1} {y_{1}-y_{2}}$
				- where $y = ax+b$
			- $\lambda^{2} - x_{1}-x_{2} \mod P =x_{3}$
			- $\lambda(x_{1}-x_{3})-y_{1} \mod 11 = y_{3}$
			- formula cannot add a point to itself
				- use new lambda formula:
				- $\lambda = \frac {3{x_{1}}^{2}+a} {2y_{1}}$
				- geometrically: tangent line of A should intersect with B=2A
	- Identity Point
		- assume it is at $\infinity$
		- 
		- 
- condition $49^{3}+27b^{2} \neq 0 \mod P$
- number of points is an even number, because the elliptic curve


### Finding X-Y Pairs

create a table
- find relationships between variables and their respective sides
x

$y^{2}= x^{3}+x+6$

| $X^{3}+x+6 \mod 11$ | x   |
| ------------------- | --- |
| 6                   | 0   |
| 8                   | 1   |
| 5                   | 2   |
| 3                   | 3   |
| 8                   | 4   |
| 4                   | 5   |
| 8                   | 6   |
| 4                   | 7   |
|                     | 8   |
|                     | 9   |
| 4                   | 10  |

y

| y   | $y^{2}$ |
| --- | ------- |
| 0   | 0       |
| 1   | 1       |
| 2   | 4       |
| 3   | 9       |
| 4   | 5       |
| 5   | 3       |
| 6   | 3       |
| 7   | 5       |
| 8   | 9       |
| 9   | 4       |
| 10  | 1       |
any two points with equal values for their respective sides of the equation satisfy the equation

ex:
(5, 2)
(2, 4)

finding pairs
- curve is symmetric 
- indicated 


## Example
Doubling

$y^{2}= x^{3}+x+6 \mod 11$
A=(2, 4)

$$\lambda = \frac {3{x_{1}}^{2}+a} {2y_{1}} = \frac {3{2}^{2}+1} {2*4} = \frac{13}{8} \mod 11$$
$$\frac{13}{8}\mod 11 = 2 * 8^{-1} \mod 11$$
$$$$

## Security Applications
- Generators for the group
	- All $\alpha$ in $\alpha A$ are on the given curve
- $\alpha$ is the hidden value
- Why ECC instead of Cyclic Groups?
	- the size: No one can know how many points on the ECC
	- you can reduce the size of the $\alpha$ compared to 
	- key of 768 bit cyclic groups: $$2*10^8$$
	- key of 150 bits ECC: $$3*8*10^{10}$$

## DHKX on EC
- use multiplication on EC for 
- (P is a generating point)
- adding it to itself will generate all points in the group

![[Pasted image 20241104194434.png]]


## DSA on EC

##### key gen
inputs:
	E: Elliptic Curve
	P: generator point

outputs
	priv_k: d= random num $d \in \mathbb{Z}^*$
##### sign
inputs
	M: message
	d: private key
	E: elliptic curve
	P: basepoint of E

1. choose ephemeral key $k\in \mathbb{Z}^{*}_{n}$
2. $kP = R = (R_{x}, R_{y}) \rightarrow r$
3. compute $s = k^{-1}(H(m)+dr) \mod n$
4. H is a hash function

##### verify
inputs
	sign (r1, s1)
	message m1
	public key q
	elliptic curve E
	basepoint P

- point is obtained through the certificate
- $w = (s^{1})^{-1}$
- $u_{1}=H(m_{1})1 \mod n$
- $u_{2}= r^{1}w \mod n$
- $R^{1} = u_{1}P + u_{2}q \in E = (R^{1}_{x_{1}}, R^{1}_{x_{2}})$
if $R^{1}_{x} = r$, accept
else reject

**DSA on EC is not Timing Secure**
you can compare times for different A values








