# Work Handbook

A personal knowledge tree for collaborating with the workplace Claude
(via Copilot integration) under two hard constraints:

1. **No sensitive or internal data may be pasted.** Sanitized code and math
   are fine; real names, numbers, tickers, positions, identifiers are not.
2. **Sessions feel short.** Optimise for information density per turn.
   Front-load context, ask focused questions, close cleanly.

---

## Layout

```
work-handbook/
├── README.md                  # this file
├── HANDBOOK.md                # universal rules across all work sessions
├── preamble.md                # paste-this-at-session-start, condensed
├── modes/                     # protocols per activity type
│   ├── review.md
│   ├── design.md
│   ├── debug-numerical.md
│   ├── implement-spec.md
│   ├── math-check.md
│   └── explain.md
├── domains/                   # knowledge ABOUT a thing — grows from practice
│   ├── numerical-methods.md
│   ├── quant-finance.md
│   ├── python.md
│   ├── cpp.md
│   ├── csharp.md
│   ├── linux.md
│   └── windows.md
└── practices/                 # knowledge about HOW to work — cross-cutting
    ├── data-discipline.md
    ├── design-patterns.md
    ├── critique-protocol.md
    └── principles.md
```

---

## How to use this tree

**Start of a work session:** copy the contents of `preamble.md` into the chat.
That establishes constraints, mode, and the universal rules.

**Mid-session, switching activity:** declare the new mode by name
("switching to debug-numerical mode"). The mode protocols live in `modes/`.

**Looking something up:** `domains/` for language/OS/field knowledge,
`practices/` for cross-cutting methodology.

**Adding to the tree:** when you find yourself reaching for the same answer
or pattern twice, codify it. Don't write speculative entries. The tree grows
from your practice, not from my imagination.

---

## Maintenance

- `HANDBOOK.md` and `preamble.md` are the stable layer. Edit when something
  genuinely changes, not on a whim.
- `modes/` files are short by design. Keep them short.
- `domains/` files start as scaffolds and grow with use. Many sections are
  placeholders marked `<!-- expand from practice -->`. Fill in as you encounter
  the relevant problem.
- `practices/` files are durable. Edit slowly, with reason.
- Review the tree quarterly: prune entries that no longer apply, promote
  patterns that have proven themselves.

---

## Relationship to the personal handbook

This is the **work** counterpart to your personal `~/.claude/` setup.
The methodology (modes, lenses, skill-emergence rule) is shared. What
differs: the constraints, the domain weight (numerical/quant), and the
fact that nothing here can be auto-loaded — everything is paste-in-context.
