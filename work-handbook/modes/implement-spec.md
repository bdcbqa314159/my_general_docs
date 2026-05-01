# Mode: implement-spec

For translating a paper, formal spec, or mathematical method into code.
High-risk mode because the gap between math-on-paper and code-that-runs
is where most bugs live.

## Protocol

1. State the source. Paper title / spec name / textbook reference. Section
   or equation number if available.
2. State the method's name and family. ("Crank-Nicolson for parabolic PDEs",
   "Andersen QE scheme for Heston", "Levenberg-Marquardt for nonlinear
   least squares".) The family matters because it determines the failure modes.
3. Paste the equations or pseudocode in sanitized form (replace specific
   asset names / parameter values with symbols).
4. State the *test case* up front. What input and expected output proves
   the implementation is correct? "We'll know it works if BS price for
   K=100, S=100, σ=0.2, T=1, r=0.05 returns 10.45..."
5. Implement in stages: a *naive* version that's obviously correct but
   slow, then optimise. Compare both at each step.

## What implementation should always do

- **Match the paper's notation in code, then rename.** First pass uses the
  paper's variable names (even if ugly) so the mapping is obvious. Rename
  for clarity only after correctness is established.
- **Validate against a known case before trusting any result.** Closed-form
  if available. Reference implementation if not. Limit case (σ→0, T→0)
  if neither.
- **Check units and dimensions.** Time in years vs days. Rate as decimal
  vs percent. Volatility annualised vs not.
- **State the discretisation explicitly.** Time step, space step, scheme
  (explicit / implicit / Crank-Nicolson, Euler / Milstein / QE, etc.).
- **State the boundary conditions explicitly** for any PDE. Most paper bugs
  hide here.

## What implementation should not do

- Translate equation-by-equation without running anything. Build the test
  case first, implement until it passes.
- Skip the "naive but correct" stage because it's slow. The naive version
  is the oracle for the optimised version.
- Trust performance optimisations (vectorisation, parallelism) without
  checking they preserve correctness.

## Common pitfalls when implementing from papers

- Sign convention: r vs -r, q vs -q for dividends, log-moneyness sign.
- Indexing: paper uses 1-indexed; code is 0-indexed.
- Discount factor placement: missing `exp(-r*T)` somewhere.
- Time direction: backward induction vs forward simulation confusion.
- "Standard" formulas with unstated assumptions (e.g., flat rates, constant vol).
- Typos in the paper itself. (Common. Always cross-check with a second source
  if a published value disagrees.)
