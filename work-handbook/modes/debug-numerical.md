# Mode: debug-numerical

For numerical code that runs but produces wrong, unstable, or divergent
results. The hardest debugging case because the program isn't crashing —
it's lying.

## Protocol

1. State the symptom precisely. "Wrong" is not a symptom. "Returns 1.04 when
   the closed-form gives 1.02" is.
2. State what you've already checked. Don't make me re-walk ground you've
   covered.
3. Paste the relevant function in sanitized form. Include shapes, dtypes,
   and a minimal input that reproduces.
4. State the expected output and how you computed the expectation
   (closed form, simplified case, reference implementation, paper result).
5. Walk through the failure modes catalogue (below) systematically.
   Don't skip steps because they "shouldn't" apply.

## The failure modes catalogue

When numerical output is wrong, it's almost always one of these:

**Floating point precision**
- Comparison with `==` instead of `abs(a-b) < tol`
- Subtractive cancellation (large minus large = small with no precision)
- Accumulation order (sum of small + large + small ≠ sum of large + small + small)
- Single vs double precision somewhere in the chain

**Conditioning / stability**
- Ill-conditioned matrix (check condition number)
- Method appropriate for the conditioning? (e.g., Gaussian elimination on
  near-singular matrix vs SVD)
- Step size in finite differences: too small → noise, too big → bias

**Convergence**
- Iteration limit reached without convergence (silently?)
- Tolerance set wrong (relative vs absolute)
- Method doesn't converge for this input class
- Initial guess in basin of wrong root

**Boundary / edge cases**
- t=0, T=expiry, K=0, sigma=0, very deep ITM/OTM
- Empty array, single element, all-zero input
- Negative inputs where positive assumed

**Random number issues** (Monte Carlo)
- Seed not set, run-to-run noise
- Insufficient paths
- Variance reduction broken or applied wrong
- Antithetic / control variate bug

**Indexing / shape**
- Off-by-one in time grid
- Transposed matrix
- Broadcasting where you expected element-wise
- numpy view vs copy confusion

**Algorithmic**
- Wrong formula (most common when implementing from a paper)
- Sign error
- Forgotten constant (the `1/sqrt(2*pi)` you didn't include)
- Discretisation scheme wrong (explicit/implicit/Crank-Nicolson confusion)

## What debugging should always do

- Compare against a known case. Black-Scholes vs a published value.
  Heat equation vs analytical solution. Don't debug in the dark.
- Reduce the input until the bug is in the simplest case that still
  reproduces. Often the bug becomes obvious here.
- Print or log intermediate values. The bug is between two of them.
- Suspect the discretisation before suspecting the formula. Suspect the
  formula before suspecting the language.

## What debugging should not do

- Add `try/except` to make the symptom go away.
- "Tune" parameters until the answer looks right. That's overfitting,
  not debugging.
- Skip the comparison-against-known-case step because it "obviously" matches.
