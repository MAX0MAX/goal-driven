# Downstream Adapters

Adapters consume the Goal Contract after `goal-driven` produces it.

## Purpose

This document explains how downstream systems use the contract without changing its schema or project thesis.

## Adapter Rules

- Adapters consume the contract; they must not redefine the canonical fields.
- Adapters may add presentation, routing, ownership, priority, runtime state, or next-action metadata outside the contract.
- Adapters may preserve a companion `Verifier Verdict`, but that runtime artifact must stay outside the canonical contract.
- Adapters may decide when to invoke or preserve a verifier result, but if they consume a `Verifier Verdict`, they must preserve its binding runtime semantics.
- Adapters must not replace measurable contract content with free-form workflow prose.
- Adapters must not add, replace, or override canonical `success_criteria`.
- If a host needs more information, extend the wrapper around the contract, not the contract itself.
- Execution routing is optional adapter metadata, not required Goal Contract content.
- Adapters must not downgrade `revise contract`, `escalate`, or `blocked` verifier outputs into ordinary notes.

## Host-Native Goal Systems

Use the Goal Contract as durable objective input.

- Preserve the canonical fields.
- Map `goal` and `success_criteria` into host goal state as needed.
- Use `guardrails` to shape execution boundaries.
- Keep host-specific pause, resume, and execution control outside the contract body.
- If the host wants independent runtime verification, it may call `goal-contract-verifier` with isolated context and keep the resulting `Verifier Verdict` as runtime metadata.
- If the host consumes that verdict, host goal state must respect the verifier's runtime constraint semantics.

## Workflow Systems

Use the Goal Contract as upstream input to planning, verification, or execution workflows.

- Treat the contract as the starting artifact, not as optional context.
- Choose the workflow path from host context, user intent, or wrapper metadata rather than requiring the contract to encode it.
- Keep workflow ownership in the downstream system; do not move it back into `goal-driven`.
- Keep host-specific review gates outside the canonical contract; they may govern local workflow, but they do not redefine goal completion.
- If the runtime needs independent checks, use `goal-contract-verifier` against `Goal Contract + current_progress + current_evidence + current_blockers`.
- Fresh-subagent isolation and fail-closed handling are adapter policy choices, not Goal Contract rules.
- If workflow systems consume a `Verifier Verdict`, `revise contract` returns work to the authoring layer and `escalate` leaves verifier authority.

Common mappings:

- Superpowers planning: use the contract as the approved requirement input for `writing-plans`.
- Superpowers implementation: use the contract and plan to choose `subagent-driven-development` or `executing-plans`.
- Superpowers verification or review: use the contract's `success_criteria` and `evidence` as the checklist, and add runtime verifier passes only if the host wants an independent gate.
- Generic workflow systems: map `goal`, `scope`, and `guardrails` into the workflow intake, then keep runtime evidence attached to completion checks.

## Specification Systems

Use the Goal Contract as source material for persistent design, proposal, task, or spec-delta assets.

- Preserve the contract fields as upstream intent.
- Translate the contract into repository assets without changing what the contract means.
- Keep lifecycle management in the downstream system, not in `goal-driven`.
- Do not turn spec-local checklists into replacement canonical completion criteria.

Common mappings:

- OpenSpec: translate `goal` and `scope` into proposal or design intent, then turn `success_criteria`, `evidence`, and `guardrails` into task and review gates.
- Spec Kit: use the contract before `/specify`; carry `scope` into the spec, `guardrails` into constraints, and `evidence` into plan or task verification.
- Generic specification systems: store the full contract as upstream intent so later spec changes can be checked against the original scope.

## Human Operator

Use the Goal Contract directly when a person is the next executor.

- Present the contract in readable structured form.
- Preserve `guardrails` and `evidence` so the hand-off remains verifiable.
- Preserve any runtime `Verifier Verdict` only when the receiving person needs the runtime validation record.
