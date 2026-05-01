# Domain: numerical methods

The gotchas catalogue. Reference doc — consulted, not read linearly.
Grows with use.

---

## Floating point — the ground truth

Everything below assumes IEEE 754 double precision unless stated.

- Doubles have ~15-17 significant decimal digits. Operations near machine
  precision (`~1e-16`) are noise.
- `0.1 + 0.2 != 0.3` is the canonical example. Never use `==` for floats.
  Compare `abs(a - b) < tol` with tolerance appropriate to scale.
- For relative comparison: `abs(a - b) / max(abs(a), abs(b), tiny) < tol`
  where `tiny` prevents division-by-zero when both are small.
- Single precision (`float32`) has ~7 digits. Almost never appropriate for
  financial numerics, but lurks in libraries and GPU code.

### Subtractive cancellation

When subtracting two nearly-equal numbers, you lose precision proportional
to how close they are. Classic example: the variance formula
`E[X²] - E[X]²` is numerically catastrophic for small variances; use the
two-pass or Welford algorithm instead.

In financial code: deep-ITM call price via put-call parity often loses
precision; compute directly when possible.

### Accumulation order

`sum([1e-10] * 1_000_000) + 1.0` is not the same as
`1.0 + sum([1e-10] * 1_000_000)`. Adding small to large loses the small
contributions.

For sums of many terms, use `math.fsum` (Python) or Kahan summation
(C++). For dot products that matter, use BLAS (`numpy.dot`) which uses
careful accumulation.

---

## Conditioning

The condition number of a problem measures how much output changes per
unit input change. Ill-conditioned problems amplify input noise.

- For a matrix `A`, the condition number is `σ_max / σ_min` (ratio of
  largest to smallest singular value). `numpy.linalg.cond(A)`.
- Condition number `> 1/eps` (~1e16 for double) means the matrix is
  numerically singular. Don't invert.
- Condition number `> 1e8` means you're losing half your precision.
  Treat results with suspicion.

When ill-conditioned:
- Don't invert. Solve `Ax = b` with a method that handles it
  (SVD, regularisation).
- Consider reformulating. Sometimes the conditioning is a symptom of a
  badly-posed problem, not just a numerical issue.
- For least squares: don't form normal equations (`A^T A x = A^T b`) — squares
  the condition number. Use QR or SVD directly.

---

## Stability of methods

A *stable* method doesn't amplify rounding error during computation.
Different methods for the same problem can have very different stability.

- Gaussian elimination without pivoting: unstable on general matrices.
  Always use partial pivoting.
- Forward Euler for stiff ODEs: unstable for any meaningful step size.
  Use implicit methods.
- Explicit finite difference for parabolic PDEs: stable only when
  `dt < (dx)² / (2D)`. CFL condition. Easy to violate accidentally.

When debugging, ask: is this method stable for *this* class of inputs?

---

## Convergence

For iterative methods (root finding, optimisation, eigenvalue solvers,
PDE relaxation):

- Always set both an iteration limit and a tolerance.
- Always check whether the limit was hit. Silent non-convergence is the
  classic numerical bug.
- "Tolerance" is ambiguous: relative or absolute? Both? `|x_{n+1} - x_n|`
  or `|f(x_n)|`? Make the criterion explicit.
- Initial guess matters. Many methods (Newton, Gauss-Newton) only converge
  in a basin near the solution.

---

## Random numbers

For Monte Carlo:
- Set the seed. Reproducibility is non-negotiable for debugging.
- Mind the generator. `random.random()` (Python) is fine for many uses;
  `numpy.random.default_rng()` is better for arrays. For derivatives
  pricing, consider quasi-random (Sobol, Halton) when paths are
  high-dimensional.
- Antithetic and control variates: easy to apply, easy to break. Test
  the variance reduction; if `var(reduced) >= var(plain)`, the variance
  reduction is bugged.
- Path generation: log-Euler vs exact for GBM. For SV models (Heston),
  Euler can produce negative variances — use QE or full truncation.

---

## Linear algebra recipes

| Problem | Don't | Do |
|---|---|---|
| Solve `Ax = b` | `inv(A) @ b` | `solve(A, b)` |
| Least squares | Normal equations | `lstsq` (QR / SVD) |
| Symmetric positive definite | Generic solve | Cholesky |
| Eigenvalues of symmetric | `eig` | `eigh` |
| Determinant for "singularity check" | `det(A)` | `cond(A)` |
| Matrix inverse for downstream solve | Compute it | Don't compute it; solve directly |

---

## PDE schemes (quick reference)

| Scheme | Stability | Accuracy | Notes |
|---|---|---|---|
| Explicit (forward Euler in time) | Conditional (CFL) | O(dt) | Simple, fast per step, restrictive on dt |
| Implicit (backward Euler) | Unconditional | O(dt) | Solve linear system per step |
| Crank-Nicolson | Unconditional | O(dt²) | Good default; oscillations near discontinuities |
| Rannacher | Unconditional | O(dt²) | CN with implicit start; smooths discontinuities |

For options pricing: Crank-Nicolson is the workhorse, Rannacher when
payoff has kinks (digital, barrier).

---

<!-- expand from practice -->

Add gotchas here as you encounter them. Each entry: situation, symptom,
diagnosis, fix.
