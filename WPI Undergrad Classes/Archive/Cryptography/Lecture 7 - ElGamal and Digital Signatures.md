### Discrete Log Problem review
$\beta = \alpha^{x} \mod N$
- find x
- 

#### DHKX problem
![[Pasted image 20240930195427.png]]
#### Baby Step Giant Step Algorithm
- Array of giant stepsbb steps with baby steps
- cols are giant
- rows are babies
- ![[Screenshot 2024-10-28 at 6.30.44 PM.png]]
- literally square and multiply
- How do you choose Alpha and Beta
- Why don't we need to take the log of everything
	- 
- ![[Screenshot 2024-10-28 at 6.33.51 PM.png]]

## El Gamal Encryption (MoreDLP)
- Only one communicator generates keys (public and private)
	- Choose large prime P
	- Choose a generator: $\alpha \ \epsilon \ \mathbb{Z}^{*}_p$
	- Choose integer $a < p-1$
	- computer $\beta = \alpha^{a} \mod P$
	- pub = $(\beta, \alpha, P)$
		- sent to Alice
	- private = a
		- because thats the discrete log problem
- key reciver encrypts messages
	- recieves pub = $(\beta, \alpha, P)$
	- choose ephemeral key $k$
		- do not 
	- computer key mask: $c_{1}=\alpha ^ {k} \mod P$
		- second part of ct: $c_{2}=m * \beta^{k} \mod P$
	- send $Enc(m,k) = (c_{1}, c_{2})$
- Bob Decrypts $Enc(m,k) = (c_{1}, c_{2})$
	- check if $m = c_{2}(c_{1}^{-1})^{a} \mod P$


How do we find $(c_{1}^{-1})^{a}$
- fermat little therom
![[Pasted image 20241028191102.png||500]]
- $-a = P-1-a$
- you cant find inverse $(c_{1}^{-1})^{a}$ 

### Example
![[Pasted image 20241028192024.png]]
![[Screenshot 2024-10-28 at 7.20.43 PM.png]]
