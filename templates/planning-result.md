# Planning Result Template

> This is a **recording template**, not a workflow.
> It does not tell the Agent *how* to plan (that lives in `workflows/planning.md`). It defines the **shape of the result** that planning hands to Implementation.
>
> Do not add planning steps, decision algorithms, scoring, or approval logic here.

---

## How to use

1. A Planning Result is built on top of a completed Discovery Result. Do not copy the whole Discovery Result — carry only what the Plan depends on.
2. Goal and Expected Result are inherited from Discovery. Planning must not silently change them.
3. The Plan is the best current implementation approach, not an immutable execution script. New facts during Implementation are handled by the existing reconciliation flow.
4. If a key decision is still blocking, do not mark the Plan `READY_FOR_IMPLEMENTATION`. Use `BLOCKED_OR_ESCALATE`.
5. The Verification Strategy below defines the direction of verification. It does not record Actual Result, Evidence, Comparison, or Verification Status — those belong to the verification workflow and its own Result template.

---

## Result skeleton

```text
Task:
    <one sentence, inherited from Discovery>

Goal:
    <what the user actually wants, inherited from Discovery>

Expected result:
    <the observable outcome that proves the Goal, inherited from Discovery>

Discovery basis:
    Facts:
      - <key fact this Plan depends on> — evidence: <where it came from>
    Assumptions:
      - <unverified premise this Plan relies on> — why believed: <reason>
      (If a Key Decision rests on an Assumption, say so explicitly.)

Implementation objective:
    <what this implementation will deliver, under what constraints>
    (An outcome statement, not a code-level task list.)

Scope:
    Task reach:          <small | cross-module | architectural>
                         (Discovery's reach judgment. Not a risk score, permission level, approval gate, or autonomy level.)
    In scope:            <what this task will cover>
    Out of scope:        <what is explicitly not handled>
    Must stay unchanged: <behavior / component / interface that must not regress>

Implementation strategy:
    - Main approach: <the chosen engineering direction>
    - Reused capabilities: <existing pieces built on>
    - Changed components: <what will change and how they cooperate>
    - Why this direction: <reasoning>

Change sequence:
    1. <step>
    2. <step>
    ...
    (An execution route, not an immutable script.)

Key decisions:
    - Decision: <the settled engineering choice>
      Reason: <why>
      Basis: <evidence / discovery fact>
    (Local choices like helper names, variable names, log phrasing, or test file layout are not Key Decisions — they belong to Implementation.)

Dependencies / constraints:
    - <important dependency, technical constraint, compatibility requirement, or condition that must hold>
    (Record constraints. Do not compute risk scores.)

Verification strategy:
    Targets:          <what must be proven>
    Expected results: <what should be observed if the change is correct>
    Methods:          <how each target will be observed, consistent with verification.md>
    (Do not write Actual Result, Evidence, Comparison, or Verification Status here. Those are recorded by the verification workflow.)

Open decisions:
    - Decision: <still-open choice>
      Why unresolved: <reason>
      Impact: <what it affects>
      Blocking / Non-blocking: <which>
    (If any Open Decision is Blocking, the Plan must not be READY_FOR_IMPLEMENTATION.)

Plan status (choose exactly one):
    [ ] READY_FOR_IMPLEMENTATION
    [ ] NEEDS_MORE_DISCOVERY        (with the next discovery question)
    [ ] BLOCKED_OR_ESCALATE          (with the escalation summary)
```

---

## Field rules

1. **Goal and Expected Result are inherited from Discovery.** Planning may sharpen how they are stated, but must not narrow, weaken, or redefine them. If the Goal turns out to be ambiguous or infeasible, escalate rather than silently reinterpreting.
2. **Task Reach is not Modification Scope.** Task Reach (`small` / `cross-module` / `architectural`) is Discovery's judgment about task complexity. In Scope / Out of Scope / Must Stay Unchanged is the actual change boundary this task commits to. Do not collapse them into one field.
3. **Discovery Basis is selective.** Carry only the Facts and Assumptions the Plan actually depends on. Do not reproduce the full Discovery Result.
4. **Facts and Assumptions stay separated.** If a Key Decision rests on an Assumption, that must be visible. The Plan is only as solid as its weakest unverified premise.
5. **Key Decisions are engineering choices, not code-level preferences.** Naming, local structure, log format, and test organization are Implementation's job, not Planning's.
6. **Open Decisions may exist only if they are Non-blocking.** A Blocking Open Decision means the Plan is not ready — use `BLOCKED_OR_ESCALATE` or `NEEDS_MORE_DISCOVERY` instead.
7. **Verification Strategy is direction, not result.** It says *how* verification will be approached. It does not pre-fill Actual Result, Evidence, Comparison, or a final verdict. Those come from the verification workflow after Implementation.
8. **Plan Status is not Task Progress.** There is no DRAFT, IN_PROGRESS, DONE, or PARTIAL. The three values answer one question: can Implementation start now?
9. **Change Sequence is a route, not a contract.** New facts during Implementation may require continuing, adjusting, re-planning, returning to Discovery, or escalating — the existing reconciliation flow handles that.
10. **Do not invent new statuses.** Use exactly the three values listed. Do not add Risk scores, approval gates, or persistence fields.

---

## Hand-off

The filled Planning Result is handed to `workflows/implementation.md` only when Plan Status is `READY_FOR_IMPLEMENTATION`.

- Implementation reads Task, Goal, Expected Result, Scope, Implementation Objective, Implementation Strategy, Change Sequence, Key Decisions, Dependencies / Constraints, Verification Strategy, and Open Decisions as its starting point.
- It still forms its own Must do / Out of scope / Must stay unchanged during Step 1, and produces its own Recommended verification targets / scope based on what actually changed.
- If a new fact invalidates the Plan, the existing reconciliation flow applies (continue / adjust / re-plan / return to Discovery / escalate).
- If Plan Status is `NEEDS_MORE_DISCOVERY`, control returns to `project-discovery.md`. If it is `BLOCKED_OR_ESCALATE`, the SKILL.md §8 escalation format applies.
