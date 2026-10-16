---
name: review-checklist
description: Reviews a product brief against the PM's six-point checklist before it goes any further. Use when the user says "review this brief", "run the review checklist", "check this brief", or points at a brief file or pasted brief and wants the standard check.
---

# Review checklist

Run the same six checks, in this order, on whatever brief the user points at (a file path, a pasted brief, or a wiki page). If no brief is named, ask which one. Read the whole brief first, then check.

## The six checks

1. **Owner.** The brief names who owns it: a specific person or role, not a team or "TBD". If several people are named, it says who decides.
2. **Success.** The brief says how we will know it worked: a measure or an observable outcome, with a rough target or direction and when it will be looked at. "Improve the experience" fails.
3. **Scope matches.** Compare the scope stated at the start with what the brief ends up covering (the proposal, the list of changes, any "next steps" or "later" section). Flag anything added at the end that was not in the opening scope, and anything promised at the start that never appears again.
4. **Problem before fix.** The problem is explained before any solution is proposed. Flag a brief that opens with the solution, or whose problem section only restates the solution. The problem should say who is affected and what happens to them, ideally with evidence.
5. **Fix is about the customer experience.** Any proposed fix describes what the customer sees, does or gets, in plain words. Flag fixes written as technical design (config values, schema, code paths, architecture) when no customer-facing description comes with them. Technical detail is fine as a supporting note, but the customer experience has to be stated first.
6. **Teams involved.** The brief names every team needed to make this possible (for example engineering, design, data, support, other products that depend on it, security or legal) and says what each is being asked to do. For each team ask: Is the ask specific? Is a named person or role the contact? Has the team been told or agreed, or is that still open? Is the team's capacity or timing mentioned? Flag teams the proposed changes clearly depend on but the brief never mentions, and teams listed with no ask.

## Success metric questions

Check 2 is not done when a metric merely exists. Put these questions to every success metric in the brief. In the report, list the ones the brief does not answer, under the Success row.

- **What is it?** Is the metric defined exactly: numerator, denominator, who is counted, over what period? Can two people compute it and get the same number?
- **Where does it start?** Is there a baseline, and where did it come from? If it is a guess or a replay rather than measured data, is that said?
- **What counts as success?** Is there a target or a direction with a size, not "toward" or "improve"? Who chose it and why?
- **By when, and who looks?** Is there a date or window for the check, and a named person or role who reads the number?
- **Does it measure the problem?** Does the metric move only if the problem named in the brief is fixed, or could it move for other reasons (seasonality, volume, a different release)?
- **Can we get it?** Does the data exist today, who owns it, and does anything need to be logged or built first?
- **What must not get worse?** Are there guardrail metrics, and do they have limits?
- **What would make us stop or change course?** Is there a threshold at which the fix is judged not to be working?
- **Is it about the customer?** Does at least one metric reflect what customers experience, not only internal activity such as releases shipped or features used?

## How to report

Start with a one-line verdict: **Ready**, **Ready with fixes**, or **Not ready**. Not ready means any check failed outright.

Then a table with one row per check: **Pass**, **Partial** or **Fail**, plus a short reason.

Under the Success row, list the unanswered questions from "Success metric questions".

Then, for every Partial or Fail, quote or point to the exact line or section in the brief, and say what to change in one sentence.

## Rules

- Judge only what is written in the brief. Do not fill gaps from other sources; a missing owner is a fail even if you know who it should be.
- Do not rewrite the brief unless asked. Report, then offer.
- Be consistent: same six checks, same order, same format every time, even when the brief is short or looks fine.
- If a check does not apply (for example, a brief that proposes no fix), say "N/A" and why instead of passing it.
