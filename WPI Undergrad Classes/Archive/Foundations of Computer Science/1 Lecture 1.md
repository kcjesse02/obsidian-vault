# Strings

Alphabets: finite set of characters
- a string will be a subset of that alphabet

language: a subset of strings over an alphabet
ex:
- all binary strings with the same number of 0s and 1s
- all id numbers of students taking foundations (in binary)
- the alphabet would be A={0, 1}

languages can be finite or infinite, or even empty

lexicographic order:
- order strings based on the placement of its first characters in the string.

$alphabet = \Sigma$

Sigma-star is the set of all possible string combinations in the alphabet

prefix: a string that can become another string if concatenated with another string.
ex: pref a the prefix of prefix

## Questions
1. How many binary strings are there of k length
	$\Sigma \cdot \left(star\right) = {2}^{k}$
2. Is 1010 a prefix of 1010?
	yes, because you can concatenate the empty string, therefore all strings are prefixes of themselves

# Proofs
- proofs and writing good code are similar

- be patient
	- it can take a long time
- Be neat
	- use clear-well defined terms
- Be concise
	- Avoid ling sentences and remove redundancies
- Try to find errors

## Proof by contradiction
- assume theorem is false
- derive and obviously false consequence
- so the theorem must be true
- use when you want to prove that something does not exist

ex:
prove $\sqrt{2}$ cannot be rational

2 = n*n
2/n = n

there is no integer that can satify this, making $\sqrt{2}$ not rational

**Solution**
How do you show something is irrational

suppose $\sqrt{2} = \frac{m}{n}$
assume m, n are relatively prime -> so they are in simplest form
${m}^{2} = 2 \cdot {n}^{2}$ --> m is even
${n}^{2} = 4 \cdot \frac{{k}^{2}}{2} = 2{n}^{2}$
n is even as well
m, n are even so they have 2 as a common factor to divide them
contradiction to the assumption that m,n are relatively prime!!

Conclusion:
$\sqrt{2}$ cannot be rational



