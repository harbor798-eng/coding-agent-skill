# Example Design

> This document is **not** an Example. It defines how Examples should be designed.
> It sits above the `examples/` directory and below the Skill / Workflow / Template layer. It does not replace any of them.

---

## 1. Purpose

Examples exist to validate that the Skill's core rules, workflows, composition rules, and templates actually work together on a real software engineering task.

An Example is **not**:

- a tutorial,
- a re-explanation of the Skill,
- a showcase for a single API,
- a scripted demo that always succeeds.

An Example asks:

> When a real Agent runs a real task against the actual Skill / Workflow / Template stack, does the composition hold?

Specifically, an Example should help surface:

- whether the Workflows are actually executable end-to-end,
- whether the interfaces between stages carry enough information,
- whether Goal Integrity holds across the full run,
- whether new facts trigger the right Reconciliation,
- whether Recovery and Re-planning actually fire when they should,
- whether Verification produces sufficient evidence,
- whether the final Completion Gate is reachable and defensible.

The core comparison an Example makes is **behavioral**:

```text
Scenario
  ↓
Target Behavior (what the Skill asks the Agent to do here)
  ↓
Expected Behavior     ←── derived from SKILL.md + the applicable Workflows
  ↓
Observed Behavior     ←── what the Agent actually did, with evidence
  ↓
Behavior Validation   ←── does Observed match Expected?
```

An Example is a **behavior validation scenario**, not a case-study library entry. It does not accumulate engineering lessons learned, postmortems, or incident records. Those may exist elsewhere later; they are not what this layer is for.

---

## 2. What an Example Must Demonstrate

A valid Example traces one task through the composition:

```text
Goal
  ↓
Discovery
  ↓
Planning (if needed)
  ↓
Implementation
  ↓
Verification
  ↓
Final Result
```

If the case includes Recovery, it must additionally trace:

```text
New Evidence
  ↓
Reconciliation
  ↓
Invalidated Understanding / Plan
  ↓
Recovery / Re-planning
  ↓
Continue
  ↓
Verification
```

Planning is optional. Do not require every Example to pass through Planning.

### Expected Behavior vs Observed Behavior

The flow above is the **Expected Behavior** — what the Skill asks the Agent to do in this scenario. An Example must also record the **Observed Behavior** — what the Agent actually did — and compare the two.

Expected Behavior is derived from SKILL.md and the applicable Workflows. It is not invented per Example.

### Expected Non-Behavior

An Example should also state what the Agent should **not** do in this scenario. Expected Non-Behavior makes validation symmetric. Typical non-behaviors the Skill forbids, which an Example may explicitly check for:

- modifying code before sufficient Discovery,
- silently narrowing or expanding the Goal because implementation is inconvenient,
- repeating a failed action without new evidence,
- declaring the task complete because the build succeeded or a test passed,
- making a major requirement, architecture, or high-impact decision autonomously,
- performing unrelated refactoring or cleanup under the cover of the task,
- treating an unverified assumption as a confirmed fact.

Not every Example needs to check every non-behavior. The point is that validation is not only "did the right thing happen" but also "did the forbidden thing not happen."

---

## 3. Example Types

### Normal Example
Shows a task reaching completion along a healthy path:

```text
Requirement → Discovery → Implementation → Verification
```

or:

```text
Requirement → Discovery → Planning → Implementation → Verification
```

### Recovery Example
Shows what happens when the run deviates: new facts surface, an old assumption fails, a Plan invalidates, an Implementation defect appears, a Discovery misunderstanding surfaces, or Verification fails. The Example must show:

```text
Observe → Reconcile → Classify → Route → Recover → Verify
```

Recovery Examples matter more than Normal Examples for this Skill. A clean happy path only proves that the Workflows can be walked through. A Recovery Example proves that the dynamic recovery rules — the part that distinguishes this Skill from a giant prompt — actually fire.

---

## 4. Example Selection

A good Example is built on a real software project with a real development task. It should have:

- a real codebase,
- a real task,
- a clear Goal,
- a clear Expected Result,
- observable engineering facts,
- a verifiable outcome.

Prefer cases that exercise several core principles at once, rather than artificially constructed mini-tasks designed to trigger one specific rule.

Complexity should be moderate. Do not manufacture pointless complexity just to look impressive. Do not simplify so far that the Example no longer exercises the interfaces it is supposed to test.

---

## 5. Example Logical Structure

Do not prescribe a fixed file layout. What matters is the logical structure, which should contain, as applicable:

```text
Scenario
  ↓
Requirement
  ↓
Discovery
  ↓
Planning (if needed)
  ↓
Implementation
  ↓
New Evidence / Reconciliation (if it happens)
  ↓
Recovery / Re-planning (if it happens)
  ↓
Verification
  ↓
Final Result
```

`Scenario` is the validation setup — the codebase, the task, and the conditions the Agent starts from. It is not a case-study background section. Its purpose is to define what behavior is being tested, not to narrate a past incident.

An Example may live in one Markdown file, or be split across several files when the task is large. Physical organization is not the design constraint. The constraint is that the engineering facts and stage relationships are expressed clearly.

---

## 6. Evidence Requirements

The Observed Behavior is recorded through five distinct categories. An Example must not be written as "the Agent should have done this." It should record what was actually observed, with evidence.

Five categories must be kept distinct:

- **Observation**: what the Agent actually saw (a build line, a test output, a log entry, a UI behavior).
- **Evidence**: the observation selected because it supports a specific claim about whether Expected Behavior was or was not met.
- **Decision**: what the Agent decided to do next, and what it based that on.
- **Action**: what the Agent actually did.
- **Result**: what happened after the action.

These categories together constitute the Observed Behavior that is compared against the Expected Behavior and the Expected Non-Behavior.

Do not write Examples as polished post-hoc "ideal runs." The value of an Example is that it reflects real engineering behavior backed by checkable evidence.

---

## 7. Recovery Example Requirements

A Recovery Example is a scenario designed to test whether the Agent's recovery and reconciliation behavior matches the Skill. It is not an engineering postmortem or an incident record.

When the Example is a Recovery Example, it must trace the recovery chain explicitly:

```text
Initial Understanding
  ↓
New Evidence
  ↓
Reconciliation
  ↓
What Became Invalid
  ↓
Routing Decision
  ↓
New Action
  ↓
Verification
```

The Example should be able to answer:

- what was originally assumed,
- what new fact was observed,
- which Assumption / Plan / Understanding became invalid because of it,
- why a rollback or re-plan was needed,
- which Workflow was chosen as the recovery target,
- what the new approach is,
- how the task is re-verified.

A failure by itself is not a reason to retry. Recovery must be driven by new evidence, a changed state, or a changed understanding. Do not design Examples around a fixed number of retries. Do not introduce a Retry Counter.

---

## 8. Example and Existing Workflow Interfaces

An Example must run on top of the existing stack:

```text
SKILL.md
  ↓
Workflow Composition
  ↓
Discovery / Planning / Implementation / Verification
  ↓
Templates
```

Stage results in an Example should use the existing Templates. Do not invent new Workflow Status values inside an Example. Do not invent a parallel field system that conflicts with the Templates.

If an Example exposes a missing interface — a Workflow cannot carry a fact the next stage needs, a Template field is insufficient, a Status is ambiguous, a Recovery route is unclear — record that as a design problem with the Skill / Workflow / Template layer. Do not paper over it with an ad-hoc field invented for the Example.

---

## 9. Example Completion Criteria

An Example is "done" when it is sufficient to exercise the Skill, not when the Markdown is finished. At minimum it should let a reader check:

- Goal is clear,
- Expected Result is clear,
- the correct Workflow(s) were chosen,
- Discovery produced enough facts,
- Planning (when used) settled the key engineering decisions,
- Implementation stayed within Goal and Scope,
- new facts were handled correctly,
- any Recovery was driven by evidence,
- Verification has enough Evidence,
- the Final Result is compared against Expected Result,
- the Completion Gate can be judged.

---

## 10. Example Should Expose Design Problems

Examples must not be designed as success-only demos.

If a real case exposes:

- a Workflow interface that is missing,
- a Template field that is insufficient,
- a Status definition that is ambiguous,
- a Recovery route that is unclear,
- a Verification evidence gap,
- a Goal Integrity failure,
- a rule that turns out to be unexecutable on a real task,

treat that as the Example's validation feedback, not as a defect in the Example:

```text
Example
  ↓
Surfaces a design problem
  ↓
Record the problem
  ↓
Return to Skill / Workflow / Template
  ↓
Improve the design
  ↓
Re-run the Example
```

Do not build an iteration system, database, automated test framework, or state machine to manage this loop. It is a design principle, not a runtime.

---

## 11. Boundaries

Example Design does **not**:

- create a new Workflow,
- define new Workflow Status values,
- implement a Workflow Engine,
- implement a State Machine,
- implement a Risk Engine,
- implement a Permission Engine,
- implement a Retry Counter,
- implement a persistence layer,
- implement a Multi-Agent Runtime,
- implement Model Routing,
- define IDE / OS / Host-specific behavior,
- replace SKILL.md,
- replace any Workflow document,
- replace any Template,
- act as a Case Library, incident archive, postmortem collection, or lessons-learned database.

An Example is an instance that validates the existing design. It stays host-, model-, and OS-neutral. It asks whether the Agent behaved the way the Skill requires, not what happened in some past project.
