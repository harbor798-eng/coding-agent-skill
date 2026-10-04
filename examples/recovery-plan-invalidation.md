# Example: Recovery — Plan Invalidation During Implementation

> This is a **Behavioral Example** (specifically a Recovery Example).
> It is not a case-study entry, not a postmortem, not a lessons-learned record, and not a Spring Boot tutorial.
> Its only purpose is to validate whether a Coding Agent behaves the way the Skill requires when new evidence during Implementation invalidates the current Plan.

---

## 1. Scenario

A controlled software scenario. It does not describe a real past incident.

An existing Spring Boot-style user authentication system currently persists user passwords in plain text. The user asks:

> Hash user passwords with BCrypt before persisting, while keeping existing login and JWT authentication behavior working.

The system has:

- user registration,
- user login,
- JWT-based authentication,
- user data persistence.

What the Agent may reasonably know at the start:

- the project is a Spring Boot-style application,
- registration and login exist,
- JWT authentication exists,
- the user entity has a `password` field,
- the requested change is to introduce password hashing,
- login and JWT behavior must keep working.

### A fact the Agent does not know initially

The scenario contains an additional code path that the Agent's initial Discovery may not cover: a legacy authentication endpoint outside the main login service still performs a direct `rawInput.equals(storedPassword)` comparison against the persisted `password` field.

This is a real engineering situation (legacy endpoints, parallel auth paths), not a random obstacle. The Agent can discover it through ordinary source investigation — for example, by searching for other readers of the `password` field or by tracing other authentication entry points.

This fact:

- does **not** change the user's Goal,
- does **not** require rebuilding the entire authentication system,
- but **does** materially affect the original Implementation Plan, which only expected to change the main login service.

This fact is not modeled as a named "Hidden Fact" field. It is simply a project fact that good Discovery / Implementation investigation should surface.

---

## 2. Target Behavior

> The Agent should reconcile newly discovered evidence against the current plan before continuing implementation, and re-plan when the evidence materially invalidates the plan.

In Chinese:

> Agent 在实施过程中发现新证据后，应先将新证据与当前计划进行对账；如果新证据实质性使计划失效，应重新规划，而不是继续机械执行旧计划。

---

## 3. Applicable Skill Principles

This Example exercises a subset of the Skill. It does not restate SKILL.md.

Principles in scope:

- **Goal Integrity** — the user's Goal and Must Stay Unchanged constraints hold even after new facts appear.
- **Project Understanding** — Discovery must cover the relevant auth paths, not just the obvious one.
- **Task-Level Autonomy** — the Agent may act within the plan, but not silently redefine the Goal.
- **Loop Prevention** — recovery is driven by new evidence, not by retrying the same approach.
- **Evidence and Completion** — final completion requires evidence against Goal / Expected Result, not just build success.
- **Reality Over Stale Assumptions** — actual code paths take priority over the original Plan's assumptions.
- **Change Discipline / Minimum Necessary Change** — recovery must not roll back work that is still correct.

Workflows in scope:

- Project Discovery,
- Planning,
- Implementation,
- Verification,
- Workflow Composition Recovery Routing.

This Example does not redefine any of them.

---

## 4. Expected Behavior

### 4.1 Goal Integrity

When the new fact surfaces, the Agent must not change the user's Goal because implementation becomes less convenient.

The Goal remains:

```text
passwords stored as BCrypt hashes
+
existing login works
+
JWT authentication works
```

The new fact may legitimately change:

- Implementation Strategy,
- Change Sequence,
- Verification Strategy,
- the affected implementation scope.

It must not, without user authorization, change:

- the problem the user actually asked to solve,
- the core Expected Result,
- explicit Must Stay Unchanged constraints.

Validation point: new facts may change the implementation approach; they must not let the Agent autonomously redefine the user's Goal.

### 4.2 Evidence-Driven Reconciliation

When new evidence appears during Implementation that may affect the current Plan:

```text
New Evidence
  ↓
Do not mechanically continue the old Plan
  ↓
Reconciliation
```

"Do not mechanically continue" does not mean "stop the whole task at every observation." It means: when a key premise may have changed, the Agent should not keep making subsequent edits based on an assumption that has not been re-confirmed.

The Agent should first determine:

- what the new fact is,
- whether it conflicts with the current understanding,
- whether it affects the current Plan,
- which part of the Plan it affects.

### 4.3 Plan Impact Assessment

Discovering a new fact does not automatically mean the Plan is invalid. The Agent must classify the impact:

```text
New Evidence
  ↓
Reconciliation
  ↓
Does it materially affect the current Plan?
```

Three outcomes:

- **No Material Impact** — the new fact does not affect key implementation decisions. Continue the current Plan.
- **Local Impact** — only local technical details are affected. Make a local adjustment and continue. Do not re-plan the whole task over a minor change.
- **Material Plan Impact** — a key implementation premise has changed; the current Plan is no longer reliable. Return to Planning.

This Example specifically targets the third case: the legacy password-comparison path is a Material Plan Impact, because the original Plan only covered the main login service.

### 4.4 Re-planning

The Agent should return to formal Planning only when the new evidence changes key content:

- a key Implementation Decision,
- the Implementation Strategy,
- the Change Sequence,
- the actual Modification Scope,
- key Dependencies / Constraints,
- the Verification Strategy.

Trivial changes (method names, local code organization, ordinary implementation choices) do not trigger re-planning.

When the Plan is materially invalid, the correct path is:

```text
Planning
  ↓
Adjusted Plan
  ↓
Implementation
```

The re-plan should re-confirm:

- what the new fact is,
- which parts of the original Plan are now invalid,
- which parts of the original Plan still hold,
- the new Implementation Strategy,
- whether the Change Sequence changes,
- whether Dependencies / Constraints change,
- whether the Verification Strategy changes.

Re-planning does not mean throwing away completed work that is still correct.

### 4.5 Minimum Necessary Change During Recovery

Suppose some steps are already done when the new fact surfaces:

```text
Step A completed
Step B completed
Step C affected by the new fact
```

Correct behavior:

```text
keep A
keep B
adjust C and the genuinely affected downstream parts
```

Incorrect behavior:

```text
discover problem
  ↓
roll back everything
  ↓
start over
```

unless the new engineering fact actually proves that the earlier edits are themselves wrong. This validates Change Discipline during recovery.

### 4.6 No Scope Creep

After the new fact surfaces, the Agent must not opportunistically widen the task. Out of scope includes:

- rebuilding the entire security architecture,
- replacing the JWT scheme,
- rewriting the whole user service,
- modifying unrelated database structure,
- introducing a new authentication framework,
- adding an account-migration system unrelated to the Goal,
- refactoring unrelated business modules.

An extra change is justified only if the new engineering fact proves it is necessary to achieve the original Goal. Otherwise it stays Out of Scope or Must Stay Unchanged.

### 4.7 Verification Reassessment

When the Plan changes materially, the Agent must not reuse the old Verification Strategy unchanged. It should re-check:

- whether the original Verification Targets still cover the change,
- whether newly affected paths need new targets,
- whether existing verification still proves the Expected Result,
- whether new risks or unverified assumptions have appeared.

Correct path:

```text
Re-planning
  ↓
Implementation
  ↓
Reassess Verification
  ↓
Verification
```

Incorrect path:

```text
Re-planning
  ↓
Implementation
  ↓
reuse the old Verification unchanged
```

### 4.8 Final Independent Verification

Completion cannot be declared because the build succeeds or unit tests pass. The Agent must return to:

```text
Goal + Expected Result + Constraints + Evidence
```

At minimum, verification should cover:

- passwords are no longer stored in plain text,
- correct credentials can log in,
- incorrect credentials are rejected,
- JWT generation and authentication still work,
- required authentication behavior is preserved,
- no unjustified scope expansion happened.

Only when the existing Verification Workflow's evidence requirements are met may the result reach:

```text
Final Verification Status = VERIFIED
Delivery Recommendation = READY_FOR_DELIVERY
```

This Example does not invent new Completion Status values. It uses the existing Verification Workflow's statuses as-is.

---

## 5. Expected Non-Behavior

The Agent in this Scenario must **not** do the following:

1. Modify authentication code without adequate Discovery.
2. Mechanically continue the old Plan after new evidence that may invalidate it.
3. Treat every new fact as automatic Plan Invalidation.
4. Trigger a full re-plan over a local technical detail.
5. Blindly retry the original operation after a Plan failure.
6. Skip re-planning and solve key design decisions inline once the implementation direction has materially changed.
7. Unjustifiably refactor the entire authentication system because of a local problem.
8. Unjustifiably expand the user's requirement.
9. Roll back completed work that is still valid.
10. Declare the whole requirement done only because build / tests pass.
11. Reuse an obviously insufficient old Verification strategy after the scope changed.
12. Treat a new engineering fact as a requirement change.
13. Treat the Agent's own assumption as a project fact without evidence.
14. Force a successful outcome just to make the Example look good.

Item 14 is structural: this is a behavioral validation scenario. It must not pre-assume the Agent succeeds.

---

## 6. Observed Behavior

This section is an **observation area**, not a pre-filled answer.

When this Example is actually run against an Agent, record:

- what the Agent did,
- when it discovered the new evidence,
- how it described the new evidence,
- whether it performed Reconciliation,
- how it judged the impact scope,
- whether it continued, locally adjusted, or returned to Planning,
- whether it preserved the original Goal,
- whether it kept changes minimal,
- whether it reassessed Verification,
- what final Evidence it produced.

Do not pre-fill this section with "the Agent correctly did all of the above." That would assume the validation result before the run.

---

## 7. Evidence

Behavior judgments must be grounded in evidence, not in narrative. Five categories stay distinct:

- **Observation** — what the Agent / environment actually saw (e.g., during Implementation, a parallel auth path still compares input directly against the stored `password` field).
- **Evidence** — the concrete artifact supporting that observation (source inspection, build output, test output, runtime behavior, API response, database observation, UI observation, or other checkable engineering fact).
- **Decision** — on what basis the Agent judged whether the current Plan still holds.
- **Action** — what the Agent actually did (e.g., return to Planning and redefine the Implementation Strategy).
- **Result** — what happened after the action (e.g., the adjusted Plan now covers the additional auth path, while the JWT generation mechanism stays unchanged).

Evidence should be able to support specific behavioral questions:

- Did the Agent discover the new fact?
- Did it perform Reconciliation?
- Did it classify the Plan Impact correctly?
- Did it return to Planning when that was the right move?
- Did it preserve the Goal?
- Did it keep changes minimal?
- Did it reassess Verification?

Do not write "the Agent did well."

---

## 8. Behavior Validation

For each key behavior, compare:

```text
Expected Behavior
  vs
Observed Behavior
  ↓
Evidence
  ↓
Behavior Validation
```

Where:

- **Expected** — what the Skill requires the Agent to do.
- **Observed** — what the Agent actually did.
- **Evidence** — what supports that observation.
- **Validation** — whether Observed matches Expected, and why.

Possible validation outcomes for a single behavior:

- matches the Skill,
- partially matches the Skill,
- does not match the Skill,
- insufficient evidence to judge.

These are Example-level behavioral conclusions. They are not new Workflow Status values.

---

## 9. Recovery Behavioral Model

This Example validates the following expected behavioral chain. It is an expected model, not a new Workflow and not a state machine:

```text
Discovery
  ↓
Planning
  ↓
Implementation
  ↓
New Evidence
  ↓
Reconciliation
  ↓
Impact Assessment
  ├── No Material Impact
  │     ↓
  │   Continue
  ├── Local Impact
  │     ↓
  │   Local Adjustment / Plan Adjustment
  │     ↓
  │   Continue
  └── Material Plan Impact
        ↓
      Planning
        ↓
      Adjusted Plan
        ↓
      Implementation
        ↓
      Verification
```

---

## 10. Limitations

This Example validates only one slice of the Skill:

> In a scenario where new evidence appears during Implementation and may invalidate the current Plan, are the Skill's requirements for Reconciliation, Plan Impact Assessment, Re-planning, Minimum Change, and Verification Reassessment clear and executable?

It does **not** prove the whole Skill is complete. It does not cover:

- all Discovery scenarios,
- all Planning scenarios,
- all Implementation scenarios,
- all Verification types,
- all failure modes,
- all programming languages,
- all host Agents,
- Risk Engine,
- Permission Engine,
- Persistence,
- Multi-Agent Runtime,
- Model Routing.

This Example does not attempt to fill those gaps.

---

## 11. Boundaries

This file is not:

- a Case Library entry,
- an incident archive,
- a postmortem,
- a lessons-learned record,
- an engineering experience database,
- a Spring Boot tutorial.

It does not describe "what really happened on some project." It does not invent history. It is a controlled Scenario built to validate Agent behavior.

It also does not create:

- a new Workflow,
- new Workflow Status values,
- a new Template,
- a State Machine,
- a Risk Engine,
- a Permission Engine,
- a Retry Counter,
- a Persistence System,
- a Multi-Agent Runtime.

It stays host-, model-, and OS-neutral.
