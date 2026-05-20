# Example: Bugfix - Empty CSV Export

## User Request

```text
The export button sometimes downloads an empty CSV. Fix it.
```

## Clarification Notes

- The request is about an intermittent CSV export bug, not a redesign of the export system.
- Reproduction details are required before a reliable fix can be planned.
- The contract should stay focused on the bugfix outcome and the evidence needed to prove it.

## Goal Contract

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

## Why This Works

The contract forces the bugfix through reproduction, targeted correction, and regression evidence. It keeps the request from drifting into a broader export redesign while still making proof obligations explicit.

## Common Failure Mode

Avoid accepting "sometimes" as enough detail. If the failure cannot be reproduced or isolated, the contract should escalate instead of pretending a future agent has enough evidence.
