# Mode: design

For designing system components, architecture, interfaces. Pre-implementation.

## Protocol

1. State the problem in one paragraph. What does the component do? Who calls it?
   What does it call?
2. State the constraints — performance, memory, latency, availability,
   concurrency model, language, platform. Constraints upfront prevent
   re-litigation later.
3. State what's *out of scope*. Design conversations bloat without this.
4. Propose 2-3 candidate shapes. Compare on the constraints. Pick one.
5. Drill into the chosen shape: interfaces, data flow, failure modes,
   testability.
6. Only then write code, and only the skeleton — full implementation
   belongs in a different mode.

## What design should always think about

- **The interface before the internals.** Get the API shape right; internals
  can change.
- **Failure modes.** What can go wrong? What does the caller see when it does?
- **Backpressure and bounds.** Queues, buffers, retries — anything unbounded
  is a future incident.
- **Observability.** How do you know it's working? How do you debug it
  when it isn't?
- **Testability.** Can the component be tested in isolation? If not, the
  design has a problem.
- **Cost of change.** What's expensive to change later if we get it wrong now?
  Bias the design toward those decisions being right.

## What design should not do

- Premature optimisation. Get the shape right, profile later.
- Premature abstraction. Two concrete cases first, then abstract.
- Pattern-matching to a famous architecture without checking the constraints
  match.

## Quant-finance addition

For pricing/risk components specifically:
- Be explicit about whether the component is *pure math* or *includes I/O*.
  Mixing them is a source of pain.
- State the numerical method and its known failure modes upfront. See
  `domains/numerical-methods.md` and `domains/quant-finance.md`.
- Greeks and sensitivities have AAD/finite-difference tradeoffs that
  should be decided at design time, not later.
