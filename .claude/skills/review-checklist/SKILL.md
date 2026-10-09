---
name: review-checklist
description: Reviews a product brief against a fixed six-point checklist before it goes any further. Use when the user says "review this brief", "run the review checklist", "check this brief", or points at a brief file and asks if it is ready.
---

# Review checklist

Run the same check on any brief, every time. Read the whole brief first, then judge each point below.

## The checks

1. **Owner.** The brief names who owns it (a person or a named team). A role with no name, or "TBD", is a fail.
2. **Problem summary.** The problem is summarized in plain terms: what is wrong, and what evidence shows it. A list of features with no problem behind it is a fail.
3. **Who is affected.** The users, or anyone else affected, are named (for example responders, handlers, another product team). "Users" alone is a fail.
4. **Success measure.** The brief says how we will know it worked: a metric, a target or a signal to watch, plus a baseline if one exists. No measure, or a measure with no way to read it, is a fail.
5. **Scope consistency.** Compare the scope stated at the start (summary, ask, goals) with the scope at the end (requirements, features, phases, out-of-scope list). Flag anything that appears in one and not the other, or any growth in scope between them.
6. **Proposed fix.** The brief proposes a fix, or several options, in a way that fits what the brief knows. If the evidence is thin, a set of options with trade-offs passes. If the evidence is strong, one clear recommendation passes. A brief that states a problem and stops is a fail, and so is a single fix that the evidence does not support.

## Output

Give a short table first, one row per check: **Pass**, **Partial** or **Fail**, with a one-line reason that quotes or points to the part of the brief it is based on.

Then:

- **Gaps to close:** the specific questions the author must answer, in order of importance.
- **Verdict:** *Ready to go further*, *Ready with small fixes* or *Not ready*. Not ready means any Fail. Small fixes means Partials only.

## Rules

- Judge only what the brief says. Do not fill gaps from other files or from what you think the answer is; if you notice a conflict with other material, mention it after the table as a note.
- Do not rewrite the brief. Point at what is missing and let the author decide.
- If the user gives no file, ask which brief to review.
- Keep the same six checks and the same order every time, even when the brief is short.
