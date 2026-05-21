# Goal Contract Verifier Examples

These examples show how a runtime can verify current state against an existing Goal Contract.

Each example includes:

- the existing Goal Contract
- current progress
- current evidence
- current blockers
- the resulting `Verifier Verdict`

## Examples

- [Bugfix: Empty CSV Export Runtime Check](runtime-empty-csv-export.md)

## Minimal Outcome Coverage

All verifier examples use the same input shape:

```yaml
Verification Package:
  goal_contract:
    Goal Contract:
      goal: "<goal>"
      scope:
        in_scope:
          - "<required work>"
        out_of_scope:
          - "<excluded work>"
      success_criteria:
        - "<observable completion condition>"
      evidence:
        - criterion: "<criterion>"
          proof:
            - "<required proof>"
      guardrails:
        - "<constraint>"
  current_progress: []
  current_evidence: []
  current_blockers: []
```

### `pass` + `blocked`

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "blocked"
  reasons:
    - "The Goal Contract still frames the runtime state correctly."
    - "Execution is blocked, so the goal is not complete."
  criteria_gaps: []
  evidence_gaps:
    - criterion: "Blocked criterion"
      gap: "blocked"
      reason: "The runtime cannot complete this criterion until the blocker is removed."
```

### `revise contract` + `blocked`

```yaml
Verifier Verdict:
  verdict: "revise contract"
  status: "blocked"
  reasons:
    - "The Goal Contract lacks observable success criteria for the runtime state."
  criteria_gaps:
    - criterion: "Unclear criterion"
      gap: "subjective"
      reason: "The criterion must be rewritten before execution can be judged."
  evidence_gaps: []
```

### `escalate` + `blocked`

```yaml
Verifier Verdict:
  verdict: "escalate"
  status: "blocked"
  reasons:
    - "The runtime state depends on missing input or approval outside verifier authority."
  criteria_gaps: []
  evidence_gaps:
    - criterion: "External decision"
      gap: "missing approval"
      reason: "The verifier cannot resolve this approval boundary."
```
