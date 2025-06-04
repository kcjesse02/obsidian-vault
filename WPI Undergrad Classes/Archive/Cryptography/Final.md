Problem 3

Before we get into evaluating its efficacy, First, you would require two large n values, and then two pairs of public and private keys

$gcd(e_{1}, \Phi(N)) = 1$
$gcd(e_{2}, \Phi(N)) = 1$

$d_{1}=e_{1}^{-1} \mod \Phi(N)$
$d_{2}=e_{2}^{-1} \mod \Phi(N)$

the encryption for double RSA can be expressed with the following equation:

$c = m^{e_{1}*e_{2}} \mod N$

decryption can be expressed with the following

$m = c^{d_{1}*d_{2}} \mod N$
or in terms of e
$m = c^{e_{1}^{-1}*e_{2}^{-1}} \mod N$

Not only does this not add security, it makes the system less secure by compomising the security of the inverse number problem. the two double encryption keys multiply together to essentially make a new key. Same goes for the decryption keys.

$e_{1}*e_{2} = e_{new}$
$d_{1}*d_{2} = d_{new}$
also
$e_{1}^{-1}*e_{2}^{-1} = e_{new}^{-1}$
$d_{new} = e_{new}^{-1}$

It is important that $e_{new}$ is relatively prime so that its inverse (the decryption key $d_{new}$) only has one possible value. There is no certainly that $e_{new}$ is relatively prime to $\Phi (N)$. Therefore, there could be multiple valid inverses to $e_{new}$, meaning there are multiple values of private key $d_{new}$ that can decrypt a message. This has the potential to make the search space for brute forcing $d_{new}$ much smaller.




