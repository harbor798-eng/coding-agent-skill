# Workflow Composition

> This document is **not** a new engineering workflow. It does not replace `project-discovery.md`, `planning.md`, `implementation.md`, or `verification.md`.
> It only answers: given a software engineering task, which existing workflows should run, in what order, and when verification fails, which layer the problem actually belongs to.

---

## 1. Purpose

This file defines how a Coding Agent selects, composes, and — when needed — routes back between the four existing workflows.

It lives in `docs/` rather than `workflows/` because it does not itself perform discovery, planning, implementation, or verification. It describes the **composition** of those workflows.

It does not:

- re-define how any single workflow performs its own steps,
- introduce a routing engine or state machine,
- decide which model, IDE, host, or OS the Agent runs on.

The core operating principle remains:

> Allow autonomous execution within the user's goal. Never silently change the goal.

---

## 2. Available Workflows

Four workflows exist. Each owns one layer of the problem.

### Project Discovery
Answers: *What are the current facts about this project and this task?*
Produces a Discovery Result covering Goal, Expected result, Scope, Current behavior, Relevant components, Dependencies, Existing verification, Facts, Assumptions, Unknowns, and Risks / constraints.

### Planning
Answers: *Given those facts, what implementation strategy should we commit to before editing?*
Produces a Planning Result covering Implementation Objective, Implementation Strategy, Change Sequence, Key Decisions, Dependencies / constraints, and Verification Strategy.

### Implementation
Answers: *What changes actually get made?*
Consumes a Discovery Result (lightweight path) or a Planning Result (planned path) and produces an Implementation Result describing Actual changes, New facts, Decisions, Remaining issues, and Recommended verification targets / scope.

### Verification
Answers: *Does the actual result actually meet the Goal, Expected result, and Constraints?*
Independently rebuilds Goal / Expected / Constraints and produces a Verification Result with Final Status and Delivery Recommendation.

This document does not restate their internal steps.

---

## 3. Workflow Selection

After Discovery, the Agent decides whether to run a formal Planning phase.

The decision is **not** "how big is the task." It is:

> Before editing, do key engineering decisions still need to be settled?

Factors that suggest Planning is needed:

- multiple implementation directions are materially different and reasonable,
- multiple components or modules must be coordinated,
- an important upstream engineering decision has not been made,
- the Implementation Strategy itself needs to be written down,
- the Verification Strategy is non-trivial,
- the change has architectural or long-term impact,
- starting Implementation directly would force the Agent to solve key design questions on the fly.

If Discovery already leaves the Agent with a clear path inside the confirmed Goal, Scope, and Constraints, formal Planning can be skipped.

Discovery Scope (`small` / `cross-module` / `architectural`) is one input to this judgment, but it is **not** a mechanical mapping. A `small` task with a hard design choice may still warrant Planning; an `architectural`-scoped task whose approach is already conventional may not.

---

## 4. Quick Change Path

The lightweight composition:

```text
Requirement
    ↓
Project Discovery
    ↓
Implementation
    ↓
Verification
```

Applies when:

- the change target is clear,
- the relevant code location is basically known,
- there is no meaningful choice between approaches,
- no key engineering decision is still open,
- the implementation path follows directly from existing project facts,
- the verification method is also basically known.

Quick Change **does not** skip Discovery. It skips the *formal* Planning phase. The Agent still thinks before editing; it just does not produce a separate Planning Result.

---

## 5. Planned Change Path

The full composition:

```text
Requirement
    ↓
Project Discovery
    ↓
Planning
    ↓
Implementation
    ↓
Verification
```

Applies when:

- a key engineering decision must be settled before editing,
- multiple important implementation directions exist,
- cross-component coordination is required,
- architectural or long-term impact is significant,
- verification is complex,
- starting Implementation directly would force the Agent to re-solve design questions.

Planning's job is **not** to predict every future detail. It is to settle the decisions that would otherwise block safe Implementation.

---

## 6. Planning Selection Principle

Planning is an optional formal phase.

All engineering work involves some thinking and planning. That is required of every task. Producing a separate, written Planning Result is required only when the task genuinely needs it.

```text
"Work with a plan"          → required of every task
"Run the Planning workflow"  → required only when key decisions need to be settled first
```

Do not produce a Planning Result for a one-line config fix just because the phase exists. Do not skip Planning when a multi-module change with open design decisions is waiting.

---

## 7. Workflow Composition

### Lightweight

```text
Discovery → Implementation → Verification
```

### Planned

```text
Discovery → Planning → Implementation → Verification
```

### Recovery

When Verification does not cleanly pass, route based on the classified problem:

```text
Verification
    ↓
  ┌─ Implementation problem     → Implementation
  ├─ Discovery problem          → Project Discovery
  ├─ Plan invalidation          → Planning
  ├─ Requirement / architecture conflict → Escalation
  └─ Environment / external blocking      → BLOCKED
```

When routing back to Discovery or Planning, re-run the necessary stages rather than continuing from the stale conclusion. A re-discovery does not resume from the old Discovery Result; it rebuilds from current facts.

---

## 8. Recovery Routing

Verification is not a binary pass/fail. A non-passing Verification carries new evidence that points at the layer actually responsible.

### Implementation Problem
The project understanding and the Goal are still valid, but the actual change is wrong or incomplete.
→ Return to `implementation.md`.

### Discovery Problem
Verification reveals that the earlier understanding of the project — its current behavior, dependencies, entry points, or configuration — was wrong.
→ Return to `project-discovery.md`, carrying the new fact that broke the old model. Do not patch the old Discovery Result in place; rebuild from current reality.

### Plan Invalidation
The Discovery facts still mostly hold, but new evidence makes the original Implementation Strategy, Change Sequence, or Key Decisions no longer valid.
→ Return to `planning.md` to re-plan.

### Requirement / Architecture Conflict
The Goal itself is ambiguous, contradictory, infeasible, or requires a direction change that exceeds the Agent's autonomy boundary.
→ Escalate per SKILL.md §8.

### Environment / External Blocking
The blocker is external: unavailable dependencies, external systems, permissions, resources, or conditions the Agent cannot resolve on its own.
→ Mark `BLOCKED`, record known facts and environmental limits. Do not misclassify an environment problem as an Implementation failure.

---

## 9. Reconciliation Before Routing

Routing must be based on current evidence, not on a stale recorded state.

If the Agent notices:

- stored state disagrees with the actual project,
- the Plan no longer matches reality,
- the Discovery Result is out of date,
- the actual Implementation outcome differs from what was expected,

reconcile against current project reality before choosing the next workflow. Do not continue a step just because it was written down earlier.

Reconciliation depth should match the conflict. A small discrepancy is a local adjustment; a fundamental mismatch is a return to Discovery or Planning.

---

## 10. Completion Gate

The entire composition reaches a deliverable engineering state only when **both** hold:

- Verification Final Status = `VERIFIED`
- Delivery Recommendation = `READY_FOR_DELIVERY`

Notes:

- Implementation's `READY_FOR_VERIFICATION` means "ready to be checked", not "task done".
- Verification's `VERIFIED` means "evidence supports the Goal", not "the user has been told".
- The final act of telling the user belongs to Delivery, which is downstream of this composition.

---

## 11. Boundaries

This document does **not**:

- orchestrate tool calls,
- implement a state machine,
- implement a workflow engine or router,
- implement a task database or persistence layer,
- implement a permission or approval system,
- implement a risk engine,
- implement an Agent runtime,
- choose a model, IDE, host, or OS,
- redefine the internal steps of any of the four workflows.

It only defines how the four existing workflows are selected, composed, and routed between.

Quick Change and Planned Change are **working-track concepts**, not Result statuses. No new status names are introduced.
