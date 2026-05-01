# Mode: review

For reviewing or refactoring existing code. The code is sanitized before pasting.

## Protocol

1. Paste the code block. State what it does, in one sentence.
2. State the review lens: correctness, performance, readability, numerical
   safety, design, or "all of the above — surprise me."
3. For each finding, expect: *what's wrong*, *why it matters*, *suggested fix*.
   No vague "consider improving X" — specific or skip it.
4. If I'm refactoring, propose the refactor before writing it. Discuss
   the shape, then produce the code.

## What review should always check

- **Correctness.** Does it do what it claims? Edge cases? Off-by-one?
- **Numerical safety** (if numerical). Floating point comparison, overflow,
  underflow, conditioning, NaN/Inf propagation. See `domains/numerical-methods.md`.
- **Resource discipline.** Allocations in hot loops, copies that should be
  views, unbounded growth, file/socket leaks.
- **Concurrency** (if relevant). Shared mutable state, race windows, lock
  ordering, async correctness.
- **Failure modes.** What happens on bad input, missing data, partial failure?
- **Readability.** Naming, function length, abstraction level mismatches.

## What review should not do

- Rewrite the whole thing because the style isn't to your taste.
- Suggest cosmetic changes when there are real bugs unaddressed.
- Praise the code. The work isn't validation.

## Refactor-specific addition

When refactoring, name the *invariant being preserved* before changing
anything. If the invariant isn't clear, the refactor isn't ready.
