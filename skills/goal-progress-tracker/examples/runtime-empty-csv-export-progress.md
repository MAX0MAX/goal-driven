# Example: Bugfix - Empty CSV Export Progress Log

## Existing Goal Contract

```yaml
Goal Contract:
  goal: "Fix the intermittent CSV export bug so the export button downloads a populated CSV for the affected dataset whenever exportable rows are present."
  scope:
    in_scope:
      - "Identify the conditions that produce an empty CSV despite exportable rows being present."
      - "Fix the export path responsible for the empty download."
      - "Add or update regression coverage for the identified failure condition."
      - "Verify the exported CSV contains the expected headers and rows for the reproduced scenario."
    out_of_scope:
      - "Redesign the export UI."
      - "Add new export formats."
      - "Rewrite unrelated data-loading, filtering, or reporting flows."
      - "Change CSV column definitions unless required to fix the empty export bug."
  success_criteria:
    - "The empty CSV failure condition is reproduced or otherwise isolated before the fix is finalized."
    - "The export button downloads a CSV with expected headers and rows when exportable rows are present."
    - "A regression test or documented verification covers the identified failure condition."
    - "The final diff excludes unrelated export or reporting redesign."
  evidence:
    - criterion: "Failure isolation"
      proof:
        - "Document the dataset, filters, permissions, or timing condition that caused the empty CSV."
        - "Point to the export path responsible for producing empty output."
    - criterion: "Correct CSV output"
      proof:
        - "Run the targeted test that verifies exported headers and row count for the affected scenario."
        - "Manually trigger export for the reproduced scenario and inspect the downloaded CSV contents if no automated browser test exists."
    - criterion: "Regression coverage"
      proof:
        - "Show the added or updated test case covering the failure condition."
        - "Run the relevant test command and record the passing result."
    - criterion: "Scope control"
      proof:
        - "Inspect the final diff for unrelated export formats, UI redesign, or broad reporting changes."
  guardrails:
    - "Escalate if the bug cannot be reproduced and no logs, sample dataset, or user scenario can isolate it."
    - "Escalate before changing CSV schema or report semantics."
    - "Do not mask the issue by returning placeholder rows or suppressing export errors."
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
          - "Verify the exported CSV contains the expected headers and rows for the reproduced scenario."
        out_of_scope:
          - "Redesign the export UI."
          - "Add new export formats."
          - "Rewrite unrelated data-loading, filtering, or reporting flows."
          - "Change CSV column definitions unless required to fix the empty export bug."
      success_criteria:
        - "The empty CSV failure condition is reproduced or otherwise isolated before the fix is finalized."
        - "The export button downloads a CSV with expected headers and rows when exportable rows are present."
        - "A regression test or documented verification covers the identified failure condition."
        - "The final diff excludes unrelated export or reporting redesign."
      evidence:
        - criterion: "Failure isolation"
          proof:
            - "Document the dataset, filters, permissions, or timing condition that caused the empty CSV."
            - "Point to the export path responsible for producing empty output."
        - criterion: "Correct CSV output"
          proof:
            - "Run the targeted test that verifies exported headers and row count for the affected scenario."
            - "Manually trigger export for the reproduced scenario and inspect the downloaded CSV contents if no automated browser test exists."
        - criterion: "Regression coverage"
          proof:
            - "Show the added or updated test case covering the failure condition."
            - "Run the relevant test command and record the passing result."
        - criterion: "Scope control"
          proof:
            - "Inspect the final diff for unrelated export formats, UI redesign, or broad reporting changes."
      guardrails:
        - "Escalate if the bug cannot be reproduced and no logs, sample dataset, or user scenario can isolate it."
        - "Escalate before changing CSV schema or report semantics."
        - "Do not mask the issue by returning placeholder rows or suppressing export errors."
  current_status: "blocked"
  completed_steps:
    - "Reproduced the empty CSV failure with a dataset that includes exportable rows and a specific permission combination."
    - "Patched the export query path so reproduced rows are no longer filtered out."
    - "Added a regression test for the reproduced condition."
  in_flight_step: "Waiting for browser-based export validation in staging because the local environment does not expose the same permission wiring."
  attempted_paths:
    - path: "Added placeholder rows when export output was empty."
      result: "failed"
      evidence:
        - "Rejected because it masks the issue and violates the Goal Contract guardrails."
    - path: "Patched the export query path to preserve rows for the reproduced permission state."
      result: "partial"
      evidence:
        - "Local regression test now passes for the reproduced condition."
        - "Manual staging export is still pending."
  verified_evidence:
    - criterion: "Failure isolation"
      proof:
        - "Bug notes capture the dataset, permission state, and code path that caused empty output."
    - criterion: "Regression coverage"
      proof:
        - "The new regression test passes in the affected export suite."
  active_blockers:
    - "Staging validation is blocked until an account with the affected permission combination is available."
  resume_hint:
    - "Get access to a staging account with the affected permission combination, rerun the manual export, and confirm the CSV contains expected headers and rows."
    - "Do not retry placeholder-row mitigations; they violate the guardrails and do not prove correct export behavior."
```

## Goal Progress Log

```markdown
# Goal Progress Log

## Goal Summary
- Goal: Fix the intermittent CSV export bug so the export button downloads a populated CSV for the affected dataset whenever exportable rows are present.
- Status: blocked
- Last checkpoint: 2026-05-20T15:00:00Z

## Current Execution State
- Completed since last checkpoint: Reproduced the failure, patched the export query path for the reproduced permission state, and added a regression test.
- Current focus: Waiting on staging validation with the affected permission combination before claiming the export path is fully fixed.

## Attempted Paths
- Path: Added placeholder rows when export output was empty.
  Result: failed
  Evidence: Rejected because it masks the issue and violates the Goal Contract guardrails.
- Path: Patched the export query path to preserve rows for the reproduced permission state.
  Result: partial
  Evidence: Local regression test now passes, but manual staging export remains unverified.

## Verified Evidence
- Criterion: Failure isolation
  Proof: Bug notes capture the dataset, permission state, and code path that caused empty output.
- Criterion: Regression coverage
  Proof: The new regression test passes in the affected export suite.
- Criterion: Correct CSV output
  Proof: Still unverified in staging for the affected permission combination.

## Active Blockers
- Staging validation is blocked until an account with the affected permission combination is available.

## Resume From Here
- Next action: Get access to a staging account with the affected permission combination, rerun the manual export, and verify the CSV contains expected headers and rows.
- Avoid repeating: Do not retry placeholder-row mitigations without new evidence; they violate the guardrails and do not prove the bug is fixed.

## Checkpoint History
### 2026-05-20T15:00:00Z - blocked
- Summary: Reproduction, code patch, and regression coverage are in place; final CSV validation is blocked on staging access.
```
