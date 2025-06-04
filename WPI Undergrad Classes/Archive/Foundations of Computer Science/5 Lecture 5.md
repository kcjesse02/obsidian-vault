# Nondeterministic Finite Automata
- same 5-tuple as a deterministinc automata
- change is in $\delta$
$$
\delta : Q \times \Sigma_{\epsilon} 
$$

Ways to think about non-determinism

- Computational: Fork new parallel thread and accept.
- Mathematical: Accept any branch that leads to accept state.
- Magical: Guess at each nondeterministic step which way to go.

The possible accepting state path is no longer a single entity, rather a subset for which we check if an element is a part of!

Accepts string $w$ if $w = w_{1}+w_{2}+w_{3}$...

### Why epsilon moves?
- you can always get rid of epsilon moves
- you have an equivalent NFA without epsilon moves


What language does this recognize?

- needs to contain a 1
- needs to have at least two characters after the 1
