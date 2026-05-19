# Goal Contract Evidence Patterns

Use these examples when drafting or reviewing a Goal Contract.

The point of each example is to strengthen `goal`, `scope`, `success_criteria`, `evidence`, and `guardrails` so the contract is objectively checkable.

If someone can explain why a contract is strong only by silently rewriting it, the contract is still weak.

## Software Delivery

Bad goal:

- `Improve the authentication flow.`

Good goal:

- `Implement the approved authentication flow so login, logout, and expired-session recovery behave as specified.`

Bad success criteria:

- `The auth flow looks complete.`
- `Authentication was implemented.`

Good success criteria:

- `The approved login, logout, and expired-session recovery scenarios all pass the targeted tests.`
- `Only the requested auth routes, handlers, and required shared helpers changed.`
- `The final diff contains no unrelated auth redesign or refactor work.`

Why this contract is objectively checkable:

- Each criterion is binary enough to judge satisfied or not satisfied.
- Together they cover behavior, scope control, and completion.
- The evidence proves the listed criteria instead of supplying hidden rules.

Evidence:

- Run the targeted test command for the auth scenarios.
- Inspect the final diff for scope discipline.
- Review the named entry points that the request called out.

Guardrails:

- Escalate if the expected auth behavior is still ambiguous.
- Escalate if the tests pass but the requested user flows remain undefined.
- Stop if the work starts pulling in unrelated auth redesign.

## Research Or Analysis

Bad goal:

- `Research the topic thoroughly.`

Good goal:

- `Answer the listed research questions with sourced conclusions and explicit uncertainties.`

Bad success criteria:

- `The report feels comprehensive.`
- `The research was done thoroughly.`

Good success criteria:

- `Each required question is answered.`
- `Each non-trivial factual claim cites a source.`
- `Unresolved uncertainty is called out explicitly.`

Why this contract is objectively checkable:

- Completion does not depend on taste or confidence.
- The criteria cover completeness, sourcing, and uncertainty handling.
- The criteria can be checked directly against the output.

Evidence:

- Map each question to the paragraph that answers it.
- Check that each factual claim cites a source or artifact.
- Confirm that uncertainties are labeled instead of implied away.

Guardrails:

- Escalate if the task depends on unavailable or conflicting sources.
- Stop at the agreed source budget or deadline.
- Reject unsupported synthesis as evidence.

## Data Or Migration Work

Bad goal:

- `Finish the migration.`

Good goal:

- `Complete the migration so the target schema, transformed data, and rollback path satisfy the approved migration plan.`

Bad success criteria:

- `The migration script ran successfully once.`
- `The migration work is done.`

Good success criteria:

- `The target schema matches the requested shape.`
- `Before and after invariants match the approved checks.`
- `Rollback instructions exist and are tested when required.`

Why this contract is objectively checkable:

- Running once is not treated as proof of correctness.
- The criteria define the finished state rather than the activity performed.
- The evidence directly proves schema, invariants, and rollback readiness.

Evidence:

- Run the schema inspection command.
- Run the invariant checks or row-count comparisons.
- Verify the rollback artifacts or instructions.

Guardrails:

- Escalate immediately on destructive uncertainty.
- Stop if validation reveals data loss risk.
- Require explicit approval before production-impacting steps.

## Spec Or Design Work

Bad goal:

- `Write a good design doc.`

Good goal:

- `Produce a design doc that defines scope, architecture, constraints, failure handling, and verification for the named feature.`

Bad success criteria:

- `The design is clear.`
- `The document is good enough.`

Good success criteria:

- `The document covers the required sections.`
- `No unresolved markers remain.`
- `Architecture, scope, and testing do not contradict each other.`

Why this contract is objectively checkable:

- The criteria can be checked by inspection without guessing author intent.
- The criteria cover required content, completeness, and internal consistency.
- The evidence proves the criteria instead of filling in missing acceptance rules.

Evidence:

- Check the written file for the required headings.
- Scan for unresolved markers or unfinished notes.
- Re-read the architecture and testing sections for contradictions.

Guardrails:

- Escalate if unresolved product choices remain.
- Stop if implementation details depend on unapproved requirements.
- Reject prose that suggests execution without defining verification.

## Common Contract Weaknesses

A contract is weak when:

- the criteria use words like `clear`, `complete`, `good`, or `reasonable` without turning them into checkable conditions
- the criteria describe activity performed instead of the completed state
- the criteria cover only part of the goal and leave completion to implication
- the evidence names checks that imply extra acceptance rules not present in `success_criteria`
- someone would need to rewrite the contract instead of evaluating the contract that was provided
