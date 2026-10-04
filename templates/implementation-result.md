# Implementation Result Template

> This is a **recording template**, not a workflow.
> It does not tell the Agent *how* to implement (that is `implementation.md`). It defines the **shape of the result** that implementation hands to `verification.md`.
>
> Do not add implementation steps, debug procedures, retry rules, or decision logic here. Those belong in `implementation.md`.

---

## How to use

1. Run `implementation.md` through to its self-check step.
2. Copy the skeleton below.
3. Fill every field using the actual changes you made, the new facts you observed, and the assumptions you resolved or left open.
4. Choose exactly one Implementation status.
5. Hand the filled result to `verification.md`.

Do not fill this template while you are still editing. It is the output, not a scratchpad.

---

## Result skeleton

```text
Task:
    <one sentence, copied from the Discovery Result>

Goal:
    <what the user actually wants to achieve, copied from the Discovery Result>

Implementation objective:
    Must do:        <what this change was supposed to deliver>
    Out of scope:   <what was explicitly not done, even if tempting>
    Must stay unchanged: <constraints from the Discovery Result that held throughout>

Actual changes:
    - <file/module>: <what changed>
        why: <reason tied to the task>
    - ...

Scope changes:
    - <any area that ended up being touched beyond the Discovery Result's Relevant components>
        why: <why the blast radius was wider than expected>
    - (or: none — stayed within the discovered Relevant components)

New facts discovered during implementation:
    - <fact> — discovered by: <action>
    - ...

Decisions made during implementation:
    - <local technical choice> — why: <reason>
        note: requirement changes, major architecture choices, and destructive operations are NOT recorded here — they go to BLOCKED_OR_ESCALATE via SKILL.md §8.

Assumptions status:
    Verified during implementation:
        - <assumption> — confirmed by: <evidence>
    Still open:
        - <assumption> — why not closed: <reason>

Remaining issues:
    - <problem discovered during implementation that is not yet resolved>
        impact: <how it affects confidence / verification>
    - (or: none)

Risks that materialized:
    - <risk from the Discovery Result that actually triggered> — handled how: <...>
    - (or: none materialized)

Recommended verification targets:
    - <from the Discovery Result's Existing verification, narrowed by what actually changed>
    - <specific behavior that must be re-checked after this change>
    - ...

Recommended verification scope:
    Direct (must verify):
        - <the behavior this task was about>
    Relevant regression (should re-check):
        - <adjacent behavior plausibly affected by this change>
    Out of scope (do not need):
        - <why the rest of the project does not need re-verification for this change>

Implementation status (choose exactly one):
    [ ] READY_FOR_VERIFICATION
    [ ] NEEDS_MORE_IMPLEMENTATION
          Missing piece: <what is left to implement>
    [ ] BLOCKED_OR_ESCALATE
          Escalation summary: <use the SKILL.md §8 escalation format>
```

---

## Field rules

These are filling rules, not implementation rules.

- **Actual changes must point to a file/module and a reason.** "Fixed stuff in the code" is not an acceptable entry.
- **Scope changes are for blast-radius surprises.** If the change stayed within the discovered Relevant components, write "none". If it did not, record what expanded and why.
- **Decisions are local technical choices only.** If a choice touches the requirement, the architecture, or a destructive operation, it belongs in `BLOCKED_OR_ESCALATE`, not here.
- **Assumptions from the Discovery Result are not Facts.** Track which ones got verified and which are still open. `verification.md` will independently check the still-open ones.
- **Remaining issues are not hidden.** If something looks wrong but is not blocking, write it down. Do not leave it in conversation memory only.
- **Recommended verification scope is a suggestion, not a verdict.** `verification.md` independently rebuilds its own scope; this field tells verification where implementation expects to look, but verification may widen or narrow it.
- **Implementation status is exactly one value.** "I think it's probably ready" is not a valid status.

---

## Hand-off

This result is consumed by `workflows/verification.md`:

- `READY_FOR_VERIFICATION` → verification starts from this result and independently judges whether the Goal is achieved.
- `NEEDS_MORE_IMPLEMENTATION` → back to `implementation.md` with the "Missing piece".
- `BLOCKED_OR_ESCALATE` → resolve the escalation path first.

Verification does **not** accept "I finished the edits" as proof. It rebuilds Expected result and Constraints from the original Goal and compares them against the Actual changes and the evidence collected during verification.

If verification later discovers that the implementation was built on a wrong project understanding, it may return to `project-discovery.md`. If it discovers the implementation itself is wrong, it returns here.
