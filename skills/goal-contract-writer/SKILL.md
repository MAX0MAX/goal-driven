---
name: goal-contract-writer
description: Use when the user asks to define a goal, create a Goal Contract, or clarify a concrete task's goal, scope, success criteria, evidence, or guardrails before planning or execution.
---

# Goal Contract Writer

## Overview

`goal-contract-writer` turns ambiguous work into a standard Goal Contract.

Clarify only what is required to define the work. Then return the canonical contract. Conversation is not the product.

This skill does not implement, run, or manage a Goal-Driven agent loop. It defines the upstream contract that a loop, workflow engine, host goal system, or human operator can execute and verify.

`goal-contract-writer` is the only normal user-facing entrypoint. The paired `goal-contract-verifier` skill exists for runtimes or operators that need fresh-subagent verification of execution state, but verifier behavior is not part of this skill's required output.

## When to Use

Use this skill when:

- the request has a concrete outcome but starts ambiguous
- the next agent, system, or person needs a stable goal artifact
- success must be judged by observable evidence

Do not use it for open-ended ideation or as a replacement for execution workflows.

## Core Output

Produce exactly one canonical artifact:

1. `Goal Contract`

`Goal Contract` must contain exactly these fields:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

Do not add runtime verdicts, adapter metadata, or workflow prose to the contract body.

Use `references/canonical-goal-contract.md` for the canonical contract schema.

## Contract Generation Flow

1. Clarify until `goal` and `scope` can be written without guesswork.
2. Write `success_criteria` that fully cover the `goal` and can be checked without subjective judgment.
3. Write matching `evidence` that proves or disproves each criterion without adding hidden acceptance rules.
4. Record `guardrails` that prevent scope drift, unsafe assumptions, or missing approvals.
5. Return the canonical Goal Contract and stop.

Use `references/contract-elicitation-protocol.md` for clarification rules and `examples/` for concrete model contracts.

## Hard Rules

- Do not replace the contract with free-form advice or host-specific prose.
- Do not use subjective completion language.
- Do not add verifier workflow, execution routing, owners, priorities, or next steps to the canonical contract.
- Do not let `evidence` smuggle in acceptance rules that are missing from `success_criteria`.
- Do not let any adapter redefine the canonical contract fields.
- Do not continue into planning or execution unless the user explicitly starts a separate task.
- Keep the contract execution-neutral.

## Downstream Consumption

Downstream systems consume the contract after this skill defines it.

- A host-native goal system may use it as durable input.
- A workflow system may use it for planning, execution, verification, or review.
- A specification system may use it for persistent spec or task assets.
- A human operator may execute directly from it.
- Adapter-specific routing, ownership, runtime state, or verifier artifacts belong outside the canonical contract.
- Runtimes or operators may invoke the paired `goal-contract-verifier` to verify execution state, but that verification flow does not change what `goal-contract-writer` returns.

Use `references/downstream-adapters.md` for adapter rules and examples.

## References

- `references/canonical-goal-contract.md`
- `references/contract-elicitation-protocol.md`
- `references/evidence-patterns.md`
- `references/downstream-adapters.md`
- `examples/README.md`

## Resource Use

- Keep the canonical schema and pattern references in `references/`.
- Keep complete before-and-after examples in `examples/`.
- Keep interface metadata in `agents/`.
