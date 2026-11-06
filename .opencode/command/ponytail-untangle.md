---
description: Refactor an explicitly selected area and verify behavior
---

# Ponytail Untangle

Refactor the requested area so pieces that change together live together,
unrelated changes stay independent, and unnecessary code disappears.
This is a one-shot implementation workflow, not an audit or a persistent mode.

## 1. Bound the work

- Use the area or concern the user names. With no argument, use the concrete
  target already established in the conversation; if none exists, ask for it.
  Never silently choose the whole repository.
- A whole-repository refactor requires an explicit request.
- Read the project's instructions and preserve unrelated working-tree changes.
  Inspect callers, configuration, exports and tests across the boundary.
  Change outside the named area only where required to keep those callers working.
- Preserve observable behavior, public contracts, validation and error handling.
  Ask before changing behavior, expanding scope, or running a destructive migration.
- Do not create a persistent goal or start an unattended loop.
- Comment cleanup and test cleanup are separate, opt-in work. Do neither unless
  explicitly requested. Updating tests broken by this refactor is still required;
  follow the project's testing rules.

## 2. Find the structural problems before editing

Read the relevant execution flows and ask: should these pieces change together?

- **Hidden links:** duplicated rules, implicit shared state or call order,
  and one feature scattered across modules that must be changed together.
- **Needless links:** callers relying on internals, stable code depending on
  volatile details, and general interfaces shaped by one caller.
- **Surplus:** dead code, pass-through wrappers, and extension points for
  requirements that do not exist.

Record concrete file and symbol references and the change each problem makes
harder. Check dynamic references before calling code unused. Use Git history
when it helps establish what actually changes together; similarity alone is
not a reason to merge code.

Inspect special cases: value-specific branches, caller-specific parameters,
swallowed exceptions, retries, sleeps and disabled tests. These are clues,
not automatic defects. Keep the ones required by the real system.

## 3. Fix shared causes

Keep a short decision log in the conversation or the project's existing task
record. Group problems under the structural fix that removes their shared cause.

For each group, describe the smallest design that fits today's requirements:
what moves together, which dependency disappears, and which special cases go.
Prefer existing mechanisms and deletion over new layers. Do not redesign
unaffected code or introduce flexibility for hypothetical future requirements.

Implement each group completely, including affected callers, configuration,
exports and necessary test updates. Keep safety checks when moving code.
Fix defects introduced by your edits; report unrelated bugs without turning
this into a separate bug hunt.

If comment cleanup was requested, remove comments that only restate the code
and keep constraints the code cannot express. If test cleanup was requested,
do it after the structural changes: keep regression protection, replace checks
tied to internals with behavior checks, and remove only tests shown to catch
no real regression. Never delete or disable a failing test just to get green.

## 4. Verify and finish

Run proportionate behavior checks at the changed boundaries using the
project's existing verification approach. Check representative success and
failure behavior, not just that the code loads.

Review the changed area once more for the three problem types and fix issues
caused by the refactor. Subagents are optional, never a completion requirement.
Finish when the scoped decision log is resolved and the behavior checks pass,
or report a concrete blocker. Do not keep searching the repository until
there is nothing left to improve.

Report what was simplified, what behavior was checked, and only material
remaining constraints. Leave unrelated findings for a separate request.

Inspired by [Caldo's untangle-codebase](https://github.com/caldoai/skills/blob/main/skills/untangle-codebase/SKILL.md),
adapted to Ponytail's smallest-complete-change boundary.
