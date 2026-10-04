# Workflow: Verification

> Operational workflow that takes an `Implementation Result` and decides, with evidence, whether the task's Goal has actually been achieved.
> Implementation produces change. Verification judges whether the change is correct. "Code was written" is not evidence that "the task is done".
> This workflow does **not** restate engineering principles (see SKILL.md) and does **not** re-run discovery or implementation (it hands control back to them when needed).

---

## Input: Implementation Result

This workflow starts only after `implementation.md` has produced an `Implementation Result`.

Check the `Implementation conclusion` field before doing anything else:

| Implementation conclusion | Action |
|---|---|
| `READY_FOR_VERIFICATION` | Proceed with this workflow. |
| `NEEDS_MORE_IMPLEMENTATION` | Return to `implementation.md`. Do not start verification. |
| `BLOCKED_OR_ESCALATE` | Stop. Do not start verification. Resolve the escalation path first. |

Read and keep in view: Task, Goal, What changed, New facts discovered during implementation, Assumptions still open, Risks that materialized, Recommended verification targets, Recommended verification scope.

---

## Step 1 — Independent judgment: reconstruct the Goal

The Implementation Result is **input evidence**, not the verdict. Verification does not accept "I finished the edits" as proof.

Reconstruct from scratch, using the original user requirement and the Discovery Result:

- **Goal**: what the user actually wants achieved.
- **Expected result**: the observable outcome that proves the Goal.
- **Constraints**: what must remain unchanged.
- **Must-stay behaviors**: existing behavior that must not regress.

If the Implementation Result's Recommended verification targets do not actually cover the Goal, verification must widen or reshape the targets itself. Do not mechanically verify whatever implementation suggested.

---

## Step 2 — Build Verification Targets

For each part of the Goal, define a Verification Target. A target has five fields:

```text
Target:             <what is being verified>
Why it matters:     <which part of the Goal it covers>
Expected result:    <what should be observed if the change is correct>
Constraints:        <what must not be broken by this check>
Verification method: <what method produces the proof, and what kind of observation will count as evidence>
```

Targets should cover every piece of the Goal that was in scope. Do not leave a critical Goal without a target.

---

## Step 3 — Scope the verification

Verification is not the whole project test suite. Three scopes:

1. **Direct verification**: exercise the behavior the task was about.
2. **Relevant regression verification**: re-check adjacent existing behavior that this change could plausibly affect, using the Relevant dependencies / Existing verification from the Discovery Result and the What-changed list from the Implementation Result.
3. **Unrelated verification**: the rest of the project.

Do **not** run unrelated verification. Principle:

> Verify what the change could reasonably affect, not everything that exists.

If the change's blast radius turns out wider than expected during verification, expand scope into the newly affected area. Do not expand it just to "be thorough".

---

## Step 4 — Choose the verification method and sufficient evidence

For each target, the Verification method must match the Expected result. A target about UI behavior is not settled by a build success; a target about API behavior is not settled by static inspection. Pick a method that directly observes what the Expected result claims.

Possible methods (non-exhaustive, technology-neutral):

- build / compilation success,
- automated tests,
- application startup,
- runtime behavior observed directly,
- API request / response observed,
- UI behavior observed,
- database / state changes observed,
- static inspection of the changed code.

**Testing is one means of verification, not the definition of it.** A task with no automated tests can still be verified by other observable evidence. Conversely, all tests passing does not by itself prove the user's Goal is met.

Do not collect maximum evidence. Collect evidence that is **sufficient** to decide each target.

---

## Step 5 — Execute and observe

Gather actual results. Start from raw **Observations** (what the Agent actually saw: a console line, a test output, a UI frame, a log entry). Not every observation becomes Evidence, and no observation by itself is Verification.

Keep three categories explicitly separated:

- **Evidence**: directly observable, reproducible facts (a test actually passed, a build actually succeeded, an input actually produced an output, a runtime behavior actually occurred).
- **Interpretation**: a judgment drawn from evidence (e.g. "because this test passed, this behavior matches expectation").
- **Assumption**: a premise that has not been verified (e.g. "production behaves the same as this environment").

Never present an Assumption as Evidence. Never present an Interpretation as raw Evidence.

---

## Step 6 — Compare Expected vs Actual

For each Verification Target:

```text
Expected result        (from Step 1)
        ↓
Verification method    (from Step 4)
        ↓
Actual result          (from Step 5)
        ↓
Compare
        ↓
Evidence-based conclusion
```

Do not write "test passed → task passed". Write: what was expected, what was actually observed, and whether the two match.

---

## Step 7 — Is evidence sufficient?

A target is settled only when:

- there is Actual evidence for it,
- Expected and Actual have been explicitly compared,
- the evidence is strong enough to support a clear conclusion.

If evidence is insufficient for a target:

- Acquire more targeted evidence for that target.
- Do not declare the whole task passed on partial evidence.

Apply sufficiency to the whole task, not per-target:

- Every critical part of the Goal has a target with real evidence.
- Expected vs Actual has been compared for each.
- No critical constraint is visibly violated.
- No critical Unknown remains open that would change the conclusion.
- The collected evidence supports a clear verdict.

**Sufficient ≠ maximum.** Scope of verification should match Task scope, Change scope, Risk, and Goal. A small task should not require a full-project regression pass.

If a critical Verification Target cannot be settled by reliable evidence (for example, the Goal is real UI behavior but only a build success was observed), do not mark the task `VERIFIED`. Choose the appropriate existing verdict instead: `NOT_VERIFIED` (evidence insufficient), `BLOCKED` (environment prevents the needed check), `NEEDS_REIMPLEMENTATION` (the change cannot produce the observable behavior), `NEEDS_REDISCOVERY` (the project model is wrong), or `NEEDS_ESCALATION` (the Goal itself needs a decision). Do not invent a new status for "partial" or "uncertain".

---

## Step 8 — When verification fails or reveals new facts

A failed check or an unexpected observation is not a cue to immediately edit code and re-run.

### 8.1 Capture the observation first

Record:

- what was expected,
- what was actually observed,
- what evidence supports the observation,
- which target / part of the Goal is affected.

### 8.2 Classify the root cause

| Class | Meaning | Where to send control |
|---|---|---|
| **Implementation problem** | The code does not correctly implement the intended change. | Return to `implementation.md`. |
| **Discovery / understanding problem** | The change was built on an incorrect model of the project. | Return to `project-discovery.md` for incremental discovery. |
| **Environment / external problem** | The verification itself cannot complete because of external state, environment, or dependencies outside the code. | Mark as **BLOCKED**, record known facts and environmental limits. Do not blame the code. |
| **Requirement / architecture problem** | Verification reveals the Goal itself is ambiguous, or the correct solution requires a major architecture change. | Escalate per SKILL.md §8. |

### 8.3 Prevent the verification loop

The dangerous loop is:

```text
Implementation → Verification FAIL → Implementation → Verification FAIL → …
```

Do not break this loop with a fixed retry counter. The real test is:

> Before the next attempt, has something actually changed — new evidence, a corrected hypothesis, a different approach?

If the next attempt is the same code change, the same verification, the same expectation, with no new information, that is a loop risk → stop, re-examine evidence, re-check the hypothesis, change strategy, or escalate.

### 8.4 New facts during verification

Verification may surface facts that contradict Discovery or Implementation:

- **Case A — irrelevant to the task**: record and continue.
- **Case B — affects the implementation**: return to `implementation.md`.
- **Case C — shows the project understanding was wrong**: return to `project-discovery.md`.
- **Case D — touches requirement / architecture / high-risk decision**: escalate per SKILL.md §8.

Do not hide a new fact inside a test result.

---

## Step 9 — Verdict

After evidence is sufficient, produce one final status:

| Status | Meaning |
|---|---|
| `VERIFIED` | Sufficient evidence shows the Goal is achieved and critical constraints hold. |
| `NOT_VERIFIED` | Current evidence is not enough to prove the Goal. This is not the same as "the code is wrong". |
| `BLOCKED` | Required verification cannot complete due to environment / external conditions. |
| `NEEDS_REIMPLEMENTATION` | Evidence shows the implementation is incorrect or incomplete. |
| `NEEDS_REDISCOVERY` | Evidence shows the project understanding was wrong. |
| `NEEDS_ESCALATION` | A requirement / architecture / high-risk decision needs the user. |

---

## Step 10 — Verification Result

Produce a structured result. This is the handoff to Delivery.

```text
Task: ...
Goal: ...

Verification scope:
  - Direct: <targets actually checked>
  - Relevant regression: <adjacent behavior re-checked>
  - Explicitly out of scope: <why>

Verification targets:
  - <target>
      Expected: ...
      Evidence: ...
      Actual: ...
      Comparison: <match / mismatch / inconclusive>

Evidence summary:
  - Confirmed evidence: <...>
  - Interpretations drawn: <...>
  - Still-open assumptions: <...>

Failures / findings:
  - <finding> — classified as: <implementation / discovery / environment / requirement-arch>

New facts discovered during verification:
  - <fact> — routed to: <continue / implementation / discovery / escalation>

Final status:
  - VERIFIED / NOT_VERIFIED / BLOCKED / NEEDS_REIMPLEMENTATION / NEEDS_REDISCOVERY / NEEDS_ESCALATION

Delivery recommendation:
  - READY_FOR_DELIVERY
  - NOT_READY                    (evidence incomplete)
  - BLOCKED                      (environment / external)
  - REQUIRES_REIMPLEMENTATION
  - REQUIRES_REDISCOVERY
  - REQUIRES_ESCALATION

Remaining limitations / remaining work:
  - <what was not verified and why>
```

**`VERIFIED` is not the same as "the user has received the delivery."** Verification proves the result; Delivery is the next layer that communicates it.

---

## Interface to downstream / upstream workflows

- **Upstream**: consumes the `Implementation Result`. Only `READY_FOR_VERIFICATION` enters.
- **Downstream to Delivery**: passes Goal, Actual result, Evidence, Verification status, Remaining limitations. Delivery decides how to communicate to the user. This workflow does **not** define the final user-facing report format.
- **Back to implementation**: on `NEEDS_REIMPLEMENTATION`, hand back with the classified failure and the evidence.
- **Back to discovery**: on `NEEDS_REDISCOVERY`, hand back with the fact that broke the earlier understanding.
- **Escalation**: on `NEEDS_ESCALATION`, use the SKILL.md §8 format.

---

## Boundaries of this workflow

This workflow does **not**:

- Re-explore the project (use `project-discovery.md`).
- Re-implement (use `implementation.md`).
- Define the final user-facing delivery / report format (Delivery's job).
- Bind to a specific language, framework, test tool, IDE, model, or host.
- Implement risk scoring, retry counters, auto-rollback, Git automation, CI/CD automation, multi-agent orchestration, or persistent verification storage.
- Explain why evidence matters or why blind retries are bad (see SKILL.md).
- Treat "all tests green" as automatically equivalent to "Goal achieved".
