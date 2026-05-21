---
name: goal-contract-verifier
description: Use only as a fresh verifier subagent when a runtime or operator provides an existing Goal Contract plus current progress, evidence, and blockers and must independently judge execution state; not for normal user-facing contract drafting.
---

# Goal Contract Verifier

## Overview

`goal-contract-verifier` is the paired verification skill for Goal Contracts.

Use it when a runtime or operator dispatches a fresh verifier subagent to check current execution state against a previously authored Goal Contract. This skill does not rewrite the contract, does not continue into planning or execution, and does not return free-form review prose. It returns only a structured `Verifier Verdict`.

The `Verifier Verdict` is execution-state verification output, not advice. Once a runtime or operator dispatches this verifier, it must respect the verdict and status when interpreting whether current work still aligns with the Goal Contract.

## When To Use

Use this skill only when all of these are true:

- a Goal Contract already exists
- the verifier is running as a fresh subagent rather than the main execution context
- the verifier received current runtime state in the form of progress, evidence, and blockers
- the task is to decide one contract verdict and one runtime status

Do not use this skill to draft a contract from scratch, revise the contract in place, add adapter metadata, or continue into downstream execution.

## Required Input

Read the verification package in full before returning a verdict.

```yaml
Verification Package:
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
  current_progress:
    - "<what has been done, observed, or claimed so far>"
  current_evidence:
    - criterion: "<criterion or group>"
      proof:
        - "<actual command result, artifact, diff, review note, or observation>"
  current_blockers:
    - "<open blocker, unresolved approval, contradiction, or `none`>"
```

If any of these inputs are missing, contradictory, or too weak to support a runtime judgment, return `escalate`.

Use `references/contract-validation.md` for the full runtime validation protocol.

## Runtime Constraint

This verifier checks execution state when a runtime or operator invokes it. It does not decide when execution should start, and it does not take over planning, execution, recovery, or completion. Every invocation must run in a fresh subagent rather than the main execution context.

- `pass` means the Goal Contract still frames the current state; it does not mean the goal is complete unless `status` is also `complete`.
- `revise contract` means the current Goal Contract is not reliable enough to constrain the goal; the runtime must return to the authoring layer before treating the current state as aligned.
- `escalate` means the runtime lacks enough trustworthy input, evidence, approval, or consistency to judge alignment; the runtime must stop optimistic interpretation and escalate outside the verifier.
- `blocked` means execution is blocked; the runtime must not summarize the goal as complete.

## Validation Standard

Check the runtime package against these rules:

1. The contract still uses the canonical Goal Contract field names: `goal`, `scope`, `success_criteria`, `evidence`, and `guardrails`.
2. `current_progress` describes concrete runtime state rather than intention or vague optimism.
3. `current_evidence` maps to the contract's `success_criteria` without inventing new completion rules.
4. `current_blockers` makes unresolved approvals, contradictions, or missing prerequisites explicit.
5. The current runtime state is still compatible with the contract's stated scope and guardrails.
6. Determine runtime status from the current package rather than assuming `pass` means complete.
7. If the runtime package exposes missing, overlapping, or non-binary contract criteria, the verifier returns `revise contract` rather than papering over the contract gap.
8. Any host or adapter metadata stays outside the canonical contract.

## Output Rules

Return exactly one structured `Verifier Verdict` block and nothing else.

- Do not rewrite the contract.
- Do not suggest execution steps, planning flow, or implementation strategy.
- Do not add praise, summaries, or host-specific workflow prose before or after the verdict.
- Keep `reasons` concrete and tied to the contract or runtime package.
- Use only these verdict values: `pass`, `revise contract`, `escalate`.
- Use only these status values: `on_track`, `complete`, `blocked`.
- Any runtime or operator that receives anything other than this exact structured artifact must fail closed for that verification request.

Use this format:

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "on_track"
  reasons:
    - "<concrete runtime validation finding>"
  criteria_gaps: []
  evidence_gaps: []
```

When gaps are present, each item must use this shape:

```yaml
criteria_gaps:
  - criterion: "<affected or missing criterion>"
    gap: "<missing, overlapping, subjective, non-binary, or incomplete coverage>"
    reason: "<why this prevents reliable runtime judgment>"
evidence_gaps:
  - criterion: "<affected criterion>"
    gap: "<missing, weak, contradictory, or hidden-rule evidence>"
    reason: "<why current evidence cannot prove or disprove the criterion>"
```

## Status Semantics

`status` describes the runtime state of the goal, not the health of the contract.

- `complete`: current evidence satisfies every `success_criteria` item.
- `on_track`: the goal is not complete yet, but current progress and evidence remain consistent with the contract and no blocker currently prevents continued progress.
- `blocked`: the goal is not complete yet, and current blockers prevent continued progress or completion.

## Verdict Semantics

`verdict` describes whether the Goal Contract still works as the right frame for the runtime state.

- `pass`: the contract still frames the current runtime state correctly. This does not mean the goal is complete.
- `revise contract`: the runtime package shows that the Goal Contract needs tighter criteria, stronger evidence mapping, or clearer scope boundaries.
- `escalate`: the runtime package exposes missing input, contradiction, unresolved approval boundary, or another condition that must be escalated outside the verifier.

A runtime may therefore legitimately return combinations such as:

- `pass` + `on_track`
- `pass` + `complete`
- `pass` + `blocked`
- `revise contract` + `blocked`
- `escalate` + `blocked`

`pass` + `blocked` is a valid verifier result. It means the Goal Contract still frames the runtime state correctly, but execution remains blocked; consumers must preserve the blocked status and must not summarize the goal as complete or progress as unblocked.

## Failure Discipline

Return `pass` when the current runtime state does not expose a contract gap or escalation trigger.

Return `revise contract` when the runtime package shows that the Goal Contract itself needs to change.

Return `escalate` when:

- runtime state is too incomplete or contradictory to judge
- objective verification is not available for the claimed progress
- required approvals or constraints are unresolved in a way that requires escalation
- the package asks the verifier to draft, rewrite, or execute instead of validate

Never collapse `on_track` into `complete`, and never use `pass` as a shortcut for "done".

If runtime dispatch fails before this skill can return, that failure is a fail-closed verification event outside this skill.

## Runtime Boundary

This skill returns runtime validation only.

- `Goal Contract` remains the canonical artifact.
- `Verifier Verdict` remains a companion runtime artifact outside the canonical contract.
- Fresh-subagent dispatch and fail-closed handling are verifier invocation rules, not Goal Contract schema fields.
