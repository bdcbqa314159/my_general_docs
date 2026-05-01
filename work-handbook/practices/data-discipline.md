# Practice: data discipline

How to work productively with Claude when sensitive/internal data can't
be pasted. The defining constraint of the work environment.

---

## The principle

**Sanitization is a thinking discipline, not a workaround.**

Reducing a problem to its abstract form forces you to identify what's
structurally going on. That's half the work of solving it. People who treat
sanitization as friction miss this — people who treat it as a clarifying
step get faster at problem-solving generally.

---

## What to strip

Always:
- Real client / counterparty names. Replace with `Client A`, `CounterpartyX`.
- Real ticker symbols. Replace with `ASSET1`, `ASSET2` or generic `X`, `Y`.
- Real position sizes, prices, P&L numbers. Replace with order-of-magnitude
  placeholders or symbols.
- Internal system names, hostnames, URLs, file paths if they reveal structure.
- Real database names, table names, column names if they're descriptive
  of business content.
- Employee names, internal team names.

Usually:
- Real currency pairs if the question isn't currency-specific. `CCY1/CCY2`
  is fine.
- Real instrument identifiers (CUSIPs, ISINs, internal IDs).
- Real timestamps that could pin down a specific trade or event.

Never necessary to strip:
- The math. Equations are equations.
- The numerical method. Crank-Nicolson is Crank-Nicolson.
- The library, language, framework.
- The general shape of the architecture.
- Public knowledge about the field.

---

## What to keep

The structural shape of the problem must survive sanitization:
- Data types and dtypes (`float64` vs `float32` matters for numerics).
- Array shapes (`(N, M)` vs `(M, N)` matters).
- Order of operations.
- Control flow.
- The numerical method and its parameters (step size, tolerance, iteration count).
- Anything causally related to the bug or design question.

The test of a good sanitization: *if the bug reproduces in your real code,
it should reproduce in the abstracted version*. If it doesn't, you stripped
something causal.

---

## Three patterns for paste-ready problems

**Pattern 1: The sanitized snippet.** Strip identifiers, keep code structure.
Best for review and debug modes. Replace strings, keep all logic.

**Pattern 2: The minimal working example (MWE).** Reduce the code to the
smallest version that still exhibits the issue or asks the design question.
Best when the original is large and most of it is irrelevant.

**Pattern 3: The shadow problem.** Pose a structurally identical problem in
a different domain. ("I have a function that takes a 2D array of measurements
and returns a calibrated parameter set...") Best when even the abstracted
version reveals too much, or when the real problem has too much domain context.

---

## Quick sanitization checklist

Before pasting, scan for:
- [ ] Names of real people, clients, products, internal systems
- [ ] Real numerical values that could identify a position or trade
- [ ] File paths that reveal directory structure
- [ ] URLs of internal services
- [ ] Database / table / column names with business meaning
- [ ] Comments that mention business context
- [ ] Variable names like `ACME_BOOK` that encode internal taxonomy

If any are present, replace before paste. The replacement should be
*structurally faithful* — same length, same type, same shape — but
content-free.

---

## When sanitization is too expensive

Sometimes the problem is so tangled with internal context that abstracting
it would take longer than solving it yourself. In that case:
- Solve it yourself, or
- Discuss the *general technique* with Claude, then apply it to the real
  problem offline.

This is fine. The handbook isn't a mandate to use Claude for everything —
it's a guide for using Claude well when you do.
