# Workflow: Planning

> Operational workflow that turns a `Discovery Result` into a controlled, goal-aligned implementation plan.
> It does **not** re-explore the project (that lives in `project-discovery.md`), does **not** write code (that lives in `implementation.md`), and does **not** restate engineering principles (that live in `SKILL.md`). It only answers: given a sufficient understanding, what are we going to change, in what order, with what decisions settled, and how will we know it worked?

---

## 1. Purpose

Planning sits between Discovery and Implementation:

```text
Discovery
    ↓
Planning        (optional, explicit)
    ↓
Implementation
    ↓
Verification
    ↓
Delivery
```

Planning is the act of converting a completed Discovery Result into a plan that:

- protects the user's Goal,
- keeps the change bounded,
- settles the key engineering decisions before editing,
- gives a clear change sequence,
- defines the verification direction,
- leaves local implementation choices to Implementation.

Planning is **not**:

- a TODO list,
- a progress tracker,
- an immutable execution script,
- a re-run of Discovery,
- a preview of every line of code.

A Plan is the best current implementation approach given what is known now. New facts may require adjusting it, re-planning, returning to Discovery, or escalating.

---

## 2. When Planning Is Needed

Planning is an **optional explicit phase**. Not every task needs a written plan.

### Lightweight task — skip explicit Planning

Simple, well-scoped, low-complexity work may flow directly:

```text
Discovery → Implementation
```

Examples: a localized bug fix, a config value change, a copy edit, a mechanical rename, a local refactor whose shape is already clear.

Skipping *explicit* Planning does **not** skip basic engineering judgment. The Agent still has to understand Goal, Expected Result, Scope, Approach, and Verification before editing — it just does not need to produce a separate Planning Result.

### Full Planning — produce an explicit Plan

Produce an explicit Plan when any of the following applies:

- the task spans several modules,
- the implementation has multiple ordered steps with dependencies,
- there are meaningful design decisions to settle,
- the change has architectural impact,
- a public API or contract is affected,
- data structures or storage schemas change,
- the requirement has material ambiguity,
- the change is high-impact,
- the verification method is non-trivial,
- new facts during implementation could plausibly redirect the work.

Do **not** use file count as a complexity score. Do **not** build a complexity engine or risk engine.

---

## 3. Planning Inputs

Planning starts only after `project-discovery.md` has produced a Discovery Result. It does not re-assume project state.

Read and keep in view: Task, Goal, Expected result, Scope, Current behavior, Relevant components, Relevant dependencies, Existing verification, Facts, Assumptions, Risks / constraints.

How Planning treats each input:

- **Goal** — inherited. Planning must not change it.
- **Expected result** — inherited. It defines the success bar.
- **Scope** — read as `small` / `cross-module` / `architectural`. This is Discovery's judgment about task reach and complexity. It is **not** a Modification Scope (Must do / Out of scope / Must stay unchanged), and it is **not** a risk score, permission level, approval gate, or execution rule.
- **Current behavior** — the pre-change baseline the plan will modify.
- **Relevant components / dependencies** — the working set the plan is built on.
- **Facts / Assumptions** — keep separate. Any plan decision that rests on an unverified Assumption must say so.
- **Risks / constraints** — considered, but not converted into a numeric score.
- **Known irrelevant unknowns** — not carried into the plan.
- **Existing verification** — input to the Verification Strategy, not copied verbatim.

If Planning discovers that a key Discovery fact no longer matches the actual project, do not plan on a false basis. Reconcile first; return to Discovery or escalate as needed.

---

## 4. Step 1 — Reconstruct Planning Context

Before designing the plan, re-confirm what the plan rests on:

- Goal,
- Expected result,
- Scope,
- Current behavior,
- Relevant components,
- Relevant Facts,
- Relevant Assumptions,
- Constraints,
- Existing verification.

This is not a re-Discovery. It is a freshness check on the inputs.

If a key fact has gone stale:

- adjust locally if the discrepancy is small,
- return to `project-discovery.md` if the discrepancy undermines the basis,
- escalate if it points at a requirement or architecture problem.

Do not build a plan on top of a known-stale understanding.

---

## 5. Step 2 — Define Implementation Objective

Translate Goal + Expected result + Scope + Constraints into one delivery definition:

> "This implementation will deliver X, while keeping Y unchanged."

The Implementation Objective states the outcome and the preserved boundaries, not a code-level task list. It is the same Must-do / Out-of-scope / Must-stay-unchanged framing that Implementation will later execute against.

If the Objective cannot be stated without silently narrowing the Goal, stop and escalate.

---

## 6. Step 3 — Determine Implementation Strategy

State the engineering approach at the level needed to implement safely:

- the main approach,
- which existing capabilities are reused,
- which relevant components change and how they cooperate,
- why this direction was chosen,
- when useful, why obvious alternatives were rejected.

Do **not** invent not-yet-confirmed files, classes, APIs, database columns, algorithms, or code structure. Planning must be specific enough to implement safely, but it must not guess at future details.

---

## 7. Step 4 — Resolve Key Decisions

Identify the engineering decisions that must be settled before editing begins.

Two categories:

- **Key decisions settled during Planning**: for example, reusing an existing auth mechanism, changing or not changing a public API, adding or not adding a dependency, modifying or not modifying an existing data structure.
- **Open decisions still open during Planning**: these may exist only if they do **not** block Implementation. Local choices left to Implementation are not open decisions.

If a pending decision is about the user's Goal, a material requirement change, a major architecture direction, a destructive or high-impact operation, a security-sensitive choice, or anything that cannot be reliably inferred from the task — do not decide it here. Use the existing escalation path. Do not build a new approval system.

---

## 8. Step 5 — Define Change Sequence

Sketch the rough order of implementation. Example shape:

```text
1. change the core logic
2. adjust the interface layer
3. add the focused tests
4. run the verification steps
```

The Change Sequence is an execution route, not an immutable script. If Implementation surfaces a new fact, judge whether the Sequence still holds before continuing — do not force a step just because it was written down.

Keep the number of sequence items small and coherent. A long file-by-file checklist is not a plan.

---

## 9. Step 6 — Define Verification Strategy

A plan must say how the Goal will be proven, not only what will be changed.

The Verification Strategy is consistent with `workflows/verification.md`. It uses the same vocabulary:

- Verification Target,
- Expected Result,
- Verification Method,
- Evidence,
- Actual Result,
- Comparison.

Planning proposes the direction of verification: which targets matter, what methods are appropriate, which existing verification surfaces to reuse. It does not dictate maximum testing, and it does not redefine verification terminology. The actual evidence gathering and final verdict belong to the verification workflow.

---

## 10. Step 7 — Check Plan Sufficiency

Before declaring the plan ready, answer:

1. Is the Goal clear?
2. Is the Expected Result clear?
3. Is the Scope clear?
4. Is the Implementation Objective clear?
5. Is the Strategy actionable?
6. Is the Change Sequence clear enough to start?
7. Are the Key Decisions resolved?
8. Are Dependencies / Constraints explicit?
9. Is the Verification Strategy clear?
10. Does any critical Unknown or blocking decision still prevent Implementation?

The core test:

> Given this Plan, does the Implementation Agent still have to solve key engineering design problems on the fly?

If yes, the Plan is not ready.

This is **Decision Complete**, not "every code-level detail decided". Local choices remain with Implementation: variable names, private helpers, local code organization, log phrasing, test file layout, and other choices that do not alter Goal, architecture, or requirements.

---

## 11. Plan Status

Planning uses exactly three statuses. No others.

| Status | Meaning |
|---|---|
| `READY_FOR_IMPLEMENTATION` | The Plan is complete enough, key engineering decisions are settled, Implementation can proceed. |
| `NEEDS_MORE_DISCOVERY` | Planning found the Discovery basis is insufficient; return to Discovery for more facts. |
| `BLOCKED_OR_ESCALATE` | A requirement, architecture, high-impact, or otherwise non-inferable decision needs the user. |

Do not add `PLAN_FAILED`, `PLAN_STALE`, `PARTIAL`, `UNCERTAIN`, or similar.

---

## 12. Plan Reconciliation and Re-planning

When Implementation surfaces a new fact, do not treat it as "Plan failed → retry". First classify the impact:

1. **No impact on the Plan** → continue.
2. **Local impact** → adjust the Plan locally.
3. **Plan partially or fully invalid** → re-plan.
4. **Discovery basis invalid** → return to `project-discovery.md`.
5. **New requirement / architecture / high-impact decision** → escalate.

Failure by itself is not a reason to re-plan. Re-plan only when something actually changed: new evidence, a changed assumption, a changed constraint, a new technical fact, or a changed requirement. Blindly repeating the same approach is a loop risk.

---

## 13. Escalation

Planning does not autonomously decide:

- changes to the user's Goal,
- material requirement changes,
- major architecture directions,
- destructive or high-impact operations,
- security-sensitive decisions,
- key product or engineering choices that cannot be reliably inferred.

These go to `BLOCKED_OR_ESCALATE` and follow the SKILL.md §8 escalation format. No new approval system is introduced here.

---

## 14. Planning Result

When the Plan is ready, produce a structured result. This is the handoff to Implementation. A separate Template file is not part of this workflow.

```text
Task: ...
Goal: ...
Expected result: ...

Discovery basis:
  - <key Discovery facts this Plan depends on, not the whole Discovery Result>

Implementation objective:
  - Must do: ...
  - Out of scope: ...
  - Must stay unchanged: ...

Scope:
  - <small | cross-module | architectural>  (Discovery's reach judgment, not a Modification Scope)

Implementation strategy:
  - <main approach, reuse, component interactions, why this direction>

Change sequence:
  - <step 1>
  - <step 2>
  - ...

Key decisions:
  - <decision> — settled how: <reason / evidence>

Dependencies / constraints:
  - <what the plan depends on / what must not break>

Verification strategy:
  - Targets: <what must be proven>
  - Methods: <how it will be observed, consistent with verification.md>

Open decisions:
  - <non-blocking local choices left to Implementation>

Plan status:
  - READY_FOR_IMPLEMENTATION
  - NEEDS_MORE_DISCOVERY   (with the next Discovery question)
  - BLOCKED_OR_ESCALATE     (with escalation summary)
```

---

## 15. Handoff to Implementation

Only `READY_FOR_IMPLEMENTATION` enters `implementation.md`.

The handoff carries: Task, Goal, Expected result, Scope, Implementation Objective, Implementation Strategy, Change Sequence, Key Decisions, Dependencies / Constraints, Verification Strategy, Open Decisions.

If an Open Decision is actually blocking, the Plan must not be `READY_FOR_IMPLEMENTATION`.

Implementation may make local technical choices within the Plan. It must not change the user's Goal, the core Scope, the major architecture, or confirmed requirements. If a new fact invalidates the Plan, use Section 12 to reconcile.

---

## Boundaries of this workflow

This workflow does **not**:

- Re-explore the project (use `project-discovery.md`).
- Write code or make the edits (use `implementation.md`).
- Run verification or decide the final verdict (use `verification.md`).
- Decide requirement changes or major architecture directions.
- Build a risk engine, risk score, permission matrix, approval matrix, plan engine, or dependency engine.
- Persist state, track history, or run a multi-agent scheduler.
- Bind to a specific language, framework, IDE, model, OS, or host.
- Explain why planning matters or why requirements must be protected (see SKILL.md).
