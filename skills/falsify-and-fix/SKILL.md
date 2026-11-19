---
name: falsify-and-fix
description: Repair a robot controller until its requirements hold. Use it when a requirement is red, a npx --no-install golem-spec test or npx --no-install golem-spec gate run reports a counterexample, or a gate blocked the stop.
---

# Falsify and fix

Follow these six steps, in this order, until `npx --no-install golem-spec gate` exits with code 0.

1. Falsify. Call the `falsify` tool on the red requirement, or run `npx --no-install golem-spec falsify <id>`. It searches for the mildest scenario that breaks the requirement and pins it as a regression seed.
2. Read the counterexample. Call `get_counterexample`. Note the scenario with its units, the seed, and the margin with its unit. A negative margin is how far the requirement broke.
3. Explain the margin. Call `explain_margin` to see which signal has the lowest margin and at what time. Call `replay` for a 10 Hz summary of the trace.
4. Fix. Change the controller (files under `controller/` or `tuning/`) so that the margin of that signal rises. Make the smallest change that does it.
5. Re-run. Call `falsify` again with the same budget, then `run_requirement` on the other requirements to check that nothing else broke. The pinned seed runs first, so the old counterexample is checked again.
6. Gate. Run `npx --no-install golem-spec gate`. Exit code 0 means every requirement held and no pinned seed fails: the loop ends. Exit code 2 means a requirement is still red: go back to step 1. Exit code 1 means a usage, compile or runtime error: report it instead of retrying.

## Rules

- Requirement files are never edited to make a test pass. The controller is changed instead. If a requirement seems wrong or too harsh, propose a change with `propose_requirement` and tell the user.
- A `held` verdict means no counterexample was found within the stated budget. It is not a proof. State the budget when you report it.
- Give a unit on every physical number you report.
