# Contract Elicitation Protocol

Use this protocol to move from an ambiguous request to a complete Goal Contract.

## Purpose

Clarification exists to fill the contract fields with enough precision for later work and verification. The goal is not to prolong conversation; it is to remove ambiguity that would otherwise make the contract unreliable.

Elicitation exists to author the canonical contract. It does not authorize the agent to drift into planning, execution, or runtime verification policy.

## Required Outputs

Before elicitation is complete, the agent must be able to write all of these:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

## Clarification Rules

- Ask only the questions required to complete the contract.
- Separate the desired outcome from implementation preference.
- Push subjective requests toward explicit `success_criteria` and `evidence`.
- Push vague completion language toward binary, observable criteria that can stand as the only canonical completion rule.
- Make `scope` explicit enough that downstream systems can reject out-of-scope expansion.
- Keep `guardrails` concrete: dependencies, approvals, safety boundaries, and escalation triggers.
- Do not ask questions only to decide execution routing; downstream agents, hosts, and workflows can decide how to act on a complete contract.

## Exit Conditions

Leave elicitation only when all of these are true:

- `goal` can be restated in one sentence.
- `scope` has clear inclusions and exclusions.
- The set of `success_criteria` fully covers the `goal`.
- Every `success_criteria` item is binary and observable.
- `evidence` is specific enough to prove or disprove each criterion without introducing hidden acceptance rules.
- `guardrails` make escalation conditions explicit.

## Optional Working Notes

A runtime or human may keep clarification notes outside the canonical contract when they need provenance.

Useful note types include:

- the original user request
- clarification points that materially changed `goal`, `scope`, `success_criteria`, `evidence`, or `guardrails`
- unresolved questions that were explicitly escalated into `guardrails`

Those notes are optional working context. They are not Goal Contract fields and must not replace the canonical contract structure.

## Escalation Cases

Escalate instead of finalizing the contract when:

- the user outcome is still ambiguous after reasonable clarification
- no objective verification path exists
- required approvals, dependencies, or inputs are missing
- unresolved product or policy choices would change `scope` or `success_criteria`
- a requested adapter or host-specific output would require the skill to redefine the canonical contract
