# Workflow: Implementation

> Operational workflow that takes a `Discovery Result` and turns it into a minimal, controlled set of changes.
> It does **not** restate engineering principles (they live in SKILL.md) and does **not** re-explore the project (that lives in project-discovery.md). It only answers: given a sufficient understanding, what to change, in what order, one coherent step at a time, and how to react when reality disagrees with the plan.

---

## Input: Discovery Result or Planning Result

This workflow has two legal entry paths:

- **Lightweight path**: `project-discovery.md` → this workflow. Discovery produced a `READY_FOR_IMPLEMENTATION` result; start implementing directly.
- **Planned path**: `project-discovery.md` → `planning.md` → this workflow. Planning produced a `READY_FOR_IMPLEMENTATION` result; start implementing from the Planning Result.

Planning is an implementation layer on top of Discovery, not a replacement for it. A Planning Result always rests on a completed Discovery Result. Do not treat a Planning Result as permission to redefine the user's Goal.

### Gate on the incoming conclusion

Before doing anything else, check the conclusion field of whatever result you received:

| Incoming conclusion | Action |
|---|---|
| `READY_FOR_IMPLEMENTATION` | Proceed with this workflow. |
| `NEEDS_MORE_DISCOVERY` | Return to `project-discovery.md`. Do not start editing. |
| `BLOCKED_OR_ESCALATE` | Stop. Do not start editing. The escalation path defined in SKILL.md §8 must be resolved first. |

This gate is identical whether the result came from Discovery or from Planning.

### What to read and keep in view

If you came via the **Lightweight path**, read the Discovery Result: Task, Goal, Expected result, Relevant components, Current behavior, Relevant dependencies, Relevant configuration / entry points, Existing verification, Facts, Assumptions, Risks / constraints.

If you came via the **Planned path**, read the Planning Result as the direct implementation input: Task, Goal, Expected result, Scope, Implementation Objective, Implementation Strategy, Change Sequence, Key Decisions, Dependencies / Constraints, Verification Strategy, Open Decisions (see `planning.md` for field definitions). The Planning Result still rests on a Discovery Result — keep the underlying Facts, Current behavior, and Assumptions available.

In both paths:

- Goal and Expected Result must match what Discovery established. Do not redefine the user's Goal because a Planning Result is present.
- The Planning Scope (small / cross-module / architectural) is Discovery's reach judgment. It is **not** the Modification Scope. Step 1 still forms Must do / Out of scope / Must stay unchanged from the Goal and Constraints.
- The Implementation Strategy and Change Sequence are the starting approach, not an immutable script. New facts are handled by the existing Step 7 Reconcile flow (continue / adjust / return to Discovery / re-plan / escalate).
- The Verification Strategy from Planning is the starting direction for verification. Implementation still produces its own Recommended verification targets and Recommended verification scope based on what actually changed — those supersede the planning-level strategy when implementation surfaces new facts.
- Open Decisions from Planning: non-blocking local choices may be handled under the existing autonomy rules. If an Open Decision is actually about the user's Goal, a material requirement change, a major architecture direction, a destructive or high-impact operation, or anything that cannot be reliably inferred, treat it as `BLOCKED_OR_ESCALATE`.

Do **not** treat Assumptions as Facts. Do **not** re-scan the project from zero.

---

## Step 1 — Frame the implementation objective

Translate Goal + Expected result into a concrete delivery definition for this task:

- **Must do**: what this task has to deliver to satisfy the confirmed requirement.
- **Out of scope**: what is explicitly *not* part of this change, even if it looks tempting (refactoring, cleanup, dependency upgrades, formatting, adjacent fixes).
- **Must stay unchanged**: constraints from the Discovery Result that cannot be broken.

If "must do" is ambiguous against the confirmed requirement, stop and escalate. Do not silently pick a smaller delivery.

---

## Step 2 — Verify cheap Assumptions before editing

The Discovery Result contains Assumptions. Before changing code:

- Pick the Assumptions that can be confirmed or rejected cheaply (open a config, read one file, run one read-only command, trace one call).
- Confirm or reject each one. Move confirmed claims into Facts; drop or adjust rejected claims.
- Leave expensive-to-verify Assumptions in place, but flag them so they are checked during execution.

Do not block implementation on every Assumption. Only resolve the ones that, if wrong, would make the planned edits wrong.

---

## Step 3 — Build a minimal implementation plan

Break the task into a small number of **coherent steps**, not a long file-by-file list.

A step is coherent when:

- it changes one related area,
- it produces an observable result (compiles, relevant behavior changes, a testable state),
- it can be reverted or inspected on its own.

Aim for the smallest number of steps that still keeps each step coherent. Avoid:

- one giant edit across many files followed by a single check at the end,
- splitting by file boundaries when the files must change together,
- including cleanup or refactoring steps that are not required by the task.

For each step, write: what changes, why, what observable result to expect.

---

## Step 4 — Execute one coherent step

Perform the edits for the current step only.

Before editing, restate:

- what will change,
- why this step is in the plan,
- what observable result you expect after it.

Keep the edit focused. Do not pull in adjacent improvements while you are in this step.

Within a step, ordinary local implementation choices are autonomous: matching existing project conventions, picking between equivalent local approaches, fixing ordinary implementation mistakes.

Autonomy does not extend across a decision boundary. If a step surfaces a change to the user's goal, a material requirement change, a major architectural direction, a destructive or high-impact operation, a security-sensitive decision, or any decision that cannot be reliably inferred from the current task — stop and escalate per SKILL.md §8 rather than deciding it inside the step.

---

## Step 5 — Observe the result of that step

After the step's edits, immediately observe the most direct feedback available:

- compile / build,
- run the narrowest relevant test,
- start the relevant runtime surface,
- inspect the relevant output, log, or UI.

You do **not** need to run the full verification suite yet. You need enough feedback to know whether this step did what it was supposed to do.

Record: expected result vs observed result.

If the observation is neither a clear success nor a clear failure (timeout, interrupted response, incomplete tool output, ambiguous command status, uncertain external-system reply), do not treat it as a plain failure and retry immediately. The action may already have produced partial or complete side effects.

Before choosing the next move:
1. Check the actual project state and any possible side effects (files written, commands run, external calls made).
2. Reconcile what actually happened against what the step expected.
3. Only then decide: continue, recover, retry (with a changed method or new evidence), or escalate.

A retry without new evidence, a changed hypothesis, or a changed method is a loop risk, not progress.

---

## Step 6 — Check for new relevant facts

Ask explicitly: did this step reveal anything that contradicts the Discovery Result?

Examples of contradictions:

- code structure differs from what discovery assumed,
- configuration value differs from what was read earlier,
- call graph differs,
- an undocumented dependency shows up,
- a file you thought peripheral turns out to be central.

If **no** new relevant fact appeared → proceed to Step 8.

If **a** new relevant fact appeared → go to Step 7.

---

## Step 7 — Reconcile against new facts

When a new relevant fact appears:

1. Update Facts / Assumptions.
2. Re-check the minimal plan from Step 3.
3. Decide:
   - **Plan still valid**: adjust the remaining steps if needed, continue.
   - **Plan no longer valid** (relevant components, dependencies, or blast radius changed): return to `project-discovery.md` for incremental discovery, do not force the old plan.
   - **New fact implies a requirement change, major architecture change, or destructive operation**: escalate per SKILL.md §8.

This reconciliation is the same discipline described in `project-discovery.md` Step 11. Discovery is a phase, not a one-time scan.

---

## Step 8 — Continue or finish

- If steps remain in the plan → go back to Step 4.
- If all planned steps are done → go to Step 9.

Do not add new steps just because you "might as well". New steps must come from either the confirmed requirement or a reconciliation event in Step 7.

---

## Step 9 — Implementation self-check

Before handing off, run a focused check. This is **not** full verification (that belongs to the verification workflow). It is an implementation-level completeness check.

Answer, with references:

- Does the delivered change match the Must-do from Step 1?
- Did anything out-of-scope get changed?
- Are the Must-stay-unchanged constraints still intact?
- Did any Risks / constraints from the Discovery Result materialize?
- Which Assumptions from the Discovery Result are now verified? Which are still open?
- What new Facts were discovered during implementation?

If Must-do is not actually delivered → go back and finish the missing piece.
If out-of-scope changes crept in → remove them or consciously justify them in the handoff.
If an open Assumption blocks confidence → either verify it now or flag it explicitly in the handoff.

---

## Step 10 — Handoff to verification

Produce an Implementation Result and pass it to the verification workflow.

```text
Task: ...
Goal: ...

What changed:
  - <file/module>: <what changed, and why>

New facts discovered during implementation:
  - <fact> — discovered by: <action>

Assumptions status:
  - Verified: <assumption> — confirmed by: <evidence>
  - Still open: <assumption> — why not closed: <reason>

Risks that materialized:
  - <risk> — how handled: <...>

Decisions made during implementation:
  - <local technical choice> — why: <reason>
  (Local implementation choices only. Requirement changes, major architecture choices, or destructive/high-impact decisions are not recorded here — they go through BLOCKED_OR_ESCALATE.)

Remaining issues:
  - <known but unresolved problem / partial item / follow-up concern> — impact: <how it affects delivery>

Recommended verification targets:
  - <from Existing Verification in Discovery Result, narrowed by what actually changed>
  - <specific behavior that must be re-checked>

Recommended verification scope:
  - Direct: <targets that must be verified>
  - Relevant regression: <adjacent behavior plausibly affected by this change>
  - Unrelated: <areas deliberately not verified, and why>
  (Implementation proposes targets and scope for verification to consider. It does not decide the final Verification Status — that belongs to the verification workflow.)

Implementation conclusion:
  - READY_FOR_VERIFICATION
  - NEEDS_MORE_IMPLEMENTATION   (with the missing piece)
  - BLOCKED_OR_ESCALATE         (with escalation summary)
```

---

## Interface to downstream workflows

- **To verification**: the Implementation Result above. Verification should not re-implement; it should independently check whether the change actually delivers the Goal.
- **Back to discovery**: if reconciliation (Step 7) reveals the plan is no longer valid, control returns to `project-discovery.md` for an incremental update, and a fresh Discovery Result feeds back into this workflow.
- **To escalation**: any requirement change, major architecture change, or destructive operation is escalated per SKILL.md §8, not decided inside this workflow.

---

## Boundaries of this workflow

This workflow does **not**:

- Re-explore the whole project (use `project-discovery.md`).
- Define how to write code, how to name things, or how to structure a diff.
- Decide requirement changes or major architecture directions.
- Run the full verification suite or decide what "correct" looks like end-to-end (use the verification workflow).
- Define Git / VCS operations, branching, or commit policy.
- Choose a programming language, framework, IDE, model, or host.
- Explain why minimal change matters or why requirements must be protected (see SKILL.md).
