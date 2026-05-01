# Practice: solid principles

Durable software principles that apply across languages and domains.
Reference, not exhaustive — the entries here have proven their worth in
practice.

---

## Naming

- A name should answer "what is this?" — not "where is this?" or "how was
  this implemented?".
- If the name needs a comment to explain what it means, the name is wrong.
- The length of a name should be proportional to its scope. Loop counters
  can be `i`. Module-level constants cannot.
- Boolean names should be predicates (`is_valid`, `has_converged`),
  not commands (`validate`, `converge`).
- If you can't name a thing, you don't understand it. Stop coding and
  think.

## Functions

- A function does one thing. If you describe it with "and," split it.
- Pure functions (no side effects, deterministic) are infinitely easier
  to test, reason about, and refactor than impure ones. Default to pure.
- A function with more than 3-4 parameters is signalling that it wants
  to be a struct/dataclass/object.
- Long functions are not always wrong, but they are always suspect.

## Coupling and cohesion

- High cohesion within a module, low coupling between modules. The two
  goals reinforce each other.
- A change to module A should rarely require a change to module B. When
  it does, the boundary is wrong.
- Coupling through shared mutable state is the worst kind. Coupling through
  a stable interface is the best.

## Abstraction

- Two examples is not enough to abstract from. Wait for the third.
- Premature abstraction creates wrong abstractions, which are worse than
  duplication.
- "Don't repeat yourself" is about *knowledge*, not *characters*. Two
  similar-looking blocks of code that represent different ideas should
  not be merged.

## Error handling

- Errors are part of the interface, not an afterthought. Decide at design
  time how each function communicates failure.
- Don't catch exceptions you can't handle meaningfully. Catching and
  logging is rarely meaningful handling.
- Fail fast and loud at boundaries; fail gracefully within.
- Distinguish *expected* failures (the user typed bad input) from
  *unexpected* ones (the disk is full). Different code paths.

## State

- Mutable state is the source of most bugs. Minimise it.
- Where state must exist, localise it. A small bag of mutable state inside
  a function is fine; a global one is a tax on every reader.
- Persistent state (files, DBs) deserves more design than in-memory state.
  Schemas, versions, migrations — none of these are afterthoughts.

## Dependencies

- Every dependency is a future maintenance burden. Add deliberately.
- Pin versions. "It worked yesterday" without pinning is luck.
- A small standard-library solution beats a large dependency for anything
  non-critical-path.

## Testing

- Tests document behaviour. Read the tests before reading the code.
- A test that can pass when the code is wrong is worse than no test.
- For numerical code: test against a known case, test the limit cases,
  test convergence, test edge inputs. Don't just test the happy path.
- Property-based tests find bugs unit tests miss. Worth the learning curve.

## Performance

- Measure before optimising. Most code does not need to be fast; identify
  the small fraction that does.
- The biggest wins are usually algorithmic, not micro-optimisations.
- For numerical work specifically: the order of magnitude wins come from
  vectorisation, the right linear algebra primitives, and avoiding
  unnecessary copies. Then profile.

## Comments

- Comments explain *why*, not *what*. The code shows what.
- A comment that contradicts the code is a bug waiting to be found.
- Stale comments are worse than no comments. Treat them as code: review
  them, update them, delete them when wrong.

## Decisions

- Record the decision, the alternatives, and what would change the decision.
  The third part is the most useful and the most forgotten.
- A decision made by drift is worse than one made by debate. If you can't
  remember why you chose X over Y, you've already lost.
