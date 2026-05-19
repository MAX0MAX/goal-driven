# Runtime Verification

Runtime verification checks whether current progress, evidence, and blockers still justify a Goal Contract as written.

This is a paired runtime protocol for `goal-contract-verifier`, not part of canonical Goal Contract generation. `goal-driven` authors the contract. Runtimes may later call `goal-contract-verifier` when they want an isolated verification pass.

## Verification Inputs

Read all of these inputs before validating:

- `goal_contract`
- `current_progress`
- `current_evidence`
- `current_blockers`

Then read the contract fields in full:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

## Verification Checks

1. Check that the Goal Contract still uses the canonical field names.
2. Check that `current_progress` describes real runtime state, not vague intent.
3. Check that `current_evidence` maps to the contract's `success_criteria` and does not add hidden acceptance rules.
4. Check that `current_blockers` makes unresolved constraints, approvals, or contradictions explicit.
5. Check that the current runtime state still fits the stated `scope` and `guardrails`.
6. Determine runtime `status` from current state instead of treating `pass` as a completion signal.
7. Check whether new runtime information exposes missing, overlapping, subjective, or non-binary contract criteria.
8. Check that any adapter metadata, if present, stays outside the canonical contract.

## Recommended Runtime Flow

1. Author the Goal Contract with `goal-driven`.
2. Let the runtime gather current progress, current evidence, and current blockers.
3. Invoke `goal-contract-verifier` when the runtime needs an independent check.
4. Consume exactly one `verdict` and one `status`.
5. If `verdict` is `revise contract`, update the Goal Contract in the authoring layer before treating the runtime state as validated.
6. If `verdict` is `escalate`, stop optimistic interpretation and resolve the blocker or contradiction before continuing.
7. Use `status` to distinguish `on_track`, `complete`, and `blocked` runtime states.

Fresh-subagent isolation and fail-closed behavior are runtime choices. Use them when the host needs stronger independence or conservative failure handling, but do not treat them as Goal Contract schema rules.

## Runtime Status

`status` describes goal progress:

- `complete`: current evidence satisfies every `success_criteria` item.
- `on_track`: not all criteria are satisfied yet, but the current state is still progressing within the contract.
- `blocked`: not all criteria are satisfied yet, and current blockers prevent continued progress or completion.

## Verdict Meaning

`verdict` describes contract validity:

- `pass`: the Goal Contract still frames the runtime state correctly. `pass` does not mean the goal is already complete.
- `revise contract`: the runtime package shows that the Goal Contract needs to change.
- `escalate`: the runtime package requires escalation outside verifier authority.

## Failure Outcomes

If verification fails, use one of these outcomes:

- `revise contract`: the contract no longer frames the runtime state precisely enough
- `escalate`: the runtime state depends on missing decisions, missing evidence, unresolved blockers, or contradictions

Advice text, workflow prose, or any non-standard verifier response should be treated as an escalation in runtimes that require strict structured outputs.

Common failure signals include partial goal coverage, unsupported evidence, evidence that hides missing criteria, subjective success criteria, process-only criteria, weak guardrails, or blockers that should have changed scope but never made it back into the contract.

## Verdict Format

The verifier should return only this structured artifact outside the Goal Contract body:

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "on_track"
  reasons:
    - "<short runtime validation rationale>"
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

With these rules:

- `verdict`: `pass`, `revise contract`, or `escalate`
- `status`: `on_track`, `complete`, or `blocked`
- `reasons`: concrete findings tied to the contract or runtime package
- `criteria_gaps`: structured gap items for any missing, overlapping, subjective, non-binary, or incomplete criteria exposed by runtime verification
- `evidence_gaps`: structured gap items for any criterion that current runtime evidence cannot prove or disprove
- the verifier must not return a rewritten contract

## Pass Standard

A verifier `pass` means all of these are true:

- the Goal Contract still frames the work clearly enough to judge the current state
- the current progress and evidence are consistent with the contract
- the runtime package does not require hidden criteria or adapter-only rules to interpret success

A verifier `pass` may therefore still report `status: on_track` or `status: blocked`.
