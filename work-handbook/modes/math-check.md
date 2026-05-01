# Mode: math-check

For sanity-checking derivations, proofs, or formulae. Pure math mode —
no code unless explicitly requested.

## Protocol

1. State what you're trying to verify. Theorem / identity / derivation /
   inequality.
2. State your derivation or claim. Use LaTeX or plain math notation.
3. State the level of rigour wanted: heuristic plausibility, dimensional
   check, full proof, counter-example hunt.
4. Get the check. Then ask for a *second* check by a different method
   if the result is load-bearing.

## What math-check should always do

- **Check dimensions and units first.** Cheapest filter — catches a
  surprising fraction of errors.
- **Check limit cases.** What happens as a parameter → 0, → ∞, → boundary?
  Does the formula reduce to the known case?
- **Check symmetry.** If the problem is symmetric in two variables, is the
  answer? If not, why not?
- **Check special values.** Identity at zero, value at infinity, behaviour
  at known points.
- **Try a second method.** Two derivations agreeing is much stronger than
  one rechecked. Methods that work: direct vs generating function, integration
  by parts vs substitution, change of measure vs direct expectation.

## What math-check should not do

- Accept a derivation because each step "looks right." Step-level checking
  is necessary but not sufficient — sign errors compound.
- Skip the limit-case check because the formula is "obviously" correct.

## Common pitfalls in math-check

- Swap of order of integration / summation without checking convergence.
- Differentiating under the integral sign without justifying it.
- Conditional expectation manipulations that drop information silently.
- Itô vs Stratonovich confusion (relevant in stochastic calculus).
- Change of measure that forgets the Radon-Nikodym derivative.
- Index gymnastics: forgetting that `∑_{i≠j}` excludes the diagonal.
- Convergence in distribution vs probability vs L².

## On asking Claude to verify

Claude can be confidently wrong on math. Treat any verification as a *second
opinion*, not a proof. If the result matters, derive it two ways yourself
and use Claude as a third check, not the only one.
