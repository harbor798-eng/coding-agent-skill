# Discovery Result Template

> This is a **recording template**, not a workflow.
> It does not tell the Agent *how* to discover a project (that is `project-discovery.md`). It defines the **shape of the result** that discovery hands to `implementation.md`.
>
> Do not add discovery steps, search strategies, retry rules, or decision logic here. Those belong in `project-discovery.md`.

---

## How to use

1. Run `project-discovery.md` through to its sufficiency check.
2. Copy the skeleton below.
3. Fill every field using the Facts / Assumptions / Unknowns lists you already built during discovery. Do not invent new content here.
4. Choose exactly one Discovery conclusion.
5. Hand the filled result to `implementation.md`.

Do not fill this template during investigation. It is the output, not a scratchpad.

---

## Result skeleton

```text
Task:
    <one sentence>

Goal:
    <what the user actually wants to achieve, beyond the literal instruction>

Expected result:
    <the observable change that proves the goal is met>

Scope:
    [ ] small
    [ ] cross-module
    [ ] architectural

Relevant components:
    - <file or module>: <why relevant to this task>
    - ...

Current behavior:
    - <what the relevant code does today, observed not assumed>
    - ...

Relevant dependencies:
    - upstream:    <what calls into this area>
    - downstream:  <what this area calls>
    - config:      <configuration / environment that affects behavior>
    - entry point: <how this area is exercised at runtime>

Existing verification:
    - <tests / run steps / manual checks already available for this area>

Facts:
    - <claim> — evidence: <where it was observed>
    - ...

Assumptions:
    - <claim> — why believed: <reason>
    - ...

Relevant unknowns resolved during discovery:
    - <unknown> — resolved by: <action taken>

Known irrelevant unknowns (recorded, not blocking):
    - <unknown>

Risks / constraints:
    - <what could break when we change this area>
    - <what must stay unchanged>

Discovery conclusion (choose exactly one):
    [ ] READY_FOR_IMPLEMENTATION
    [ ] NEEDS_MORE_DISCOVERY
          Next targeted step: <the single investigation that would resolve the open unknown>
    [ ] BLOCKED_OR_ESCALATE
          Escalation summary: <use the SKILL.md §8 escalation format>
```

---

## Field rules

These are filling rules, not discovery rules.

- **Facts must carry evidence.** If you cannot point to where you observed it, the claim is an Assumption, not a Fact.
- **Assumptions must be labeled as such.** Do not bury them in the Facts list. `implementation.md` will verify them early.
- **Relevant unknowns must be resolved** before choosing `READY_FOR_IMPLEMENTATION`. Irrelevant unknowns may be recorded but are not chased.
- **Discovery conclusion is exactly one value.** If the result does not cleanly fit any of the three, discovery is not finished yet — return to `project-discovery.md`.
- **Do not smuggle new decisions into this template.** If you discover a requirement change, architecture decision, or destructive operation, that goes into `BLOCKED_OR_ESCALATE` with the SKILL.md §8 format — not into Risks / constraints.

---

## Hand-off

This result is consumed by `workflows/implementation.md`:

- `READY_FOR_IMPLEMENTATION` → implementation proceeds from this result instead of re-scanning the project.
- `NEEDS_MORE_DISCOVERY` → return to `project-discovery.md` with the "Next targeted step".
- `BLOCKED_OR_ESCALATE` → resolve the escalation path before any implementation begins.

If, during implementation, new facts contradict this result, the implementation workflow runs reconciliation and may return here. This result is a snapshot, not a permanent truth.
