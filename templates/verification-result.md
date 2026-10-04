# Verification Result Template

> This is a **recording template**, not a workflow.
> It does not tell the Agent *how* to verify (that is `verification.md`). It defines the **shape of the result** that verification hands to the downstream Delivery step.
>
> Do not add verification steps, testing procedures, debug procedures, retry rules, failure-recovery procedures, or decision logic here. Those belong in `verification.md`.

---

## How to use

1. Run `verification.md` through to its verdict step.
2. Copy the skeleton below.
3. Fill every field using the evidence you actually observed, the comparisons you actually made, and the assumptions you could not close.
4. Choose exactly one Final Verification Status and exactly one Delivery Recommendation. They are separate fields with separate vocabularies — do not merge them.
5. Hand the filled result to the downstream Delivery step.

Do not fill this template while you are still gathering evidence. It is the output, not a scratchpad.

Remember: the `Implementation Result` you received upstream is **input evidence**, not a verdict. Verification rebuilt Goal, Expected result, and Constraints from the original requirement. This template records that independent judgment.

---

## Result skeleton

```text
Task:
    <one sentence, copied from the Implementation Result>

Goal:
    <what the user actually wants to achieve, rebuilt independently during verification>

Verification scope:

  Direct (must verify):
    - <the behavior this task was about>
    - ...

  Relevant regression (should re-check):
    - <adjacent behavior plausibly affected by this change>
    - ...

  Unrelated (observed but out of scope):
    - <what was seen but does not belong to this task's verification>

Verification targets:

  Target 1:
    What:           <what is being verified>
    Why it matters: <which part of the Goal it covers>
    Expected:       <what should be observed if the change is correct>
    Constraints:    <what must not be broken by this check>
    Evidence needed: <what kind of observation will count as proof>

  Target 2:
    ...

Expected result (rebuilt during verification):
    <the observable outcome that proves the Goal, as reconstructed from the original requirement>

Constraints (rebuilt during verification):
    - <what must remain unchanged>
    - ...

Evidence:

  Evidence 1:
    Source:     <tests / runtime behavior / logs / API response / UI behavior / database state / static inspection / other directly observable fact>
    Observation:<what was actually observed, reproducibly>

  Evidence 2:
    ...

Interpretation:
    - <judgment drawn from the evidence> — based on: <which evidence>

Actual result:
    <what the system actually did when exercised, as opposed to what the Implementation Result claimed>

Expected vs Actual comparison:

  Comparison 1:
    Expected:         <what was expected>
    Actual:           <what was observed>
    Match / Mismatch: [ ] match   [ ] mismatch   [ ] inconclusive
    Relevant evidence: <pointer to Evidence entries>

  Comparison 2:
    ...

Unverified assumptions:

  Assumption 1:
    Assumption:       <premise still relied on>
    Why unverified:   <why it could not be closed during this verification>
    Potential impact: <what might go wrong if it turns out false>

New facts discovered during verification:

  Fact 1:
    Fact:     <something observed that was not known before>
    Evidence: <how it was observed>
    Impact:   <whether it changes the picture / routes to implementation / discovery / escalation>

Findings (problems or unexpected observations):

  Finding 1:
    Finding:              <what was wrong or unexpected>
    Evidence:             <what supports this finding>
    Impact:               <how it affects the Goal / constraints / delivery>
    Related target:       <which Verification Target this finding belongs to>

Final Verification Status (choose exactly one):
    [ ] VERIFIED
    [ ] NOT_VERIFIED
    [ ] BLOCKED
    [ ] NEEDS_REIMPLEMENTATION
    [ ] NEEDS_REDISCOVERY
    [ ] NEEDS_ESCALATION

Delivery Recommendation (choose exactly one):
    [ ] READY_FOR_DELIVERY
    [ ] NOT_READY
    [ ] BLOCKED
    [ ] REQUIRES_REIMPLEMENTATION
    [ ] REQUIRES_REDISCOVERY
    [ ] REQUIRES_ESCALATION

Remaining limitations:

  Limitation 1:
    Limitation:     <what was not verified or not covered>
    Why it remains: <why it could not be closed>
    Delivery impact: [ ] prevents delivery   [ ] does not prevent delivery, but must be disclosed
```

---

## Field rules

These are filling rules, not verification rules.

1. **Evidence is directly observed and reproducible fact.** "Looks fine", "should work", "I think it's OK" are not Evidence.
2. **Interpretation is not Evidence.** It is a judgment drawn from Evidence, and must point back to the Evidence it relies on.
3. **Assumption is not Evidence.** A premise you have not verified goes into Unverified assumptions, not into Evidence.
4. **Expected and Actual are separate fields.** Do not collapse them into a single "test passed" line.
5. **Unverified items must be recordable.** If something could not be checked, write it down in Unverified assumptions or Remaining limitations. Do not leave it silent.
6. **Failure must be recordable.** A Mismatch in Comparison, or a non-empty Findings list, is the normal way to record failure. Do not erase it to make the result look cleaner.
7. **Blocking must be recordable.** When verification cannot complete because of environment or external state, use `BLOCKED` and record why in Remaining limitations.
8. **Re-implementation, re-discovery, and escalation must each be recordable** through their corresponding Final Verification Status values.
9. **Final Verification Status and Delivery Recommendation are two different fields.** The first describes what verification found; the second describes what the downstream delivery state should be. Do not merge them, and do not invent a third combined status.
10. **Do not create new status names.** Use exactly the six values listed for each field, verbatim.
11. **Evidence is not limited to automated tests.** It may come from runtime behavior, logs, API responses, UI behavior, database state, static inspection, or any directly observable fact.
12. **Findings record observations, not fixes.** Do not write the fix procedure here. That belongs in implementation / discovery workflows.
13. **Remaining limitations record what is still open, not what to do about it.** Do not define a remediation workflow here.
14. **Do not treat the upstream Implementation Result as proof.** This template records the independent judgment of verification.
15. **Do not impose a forced mapping between the two status fields.** The relationship between Final Verification Status and Delivery Recommendation is decided by the workflow based on the Findings and evidence; this template only records the result.

---

## Hand-off

This result is handed to the downstream Delivery step.

- The combination of `VERIFIED` + `READY_FOR_DELIVERY` is what tells the downstream step that the current result has enough evidence to be delivered to the user.
- Other combinations are not reinterpreted by this template. They are recorded as-is and the downstream step follows the Delivery Recommendation.
- This template records **what verification saw, what it concluded, and what remains open**. The actual act of telling the user — what was done, how it was verified, what the known limitations are — belongs to Delivery, not to this template.

If the Delivery Recommendation is `REQUIRES_REIMPLEMENTATION`, control returns to `implementation.md` together with the Findings. If it is `REQUIRES_REDISCOVERY`, control returns to `project-discovery.md` together with the New facts that broke the earlier understanding. If it is `REQUIRES_ESCALATION`, the SKILL.md §8 escalation format applies.
