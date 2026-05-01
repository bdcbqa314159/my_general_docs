# HANDBOOK — Universal rules for work sessions

The rules below apply to every work session, regardless of mode. Mode-specific
protocols live in `modes/`. Reference knowledge lives in `domains/` and
`practices/`.

---

## The two hard constraints

**No sensitive data.** Sanitized code and math are fine. Real names, numbers,
tickers, positions, client identifiers, internal system names, internal URLs
are not. When in doubt, abstract first.

**Short sessions.** Treat context as expensive. Front-load what matters,
ask focused questions, avoid recap and preamble, close cleanly.

These two constraints drive most of the methodology below.

---

## Operating principles

**Critique by default, not on request.** When I propose something, push back
if there's a real reason — a numerical issue, a design smell, a pattern that
won't scale, a math error. Hedged pushback is worse than none. Say it plainly.

**Context-first, then question.** Every session opens with the preamble.
Every meaningful turn states what mode we're in and what the goal is. Don't
let context get inferred silently — it'll be inferred wrong.

**Abstract before paste.** Sanitization is a thinking discipline, not a
workaround. Reducing a problem to its structural form is half the work
already. See `practices/data-discipline.md`.

**Skills emerge from practice, not speculation.** Don't write entries in
`domains/` or `practices/` for patterns that haven't proven themselves through
real use. When a method gets used the third time, it earns a place in the tree.

**Numerical work demands explicit checks.** Floating point, conditioning,
stability, convergence — these are not advanced topics, they are the default
failure modes of every numerical method. See `domains/numerical-methods.md`.

---

## The six modes

Each mode has its own protocol in `modes/`. The mode is declared at the start
of any non-trivial turn. The modes:

- **review** — reviewing or refactoring existing code
- **design** — system components, architecture, interfaces
- **debug-numerical** — code that runs but produces wrong / unstable / divergent results
- **implement-spec** — translating a paper, spec, or mathematical method into code
- **math-check** — sanity-checking derivations, proofs, formulae
- **explain** — docs, write-ups, explaining a concept to self or others

The modes are not exclusive — a single session may shift between them — but
declaring the active mode keeps the response shape appropriate.

---

## Session protocol

**Open:** paste the preamble. Declare mode and goal.

**Work:** keep turns dense. One concrete question or one concrete artefact per
turn. If the conversation drifts into clarification ping-pong, stop and
re-state the goal in one sentence.

**Close:** before the session ends, ask for a one-line summary of what was
decided or produced. Capture it externally if it matters — sessions don't
persist.

---

## Things to avoid

- Pasting anything sensitive. If unsure, sanitize and proceed; don't ask permission.
- Long preambles, recaps, or "as a quant developer working on..." framing — wastes context.
- Hedged pushback ("you might consider possibly..."). Say it plainly or say nothing.
- Mode-less requests. "Help me with this code" is too vague to answer well — specify review / debug / refactor / extend.
- Generating large code blocks before constraints are stated. Get the constraints first.
- Assuming the workplace Claude has tools (web search, code execution, file access). It usually doesn't. Ask before relying on them.
- Trusting numerical output without a sanity check. See `domains/numerical-methods.md`.

---

## Maintenance

This file is the stable layer. Edit when a rule genuinely changes, not on a
whim. Keep the modes count fixed unless a genuinely new activity emerges.
The constraints change rarely — when they do, update the preamble too.
