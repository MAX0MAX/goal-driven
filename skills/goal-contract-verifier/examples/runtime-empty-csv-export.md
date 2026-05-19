# Example: Bugfix - Empty CSV Export Runtime Check

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

## Verification Package

```yaml
Verification Package:
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
  current_progress:
    - "The failure was reproduced with a dataset that includes exportable rows and a specific permission combination."
    - "The export query path was patched to preserve rows for that reproduced case."
    - "A regression test was added for the reproduced condition."
    - "The final diff stayed limited to the export bugfix path and the targeted regression test."
  current_evidence:
    - criterion: "Failure isolation"
      proof:
        - "Bug notes record the dataset, permission state, and code path that caused empty output."
    - criterion: "Correct CSV output"
      proof:
        - "The reproduced export now downloads a CSV with the expected header and row count."
    - criterion: "Regression coverage"
      proof:
        - "The added regression test passes in the affected export suite."
    - criterion: "Scope control"
      proof:
        - "The final diff is limited to the export bugfix path and the targeted regression test."
  current_blockers:
    - "none"
```

## Verifier Verdict

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "complete"
  reasons:
    - "Current evidence satisfies all four success criteria in the Goal Contract."
    - "The contract still frames the runtime state correctly and does not need revision."
  criteria_gaps: []
  evidence_gaps: []
```
