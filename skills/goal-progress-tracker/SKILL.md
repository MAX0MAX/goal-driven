---
name: goal-progress-tracker
description: Use when a runtime or operator already has a Goal Contract and must maintain a human-readable Goal Progress Log that shows current execution state, attempted paths, verified evidence, blockers, and resume guidance.
---

# Goal Progress Tracker

## Overview

`goal-progress-tracker` is the paired runtime tracking skill for Goal Contracts.

Use it to maintain one recovery-oriented Markdown artifact: `Goal Progress Log`.

This skill does not define the goal, does not rewrite the Goal Contract, does not replace runtime verification, and does not continue into planning or execution. It records the current state of execution clearly enough that a human or later agent can inspect the work, understand what has already been tried, and continue from the right checkpoint. Once a Goal Contract enters execution, this tracker must be maintained from the initial checkpoint onward rather than reconstructed only after the work stops.

## When To Use

Use this skill only when all of these are true:

- a Goal Contract already exists
- the Goal Contract has entered execution or is about to enter execution
- the runtime or operator must maintain a durable Markdown status and recovery artifact that stays current during execution
- current progress, attempted paths, evidence, blockers, or resume guidance are available

Do not use this skill to draft a Goal Contract from scratch, judge contract validity, prescribe execution strategy, or replace a plan or checklist system.

Post-hoc reconstruction after execution has already finished is a protocol failure fallback when no maintained log exists. It is not compliant primary workflow.

## Required Input

Read the full tracking package before updating the log.

```yaml
Tracking Package:
  goal_contract:
    Goal Contract:
      goal: "<one-sentence end state>"
      scope:
        in_scope:
          - "<required work>"
        out_of_scope:
          - "<excluded work>"
      success_criteria:
        - "<observable completion condition>"
      evidence:
        - criterion: "<criterion or group>"
          proof:
            - "<planned command, artifact, diff, or review gate>"
      guardrails:
        - "<constraint, escalation rule, or approval boundary>"
  current_status: "on_track"
  completed_steps:
    - "<checkpoint-level completed work>"
  in_flight_step: "<current focus>"
  attempted_paths:
    - path: "<approach tried>"
      result: "<worked | failed | partial>"
      evidence:
        - "<command output, diff, observation, or review result>"
  verified_evidence:
    - criterion: "<success criterion or group>"
      proof:
        - "<actual evidence>"
  active_blockers:
    - "<blocking issue or none>"
  resume_hint:
    - "<where to continue from>"
  existing_log: "<optional current Goal Progress Log markdown>"
  verifier_verdict: "<optional Verifier Verdict>"
```

If the package does not contain enough information to let another agent or person resume safely, return `Tracking Escalation` instead of inventing a reassuring summary.

At each required verifier gate, the runtime must update this log before dispatching the fresh verifier subagent. If verifier dispatch fails or returns a blocking result, the runtime must update the log again to reflect the blocked handoff state.

Use `references/progress-log-format.md` for the log schema and checkpoint update rules.

Valid tracker status values are:

- `on_track`
- `blocked`
- `complete`

The tracker does not define a separate fourth terminal status. If execution stops without completion, keep `current_status: "blocked"` and record the failed handoff context in `Current Execution State`, `Active Blockers`, and `Checkpoint History`.

## Initial Checkpoint

The tracker must support the mandatory moment execution has just started.

For an initial checkpoint, these inputs are valid:

- `completed_steps: []`
- `attempted_paths: []`
- `verified_evidence: []`
- `active_blockers:` with `"none"`
- a non-empty `in_flight_step`
- a non-empty `resume_hint`

In that case, the output should explicitly record that no steps have completed yet, no approaches have been tried yet, and no evidence has been verified yet. Do not escalate just because execution has started before any attempts exist.

## Artifact Rules

Return exactly one of these outcomes:

1. `Goal Progress Log`
2. `Tracking Escalation`

`Goal Progress Log` is a runtime companion artifact outside the Goal Contract body.

- Do not modify canonical Goal Contract fields.
- Do not add or remove Goal Contract requirements.
- Do not replace `success_criteria` with tracker prose.
- Do not treat tracker notes as verifier authority.
- Do not replace a detailed execution plan or checklist.
- Do not invent new execution paths and present them as approved plan changes.
- The tracker may record `Next action` and `Avoid repeating`, but those notes stay within the current Goal Contract and current execution state.
- Do not turn the log into a full transcript.

## Checkpoint Update Discipline

Once execution starts, update the log at required gates and any other meaningful checkpoints such as:

- execution start before the first verifier run
- a key sub-goal is completed
- the execution path changes
- a new blocker appears
- blocker state changes
- verifier status changes
- the session is stopping and needs a handoff summary
- any claim of completion

Do not wait until the goal is complete or failed before starting the log. Every update must refresh the current-state sections so that reading the latest checkpoint is enough to resume.

## Output Format

When input is sufficient, return exactly one `Goal Progress Log` Markdown document.

The log must contain these sections in this order:

1. `Goal Summary`
2. `Current Execution State`
3. `Attempted Paths`
4. `Verified Evidence`
5. `Active Blockers`
6. `Resume From Here`
7. `Checkpoint History`

Use this heading skeleton:

```markdown
# Goal Progress Log

## Goal Summary
- Goal: ...
- Status: on_track
- Last checkpoint: 2026-05-20T15:00:00Z

## Current Execution State
- Completed since last checkpoint: ...
- Current focus: ...

## Attempted Paths
- Path: ...
  Result: worked
  Evidence: ...

## Verified Evidence
- Criterion: ...
  Proof: ...

## Active Blockers
- none

## Resume From Here
- Next action: ...
- Avoid repeating: ...

## Checkpoint History
### 2026-05-20T15:00:00Z - on_track
- Summary: ...
```

The latest state must be readable without scanning the full history. `Checkpoint History` preserves prior updates in condensed form.

For an initial checkpoint, write explicit empty-state lines such as:

- `Completed since last checkpoint: none yet`
- `Attempted Paths: none yet`
- `Verified Evidence: none yet`

## Escalation Format

If required recovery information is missing, return only this artifact:

```yaml
Tracking Escalation:
  reason: "<why the current package cannot support a resumable progress log>"
  missing_inputs:
    - "<required missing field or contradiction>"
```

Escalate when:

- there is no Goal Contract
- attempted paths are missing for a non-initial in-progress or blocked state where the runtime should already know what has been tried
- blockers are unresolved but not described
- resume guidance is absent for an unfinished goal
- the package is contradictory enough that a handoff log would mislead the next executor

## Failure Discipline

Do not write a vague status report such as "work in progress" or "almost done".

Make the handoff concrete:

- what is already done
- what has been tried
- what evidence is real
- what is still blocking
- where execution should resume
- which paths should not be retried without new input

If that standard cannot be met, escalate.

If verifier dispatch fails, or verifier returns `revise contract`, `escalate`, or `status: blocked`, the runtime must record that blocked handoff state in the log before continuing with any external escalation or contract revision flow.

## Runtime Boundary

`Goal Contract` remains the canonical objective artifact.

`Verifier Verdict` remains the runtime validation artifact.

`Goal Progress Log` remains the mandatory runtime handoff and recovery artifact that must be maintained throughout execution.

These three artifacts complement each other. None of them replaces the others.
