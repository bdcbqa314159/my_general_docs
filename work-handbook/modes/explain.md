# Mode: explain

For docs, write-ups, and explaining concepts to self or others.

## Protocol

1. State the audience. "Quant team" vs "non-quant stakeholder" vs
   "future-me in six months" vs "new hire" — completely different docs.
2. State the goal. Reference doc / tutorial / decision record / runbook /
   onboarding material.
3. State what the reader already knows and what they don't. Mismatched
   level is the #1 reason explanations fail.
4. State the length budget. "One paragraph" / "one page" / "as long as it
   takes." Without this, the doc bloats.
5. Draft. Review. Iterate.

## What explain should always do

- **Lead with the answer.** Then justification, then derivation, then caveats.
  Most readers stop after the first paragraph; make it the load-bearing one.
- **Use the audience's vocabulary.** A doc for the quant team uses "implied
  vol" without defining it; a doc for engineering needs the definition.
- **Show, don't claim.** A worked example beats a paragraph of description.
- **Distinguish what's true from what's our convention.** "Black-Scholes
  assumes constant volatility" (true). "We use ACT/365 day count" (convention).
- **Name what's not covered.** A doc that pretends to be complete when it
  isn't is worse than one that flags its scope.

## What explain should not do

- Start with "In this document we will..." — wastes the most-read sentence.
- Pile on jargon to sound rigorous. Rigour is precision, not vocabulary.
- Explain things the audience already knows. Insulting and wastes space.
- Bury the decision in a wall of context. If it's a decision record, the
  decision goes first.

## Document types and their shapes

**Reference** — looked up, not read linearly. Optimise for skim and search.
Headers and tables, not narrative.

**Tutorial** — read once, linearly. Optimise for momentum. Each step builds
on the last; no forward references.

**Decision record** — *what* was decided, *why*, *what alternatives were
considered*, *what would change the decision*. The last one is the most
forgotten and most useful.

**Runbook** — followed under stress. Optimise for unambiguous instructions.
No prose, just steps. Include the "if X happens, do Y" branches.

**Concept explanation** — bridges from known to unknown. Start where the
reader is, end where they need to be. Don't skip rungs.
