---
name: robot-ears
description: Write robot EARS requirement lines with units and ranges that compile with npx --no-install golem-spec check. Use it when writing or editing acceptance criteria for a robot, or when npx --no-install golem-spec check reports a compile error.
---

# Write robot EARS lines

A robot requirement is one EARS line per acceptance criterion. GOLEM compiles it into a scenario space (what the falsifier may vary) and a monitor (what must hold). Write the line, run `npx --no-install golem-spec check` or the `check_requirement` tool, and fix every error before you save.

## The five patterns

- Ubiquitous: `THE robot SHALL <response>`. The response holds for the whole episode.
- Event: `WHEN <trigger> THE robot SHALL <response>`. The trigger is injected, and the response is checked in the window that follows.
- State: `WHILE <state> THE robot SHALL <response>`. At every instant, if the state holds, the response holds.
- Unwanted: `IF <fault> THEN THE robot SHALL <response>`. The fault is injected like a trigger.
- Optional: `WHERE <feature> THE robot SHALL <response>`. It applies only when the app enables the feature.

## Units

Every number carries a unit. The accepted units are N, kN, m, cm, mm, s, ms, m/s, cm/s, deg, °, rad, kg, g and %. Counts and declared ratios are the only numbers without a unit.

## Ranges define the search space

A range in a trigger is what the falsifier explores. `with up to 100 N` searches 0 N to 100 N. `with 50 to 150 N` searches 50 N to 150 N. A fixed value such as `for 1 s` is not searched. Put the harshest believable value in the trigger and let the falsifier find the boundary.

## Examples

These lines use the phrases of the small test app (a base that is pushed, a height and a tilt). Use the phrases from your own app's `lexicon.ts`.

```ears
THE robot SHALL keep its tilt below 0.5 rad for 2 s
```

```ears
WHEN the base is pushed with up to 100 N THE robot SHALL keep its height above 0.4 m for 1 s
```

```ears
WHEN the base is pushed with 50 to 150 N THE robot SHALL keep its tilt below 0.5 rad for 1 s
```

```ears
IF the base is pushed with up to 100 N THEN THE robot SHALL keep its height above 0.4 m for 1 s
```

## When a line does not compile

Read the diagnostic: it gives `file:line:col`, the reason and the closest template. Edit the line toward that template. Never guess a phrase, and never change a requirement to make a test pass: if a requirement seems wrong, propose a change with `propose_requirement`.
