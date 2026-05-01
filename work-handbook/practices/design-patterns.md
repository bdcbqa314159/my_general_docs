# Practice: design patterns

Patterns I reach for in quant / system / numerical work. Not GoF — those
exist; this is the working set that's actually useful day-to-day.

---

## Pure core, imperative shell

Separate the *math / decision logic* (pure functions, deterministic, easily
testable) from the *I/O and orchestration* (database, network, files, time).
The pure core is where most work goes; the shell is thin.

Pays off massively in:
- Testability (pure core needs no mocks).
- Reproducibility (pure core is deterministic given inputs).
- Reasoning (the hard logic is in one place, free of side-effect noise).

Antipattern: pricing functions that read from a database. Decouple them.

---

## Strategy / policy parameter

When a piece of code can vary by *method choice* (interpolation scheme,
optimisation algorithm, discretisation), pass the choice as a parameter
or object — not as a branch inside the function.

```
# Instead of: if method == 'cn': ... elif method == 'implicit': ...
# Pass: scheme: TimeSteppingScheme
```

Lets you test schemes independently and add new ones without touching
the orchestration.

---

## Builder for parameter-heavy objects

For objects that need many parameters (a calibrated model, a Monte Carlo
engine config), don't take them all in a constructor. Use a builder or
a config dataclass with sensible defaults. Constructors with 10 parameters
become call sites with 10 positional arguments; nobody can read those.

---

## Result type for fallible operations

Numerical methods can fail (didn't converge, ill-conditioned, NaN). Don't
return a sentinel value (`-1`, `NaN`) that the caller might forget to check.
Return a result type — `(success, value, diagnostics)` or an `Optional`
or an explicit `ConvergenceResult` object.

In Python: dataclass with `converged: bool`, `value: float`,
`iterations: int`, `residual: float`.
In C++: `std::expected<T, Error>` (C++23) or a custom result type.

---

## Caching layer at module boundary

Numerical functions are often called repeatedly with the same arguments
(calibration, sensitivity computation, repeated pricing). Cache at the
module boundary, not inside the function. Inside-the-function caching
hides the cost; boundary caching keeps the function pure and the cache
explicit.

`functools.lru_cache` in Python for simple cases; explicit cache objects
for anything with a non-trivial key.

---

## Two-phase initialisation: validate then construct

For complex objects with parameter constraints (vols positive, correlation
matrices PSD, etc.), validate parameters separately before constructing.
Returns errors closer to where they originated, and constructed objects
are guaranteed valid.

```
ParamSet.validate(raw) -> Result[ParamSet, ValidationErrors]
Model.from_params(ParamSet) -> Model  # always succeeds
```

---

## Adapter at the boundary of legacy code

When integrating with a legacy system or external library with a
problematic API, write a thin adapter rather than spreading the legacy
shape through your codebase. The adapter is the only place that knows
the legacy contract; everything inside the adapter speaks your contract.

Pays off doubly when the legacy thing eventually gets replaced.

---

## Numerical-specific: the analytical-fallback pattern

For methods that have a closed form in some regime and require numerics
elsewhere (e.g., series expansion for small parameter, asymptotic for
large), structure the code as:

```
if regime_A:
    return closed_form(...)
elif regime_B:
    return asymptotic(...)
else:
    return numerical(...)
```

Make the regime boundaries explicit and tested. The boundaries are where
bugs hide.

---

## Numerical-specific: dimensional separation

Keep the algorithm dimensionless when possible. Scale inputs in, scale
results out. The dimensionless core is testable against textbook cases;
the scaling is a thin layer.

Especially valuable in PDE solvers and optimisation routines where
condition numbers depend on scale.

---

<!-- expand from practice -->

Add patterns here as you notice yourself reaching for them. Each entry
should have: name, when-to-use, why-it-pays-off, and ideally an antipattern.
Don't add entries speculatively.
