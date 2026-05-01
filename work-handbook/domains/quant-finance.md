# Domain: quantitative finance

Patterns and gotchas specific to pricing, risk, calibration, and quant
infrastructure. Reference doc — grows with use.

---

## Pricing

### Black-Scholes sanity values (memorise)

For ATM call/put, S = K = 100, r = 0, q = 0:
- σ = 0.20, T = 1.0 → price ≈ 7.97
- σ = 0.10, T = 1.0 → price ≈ 3.99
- The rule of thumb: ATM price ≈ 0.4 · S · σ · sqrt(T) for small σ√T.

If your implementation deviates from these, fix the implementation before
trusting any other output.

### Common pricing pitfalls

- **Day count conventions.** ACT/365, ACT/360, 30/360, ACT/ACT — they
  produce different times, different rates, different prices. Pin down
  the convention at the top of any pricing pipeline.
- **Discount factors and compounding.** Continuous vs simple vs compounded.
  `exp(-rT)` vs `1/(1+rT)` vs `(1+r/m)^(-mT)`. Inconsistency is the bug.
- **Forward vs spot price quoting.** F = S · exp((r-q)T) for equities.
  Easy to confuse the two in commodities and FX.
- **Dirty vs clean price** for bonds. Coupon accrual matters.

### Greeks

- Analytical Greeks are exact and fast — use them when available.
- Bumped (finite-difference) Greeks are method-agnostic but noisy. Bump
  size matters: too small → noise dominates, too large → bias dominates.
  Rule of thumb: `bump = sqrt(eps) * scale` where `eps ≈ 1e-16`.
- Central differences are O(h²); forward differences are O(h). Use central
  unless you have a reason not to.
- AAD (algorithmic differentiation) gives all sensitivities in one
  reverse pass — cost roughly 3-5× the original valuation, regardless of
  number of sensitivities. Lifesaver for high-dimensional sensitivities.

---

## Calibration

The art of finding model parameters that match observed market prices.
Notoriously easy to get wrong.

### Common pitfalls

- **Loss function design.** Squared price errors weight expensive options
  more; squared vol errors are usually more sensible. Use vega-weighted
  price errors as a compromise.
- **Local minima.** Most calibration objectives are non-convex. Use a
  global method (differential evolution, basin hopping) for the first
  guess, then a local method (Levenberg-Marquardt) to refine.
- **Parameter bounds.** Vol > 0, correlation in [-1, 1], mean reversion > 0.
  Constrain explicitly, don't hope.
- **Overfitting.** A model with enough parameters fits any market;
  doesn't mean it predicts. Out-of-sample test before trusting.
- **Stale or mispulled market data.** The most common cause of weird
  calibrated parameters. Sanity-check the inputs first.

### When calibration "fits but doesn't make sense"

Symptom: low residual error, but parameters are at bounds, or oscillate
day-to-day, or take physically implausible values.

Likely causes:
- Model is misspecified (wrong dynamics for this market).
- Data is bad.
- Loss function is wrong (weighted in a way that gives flat regions).
- Genuine degeneracy: multiple parameter combinations fit equally well.

---

## Monte Carlo

### Path generation

- For GBM: log-Euler is exact, plain Euler is not. Use log-Euler.
- For SV models (Heston, SABR): variance can go negative under Euler.
  Use QE (Andersen) or full truncation; never plain Euler.
- For LMM and HJM: drift terms are expensive; precompute and reuse.

### Variance reduction

- Antithetic: cheap, works for smooth payoffs, breaks for path-dependent
  early exercise.
- Control variates: powerful when correlated control exists. The control
  must have a known expectation; that's the constraint.
- Importance sampling: high-variance reductions for rare events (deep
  OTM, default probabilities). Hard to set up correctly.

### Convergence diagnostics

- Standard error scales as `1/sqrt(N)`. Halving SE costs 4× paths.
- Plot running mean and 95% CI as a function of paths. If it hasn't
  stabilised, you don't have enough paths.
- For early exercise (Longstaff-Schwartz): regression basis matters
  enormously. Test sensitivity.

---

## PDE pricing

### When to prefer PDE over Monte Carlo

- Low-dimensional (1-3 underlyings).
- Early exercise (American, Bermudan).
- Stable Greeks needed (PDE gives Greeks for free in the grid).
- Fast for repeated valuation across spots.

### Common bugs

- Boundary conditions wrong. Especially for barriers and early exercise.
- Grid not refined where the action is (near strike, near barrier).
- Time stepping at coupon dates / dividend dates: handle them as discrete
  events, not as continuous interpolation.
- Crank-Nicolson oscillations near payoff discontinuities. Fix with
  Rannacher start (a few implicit steps).

---

## Risk

### Greeks

Greeks are tools for hedging and exposure measurement, not just
sensitivities. Their meaning matters:
- Delta: hedge ratio for spot.
- Gamma: convexity; how much delta changes per spot move.
- Vega: vol exposure. Per absolute vol point or relative? Both used.
- Theta: time decay. Per calendar day or business day? Both used.
- Rho: rate exposure. Per absolute rate point or relative? Both used.

Always pin down the unit. "Vega is 0.5" is meaningless without it.

### VaR and ES

- Historical vs Monte Carlo vs parametric. Different assumptions, different
  failure modes.
- VaR is not subadditive in general. ES (expected shortfall, CVaR) is.
- Backtesting requires care — overlapping windows, holiday handling,
  P&L attribution.

---

## Yield curves

- Bootstrapping vs splines vs Nelson-Siegel-Svensson. Bootstrapping is
  exact at quoted instruments but can produce wiggles between. Smooth
  methods sacrifice exactness for shape.
- For single-curve (pre-2008) vs multi-curve (post-2008) frameworks:
  the multi-curve is now standard. OIS discount, forward curves per
  index tenor.
- Day count, compounding, and roll conventions vary by currency and
  market segment. Don't assume; look up.

---

<!-- expand from practice -->

Add patterns, gotchas, and reference values here as you encounter them.
Each entry: context, what to know, common mistake to avoid.
