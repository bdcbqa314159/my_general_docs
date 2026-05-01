# Domain: Python

Idioms, traps, and reference for the numpy/scipy/pandas stack.
Grows with use.

---

## numpy

### Things that are slower than they look

- Python-level loops over arrays. Vectorise instead.
- `np.append` in a loop. O(n²). Build a list and convert at the end,
  or preallocate.
- `np.concatenate` in a loop. Same problem.
- Repeated `.iloc` or `.loc` access in pandas. Vectorise.

### Things that are faster than they look

- Boolean masking: `a[a > 0]` is vectorised.
- `np.where(cond, a, b)` for vectorised conditionals.
- `np.einsum` for tensor contractions; often faster and clearer than
  chained transposes and matmuls.
- BLAS-backed operations (matmul, dot, solve) for anything large.

### Memory traps

- Slicing returns a *view*, not a copy. `b = a[2:5]; b[0] = 99` modifies
  `a`. Sometimes desired, sometimes a bug.
- Fancy indexing (`a[[1, 3, 5]]`) returns a *copy*.
- `.flatten()` copies; `.ravel()` returns a view if possible.
- Use `.copy()` when you need a copy. Don't hope.

### dtypes

- Default float is `float64`. Default int is platform-dependent
  (`int32` or `int64`). Pin explicitly when it matters.
- Integer overflow is silent in numpy (unlike Python ints).
  `np.int32(2**30) * np.int32(4)` overflows to negative.
- Mixed int/float operations promote to float. Check the result dtype.

### Broadcasting

- Powerful, but a frequent source of subtle bugs.
- Two arrays broadcast iff their shapes are compatible *trailing-aligned*.
  Shapes `(3, 1)` and `(4,)` give `(3, 4)`.
- Use `a[:, None]` and `b[None, :]` to make broadcasting explicit at
  the call site rather than relying on inferred alignment.

---

## scipy

### Optimisation

- `scipy.optimize.minimize` defaults to BFGS. For bounded problems use
  L-BFGS-B. For non-smooth, Nelder-Mead. For global, basin-hopping or
  differential evolution.
- Pass the Jacobian when you have one. Massive speedup, especially in
  high dimensions.
- Always check `result.success` and `result.message`. The optimiser
  failing silently is the classic bug.
- `least_squares` is better than `minimize` for least-squares problems
  (specialised methods, better diagnostics).

### Linear algebra

- `scipy.linalg` has more options than `numpy.linalg` (banded, sparse,
  specialised solvers). Prefer when relevant.
- `scipy.sparse.linalg.spsolve` for sparse linear systems.
- `scipy.linalg.lu_factor` + `lu_solve` when solving with the same matrix
  many times. Cache the factorisation.

### Integration

- `scipy.integrate.quad` for 1D, adaptive. Good default.
- `dblquad`, `tplquad` for 2D, 3D. Beyond that, use `nquad` or sample
  with Monte Carlo.
- For ODEs, `solve_ivp` (modern API). Pick the method by stiffness:
  `RK45` non-stiff, `BDF` or `Radau` stiff.

---

## pandas

### Performance

- Use vectorised operations. `.apply` with a Python function is slow.
- For row-by-row logic that can't vectorise, use `.itertuples()` not
  `.iterrows()`. `iterrows` is dramatically slower.
- `df.loc[df.col > 0, 'other'] = ...` is the canonical conditional update.
- Avoid `.append` in a loop. Build a list of dataframes, concat once.

### Indexing pitfalls

- Setting on a chained selection (`df[mask]['col'] = x`) is a SettingWithCopy
  warning waiting to happen. Use `.loc` for combined selection and assignment.
- `df.iloc` is positional; `df.loc` is label-based. Confusing them on a
  default-integer-indexed dataframe is fine; on a dataframe with a custom
  index, it's a bug.
- `pd.MultiIndex` is powerful and confusing. `xs`, `slice(None)`, and the
  `IndexSlice` helper are worth learning.

### Time series

- `DatetimeIndex` is the right choice for time-indexed data.
- `df.resample('D').agg(...)` for downsampling. Mind which side the bin
  is closed on (`closed='left'` vs `'right'`) — different conventions for
  different markets.
- Timezones: be explicit. `tz_localize` for naive → aware,
  `tz_convert` for aware → different aware. Mixing naive and aware
  datetimes errors loudly.

---

## Idioms and conventions

- Use type hints in new code. Even basic ones (`np.ndarray`, `pd.DataFrame`,
  `dict[str, float]`) document intent.
- `dataclass(frozen=True)` for value types. Default to immutable.
- `pathlib.Path` not `os.path`. Cleaner, cross-platform.
- f-strings for formatting, not `%` or `.format()`.
- Context managers for resources (`with open(...) as f:`).
- `enumerate` not `range(len(...))`.

---

## Testing

- `pytest` is the de facto standard. `unittest` only if you must.
- `pytest.fixture` for setup, `parametrize` for testing the same logic
  with different inputs.
- For numerical tests: `np.testing.assert_allclose(actual, expected, rtol=...)`
  not `assert_equal`.
- `hypothesis` for property-based testing — finds edge cases you didn't
  think of.

---

<!-- expand from practice -->

Add Python-specific patterns, gotchas, and library notes as you encounter them.
