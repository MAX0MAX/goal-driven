# Example: Goal Progress Log At Execution Start

## Existing Goal Contract

```yaml
Goal Contract:
  goal: "Fix the intermittent CSV export bug so the export button downloads a populated CSV for the affected dataset whenever exportable rows are present."
  scope:
    in_scope:
      - "Identify the conditions that produce an empty CSV despite exportable rows being present."
      - "Fix the export path responsible for the empty download."
      - "Add or update regression coverage for the identified failure condition."
    out_of_scope:
      - "Redesign the export UI."
      - "Add new export formats."
  success_criteria:
    - "The empty CSV failure condition is reproduced or otherwise isolated before the fix is finalized."
    - "The export button downloads a CSV with expected headers and rows when exportable rows are present."
  evidence:
    - criterion: "Failure isolation"
      proof:
        - "Document the dataset and code path that caused the empty CSV."
    - criterion: "Correct CSV output"
      proof:
        - "Verify exported headers and row count for the affected scenario."
  guardrails:
    - "Do not mask the issue by returning placeholder rows."
```

## Tracking Package

```yaml
Tracking Package:
  goal_contract:
    Goal Contract:
      goal: "Fix the intermittent CSV export bug so the export button downloads a populated CSV for the affected dataset whenever exportable rows are present."
      scope:
        in_scope:
          - "Identify the conditions that produce an empty CSV despite exportable rows being present."
          - "Fix the export path responsible for the empty download."
          - "Add or update regression coverage for the identified failure condition."
        out_of_scope:
          - "Redesign the export UI."
          - "Add new export formats."
      success_criteria:
        - "The empty CSV failure condition is reproduced or otherwise isolated before the fix is finalized."
        - "The export button downloads a CSV with expected headers and rows when exportable rows are present."
      evidence:
        - criterion: "Failure isolation"
          proof:
            - "Document the dataset and code path that caused the empty CSV."
        - criterion: "Correct CSV output"
          proof:
            - "Verify exported headers and row count for the affected scenario."
      guardrails:
        - "Do not mask the issue by returning placeholder rows."
  current_status: "on_track"
  completed_steps: []
  in_flight_step: "Begin by reproducing the empty CSV failure on the affected dataset."
  attempted_paths: []
  verified_evidence: []
  active_blockers:
    - "none"
  resume_hint:
    - "Start with the reproduction path for the affected dataset and record the exact failing condition before changing code."
```

## Goal Progress Log

```markdown
# Goal Progress Log

## Goal Summary
- Goal: Fix the intermittent CSV export bug so the export button downloads a populated CSV for the affected dataset whenever exportable rows are present.
- Status: on_track
- Last checkpoint: 2026-05-20T15:00:00Z

## Current Execution State
- Completed since last checkpoint: none yet
- Current focus: Begin by reproducing the empty CSV failure on the affected dataset.

## Attempted Paths
- none yet

## Verified Evidence
- none yet

## Active Blockers
- none

## Resume From Here
- Next action: Start with the reproduction path for the affected dataset and record the exact failing condition before changing code.
- Avoid repeating: none yet

## Checkpoint History
### 2026-05-20T15:00:00Z - on_track
- Summary: Execution started. No reproduction attempt, verified evidence, or blockers yet.
```
