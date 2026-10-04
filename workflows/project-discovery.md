# Workflow: Project Discovery

> Operational workflow that turns SKILL.md principles (Understand Before Modify, Facts Before Assumptions, Project Understanding, Task-Level Autonomy, Reality Over Stale Assumptions) into concrete steps.
> It does **not** restate those principles. It only answers: when starting a task on an unfamiliar project, what to look at first, how deep to look, when to stop investigating, and when to escalate.

---

## When to run this workflow

Run this workflow at the start of a software engineering task when the Agent does not yet have enough context to safely modify the project.

You do **not** need to re-run it from scratch for every small follow-up edit once a stable Discovery Result already exists.

---

## Step 0 — Classify task scope first

Before opening any file, classify the task. Discovery depth must match this scope.

| Scope | Typical trigger | Required depth |
|---|---|---|
| **Small / local** | One file, one function, one config value, a localized bug fix | Locate the relevant file, read it, identify its caller and the existing test. Stop. |
| **Cross-module** | Touches several files or modules with known relationships | Build a baseline map, locate the area, read the core, trace key dependencies, find relevant tests. |
| **Architectural / new capability** | Introduces new structure, replaces a subsystem, or spans large unknown areas | Full baseline map, module boundaries, entry points, configuration, and risk scan. |

Do **not** run architectural-depth discovery for a one-line config fix. Do **not** stop at small-task depth when the task actually spans modules. If the scope is unclear after reading the request, treat it as cross-module until evidence says otherwise.

---

## Step 1 — Frame the task

Read the user request. Write down, concisely:

- **Task**: one sentence.
- **Goal**: what the user actually wants to achieve, beyond the literal instruction.
- **Expected result**: what observable change proves the goal is met.
- **Constraints**: what must remain unchanged.
- **Provisional scope**: Small / Cross-module / Architectural (from Step 0).

If the request is ambiguous on any of these, mark it as a candidate escalation point. Do not silently pick an interpretation and proceed.

---

## Step 2 — Establish a baseline map

Skip this step for trivial small tasks. For cross-module or architectural work, build a high-level map of the project without diving into any one area:

- Top-level directory layout.
- Entry points, run commands, test commands (read from project files / docs, not from memory).
- Major modules and their naming.
- Where configuration lives.

Stop when you can name the major parts of the project and where the task area likely sits. Do not read implementation files in this step.

---

## Step 3 — Locate the relevant area

Using task keywords, names, error messages, file names, or expected behavior:

- Search the codebase for symbols, strings, config keys, and file names related to the task.
- List candidate relevant files and modules.
- Mark each candidate as **central** or **peripheral** to the task.

Do not assume a file is relevant just because it looks similar.

---

## Step 4 — Read the relevant implementation

Read the central relevant files. Build a model of:

- What the code **currently does** (current behavior), not what you assume it should do.
- How the relevant pieces connect.
- What inputs they read, what outputs they produce, what side effects they have.

If reading reveals that a file you marked peripheral is actually central, move it.

---

## Step 5 — Trace dependencies and entry points

For the relevant area, trace:

- **Upstream**: what calls into this code, what triggers it.
- **Downstream**: what this code calls, what it depends on.
- **Configuration**: what config, environment, or external state changes its behavior.
- **Entry points**: how this area gets exercised at runtime.

Go one level beyond the obvious. Stop tracing when you leave the neighborhood that could plausibly be affected by the task.

---

## Step 6 — Find existing verification

Identify how this area is currently checked:

- Automated tests covering it.
- Manual run / build steps.
- Known observability surfaces (logs, UI, API endpoints, data checks).

You do not need to run them yet. You only need to know **where** verification will happen later.

---

## Step 7 — Classify what you know

Write down three lists:

- **Facts**: claims supported by code you have read, config you have opened, output you have observed, or tests you have run. Each Fact should point to its evidence.
- **Assumptions**: reasonable guesses that help you proceed but are not yet verified. Mark them explicitly as assumptions.
- **Unknowns**: things you have not yet checked.

A claim is a Fact only if you can point to the evidence. Otherwise it is an Assumption. An Assumption may guide the next step, but must not be reported as established.

---

## Step 8 — Sufficiency check

Answer each question with a specific reference, not a bare yes/no:

1. What files / modules will this task touch?
2. Why is this change needed?
3. How does the relevant code currently work?
4. What is the current observable behavior?
5. What else might break when we change this?
6. Where will we verify the change?
7. What facts are still unknown?

For each Unknown, classify it:

- **Relevant Unknown**: if left unchecked, it could change the implementation plan, defeat the goal, or cause a missed regression.
- **Irrelevant Unknown**: does not affect how this task is implemented or verified.

**Decision:**

- No Relevant Unknown remains → **READY_FOR_IMPLEMENTATION**.
- Relevant Unknowns remain → go to Step 9.
- A Relevant Unknown turns out to be a requirement / architecture / destructive-operation question, not a code-reading question → go to Step 10.

---

## Step 9 — Targeted re-investigation

For each Relevant Unknown, ask: **what single action will most directly resolve it?**

Possible actions: run a specific search, read a specific file, trace one call chain, run a specific command, inspect a specific config.

Do **not** broaden the search into "understand the whole project". Resolve the specific Unknown, then return to Step 8.

**Loop cap**: if you have done several targeted re-investigations and the same Unknown remains unresolved, treat it as an architectural or requirement question rather than a code-reading question → go to Step 10.

Irrelevant Unknowns are not chased. They are recorded as "known but not blocking" and left alone.

---

## Step 10 — Escalate

If during discovery you hit any of the following, stop and escalate per SKILL.md §8:

- The requirement itself is ambiguous between materially different interpretations.
- Two or more architectural directions are both reasonable and the choice significantly changes the implementation.
- The current observable behavior contradicts what the user described.
- The confirmed requirement would need to change.
- A major architectural change is required.
- A destructive or high-risk operation is required.

Use the escalation format defined in SKILL.md §8 verbatim:

```text
Problem
Known facts
What has been investigated
What has already been tried
Current evidence
Available options
Consequences
Decision required from the user
```

Do not invent a new format.

---

## Step 11 — Reconcile on new facts

If at any point a new observation contradicts your earlier understanding (e.g. you thought A→B→C but the code shows A→D→C):

1. Update Facts / Assumptions.
2. Re-run Step 8 against the new understanding.
3. Re-scope the Relevant Components list if the new fact points to a different area.
4. Do not continue implementing on top of the stale model.

This applies not only inside this workflow. If a later implementation step surfaces a contradiction, come back to this reconciliation step instead of forcing the old plan.

---

## Discovery Result

When discovery is sufficient, produce a structured result. This is the handoff to implementation.

```text
Task: ...
Goal: ...
Expected result: ...
Scope: <small | cross-module | architectural>

Relevant components:
  - <file/module>: <why relevant>

Current behavior:
  - <what it does today>

Relevant dependencies:
  - <upstream / downstream / config / entry point>

Existing verification:
  - <tests / run steps / manual checks>

Facts:
  - <claim> — evidence: <where observed>

Assumptions:
  - <claim> — why believed: <reason>

Relevant unknowns (resolved during discovery):
  - <unknown> — resolved by: <action>

Known irrelevant unknowns (recorded, not blocking):
  - <unknown>

Risks / constraints:
  - <what could break / what must stay unchanged>

Discovery conclusion:
  - READY_FOR_IMPLEMENTATION
  - NEEDS_MORE_DISCOVERY   (with the next targeted step)
  - BLOCKED_OR_ESCALATE     (with escalation summary)
```

---

## Handoff to implementation

The Discovery Result above is the input to the implementation workflow.

- Implementation starts from this result instead of re-scanning the project from zero.
- The `Scope` field (`small` / `cross-module` / `architectural`) is part of this result. Implementation should read it to understand the task's expected reach and implementation context. It is a discovery judgment about scope and complexity — not a risk score, permission level, approval gate, autonomy level, security level, or execution rule.
- Assumptions in the result must be verified early during implementation; they are not Facts.
- If implementation discovers a new fact that contradicts the result, run Step 11 (Reconcile) before continuing.
- Relevant Unknowns already resolved during discovery should not be re-litigated unless new evidence appears.

Discovery is a **phase**, not a one-time scan.

---

## Boundaries of this workflow

This workflow does **not**:

- Explain why discovery matters (see SKILL.md).
- Define how to write code, how to structure a diff, how to run tests, or how to commit.
- Choose a programming language, framework, build tool, IDE, model, or host.
- Decide how much code to write or how large a change to produce.
- Replace the implementation or verification workflows.
