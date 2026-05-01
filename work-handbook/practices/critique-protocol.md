# Practice: critique protocol

How to ask Claude to challenge your work, and how to evaluate the response.

---

## The principle

**Critique is the highest-leverage use of Claude in technical work.**

Claude is fast, has read widely, has no ego in your design, and will name
problems you've stopped seeing. But it also has a strong drift toward
agreement and will gladly tell you a bad idea is good if you frame it
that way. The protocol is about counteracting that drift.

---

## How to ask for critique well

**Bad:** "What do you think of this approach?"
*Why it fails:* primes agreement. Claude tends toward affirmation when
asked for an opinion neutrally.

**Better:** "Critique this approach. What's wrong with it? What did I miss?"
*Why it's better:* explicitly asks for problems. Lower threshold for
naming issues.

**Best:** "Steelman the case for *not* doing this. Then steelman the case
for the opposite approach."
*Why it's best:* forces structured opposition. Claude has to articulate
the strongest version of disagreement, not just gesture at concerns.

---

## The three-pass critique

For anything load-bearing — a design decision, a numerical method choice,
a key piece of code — run three passes:

**Pass 1: structural.** Is the approach right at all? Are we solving the
right problem? Are there hidden assumptions?

**Pass 2: implementation.** Granted the approach, is the implementation
correct? Edge cases, failure modes, numerical safety, performance.

**Pass 3: alternatives.** What would someone with different priors do? Is
there a simpler / more standard / more robust way?

Each pass takes a separate turn. Don't conflate them.

---

## The lenses (compressed)

Borrowed from the personal handbook. When reviewing anything:

- **Surface** — does the thing do what it claims? Tests pass? Behaviour
  matches spec?
- **Structure** — is the design sound? Coupling, cohesion, abstraction
  level, failure isolation?
- **Foundation** — are the underlying assumptions correct? Numerical
  stability, mathematical validity, conceptual coherence?

Most problems hide at the foundation. Always check there.

---

## Counteracting agreement drift

Claude has a tendency to:
- Soften disagreement.
- Frame critique as "consideration."
- Find merit in any approach you propose.

To counteract:
- Ask explicitly for the case against, not "thoughts."
- Ask "what would change your mind that this is wrong?" — if Claude can't
  state what evidence would falsify its agreement, the agreement is empty.
- If Claude's first response is largely positive, ask "now critique
  yourself — what did you miss in that response?"
- Periodically test by proposing a deliberately bad idea. If Claude
  agrees, recalibrate.

---

## What to do with critique

Not every critique is right. Claude will sometimes:
- Apply a pattern that doesn't fit your domain.
- Flag a "best practice" that's not best for your case.
- Miss context that would change the assessment.

The protocol:
1. Take every critique seriously enough to engage with it.
2. Refute it explicitly if you disagree — *to Claude*, not just internally.
   This forces you to articulate why, which is itself useful.
3. If your refutation is weak, the critique probably has merit.
4. Keep the critiques you accepted. They're learning material for next time.
