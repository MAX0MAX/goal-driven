# Canonical Goal Contract

The Goal Contract is the stable artifact that `goal-driven` produces.

## Purpose

The contract defines work precisely enough that an agent, system, or person can understand the goal, respect the boundaries, and verify completion without rediscovering the task.

It exists to create a narrow, verifiable interface between clarification and execution. The contract states what must be true, what is in and out of scope, how completion is checked, and what boundaries must be preserved.

## Required Fields

Every Goal Contract must include all of these fields:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

A contract with missing fields is incomplete.

## Field Guidance

### `goal`

Write the end state in one sentence. Describe what will be true when the task is done, not the activity that might get there.

### `scope`

State what is in scope and out of scope. This is the boundary that prevents drift during execution.

### `success_criteria`

List the observable conditions that define completion. `success_criteria` are the only canonical basis for deciding whether the `goal` is complete.

Each criterion must be independently checkable and must satisfy all of these rules:

- it is binary enough to determine satisfied or not satisfied
- it describes an end condition, not an activity list or implementation step
- it avoids subjective or rhetorical wording unless the wording is converted into a checkable condition
- it contributes necessary coverage of the `goal` rather than repeating another criterion

The full set of criteria must cover the complete `goal`. If all criteria are satisfied, the `goal` is complete. If any criterion is not satisfied, the `goal` is not complete.

### `evidence`

Name the proof required for each criterion: commands, artifacts, diffs, review gates, tables, or linked outputs. Evidence should make the contract verifiable, not rhetorical.

Evidence proves or disproves existing criteria. It must not add hidden completion rules that were omitted from `success_criteria`.

### `guardrails`

Record limits, safety constraints, escalation triggers, dependencies, and approvals that control the work. Guardrails protect the contract from expanding into implied work.

## Output Format

Use a structured block that preserves the canonical field names.

```yaml
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
        - "<command, artifact, diff, or review gate>"
  guardrails:
    - "<constraint, escalation rule, or approval boundary>"
```

Use the exact field names even if a downstream adapter later reformats them.

## Runtime Companion Artifacts

The Goal Contract schema stays unchanged. Runtime artifacts live outside the contract body.

A runtime may preserve a companion `Verifier Verdict` artifact such as:

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "complete"
  reasons:
    - "<short validation rationale>"
  criteria_gaps: []
  evidence_gaps: []
```

That runtime artifact is optional from the contract's perspective. It is not a Goal Contract field, and it must not become a required part of the canonical schema.

## Adapter Metadata

Adapters may wrap a Goal Contract with host-specific metadata such as execution routing, owner, thread state, priority, or suggested next action.

That metadata belongs outside the canonical contract. It must not become required contract content, and it must not add, replace, or override canonical `success_criteria`.

## Consumption Rules

- The contract must be readable by a human and stable enough for automated consumers.
- Each `success_criteria` item must have corresponding `evidence`.
- Adapters and runtimes may add wrapper metadata or runtime verifier artifacts outside the contract body.
- Adapters may consume or wrap the contract, but must not redefine the canonical fields.
- The contract should not encode execution routing that a downstream agent, host, or workflow can decide from context.
