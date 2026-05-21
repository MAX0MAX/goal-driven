# Downstream Adapters

Adapters consume the Goal Contract after `goal-contract-writer` produces it.

## Purpose

This document explains how downstream systems use the contract without changing its schema or project thesis.

## Adapter Rules

- Adapters consume the contract; they must not redefine the canonical fields.
- Adapters may add presentation, routing, ownership, priority, runtime state, or next-action metadata outside the contract.
- Adapters may preserve companion runtime artifacts such as `Verifier Verdict` and `Goal Progress Log`, but those runtime artifacts must stay outside the canonical contract.
- If an adapter executes a Goal Contract, it must maintain `Goal Progress Log`, dispatch `goal-contract-verifier` in a fresh subagent at the required gates, and preserve verifier results as binding runtime semantics.
- Adapters must not replace measurable contract content with free-form workflow prose.
- Adapters must not add, replace, or override canonical `success_criteria`.
- If a host needs more information, extend the wrapper around the contract, not the contract itself.
- Execution routing is optional adapter metadata, not required Goal Contract content.
- Adapters must not downgrade `revise contract`, `escalate`, or `blocked` verifier outputs into ordinary notes.
- Adapters must fail closed when verifier dispatch fails, verifier output is non-standard, verifier returns `revise contract` or `escalate`, or verifier returns `status: blocked`.

## Host-Native Goal Systems

Use the Goal Contract as durable objective input.

- Preserve the canonical fields.
- Map `goal` and `success_criteria` into host goal state as needed.
- Use `guardrails` to shape execution boundaries.
- Keep host-specific pause, resume, and execution control outside the contract body.
- If the host starts executing a Goal Contract, it must create `Goal Progress Log` before the first work step and keep the resulting log as runtime metadata.
- At each required gate, the host must update tracker state first, then call `goal-contract-verifier` with isolated context in a fresh subagent, then decide whether execution may continue.
- Host goal state must respect the verifier's runtime constraint semantics.

## Workflow Systems

Use the Goal Contract as upstream input to planning, verification, or execution workflows.

- Treat the contract as the starting artifact, not as optional context.
- Choose the workflow path from host context, user intent, or wrapper metadata rather than requiring the contract to encode it.
- Keep workflow ownership in the downstream system; do not move it back into `goal-contract-writer`.
- Keep host-specific review gates outside the canonical contract; they may govern local workflow, but they do not redefine goal completion.
- Required verifier gates are execution start, execution path changes, blocker appearance or blocker-state changes, and any claim of completion.
- At those gates, use `goal-contract-verifier` against `Goal Contract + current_progress + current_evidence + current_blockers` in a fresh subagent.
- Once the runtime begins execution, use `goal-progress-tracker` against `Goal Contract + current_status + completed_steps + attempted_paths + verified_evidence + active_blockers + resume_hint` and keep updating it before those gates and at any handoff checkpoint.
- Fresh-subagent isolation and fail-closed handling are mandatory runtime protocol rules for executing hosts, not Goal Contract fields.
- If workflow systems consume a `Verifier Verdict`, `revise contract` returns work to the authoring layer and `escalate` leaves verifier authority.
- If workflow systems consume a `Goal Progress Log`, treat it as the maintained recovery record for current execution state rather than as a replacement plan or contract.
- `Goal Progress Log` may record `Next action` and `Avoid repeating`, but it must not replace a detailed execution plan, add new success criteria, or authorize a new execution path on its own.

Common mappings:

- Superpowers planning: use the contract as the approved requirement input for `writing-plans`.
- Superpowers implementation: use the contract and plan to choose `subagent-driven-development` or `executing-plans`.
- Superpowers verification or review: use the contract's `success_criteria` and `evidence` as the checklist, and if execution is active, preserve the mandatory runtime verifier gate outputs.
- Superpowers recovery or handoff: once implementation begins, preserve a `Goal Progress Log` at checkpoints so the runtime always has durable visibility into attempted paths, verified evidence, blockers, and resume guidance.
- Generic workflow systems: map `goal`, `scope`, and `guardrails` into the workflow intake, then keep runtime evidence attached to completion checks.

## Specification Systems

Use the Goal Contract as source material for persistent design, proposal, task, or spec-delta assets.

- Preserve the contract fields as upstream intent.
- Translate the contract into repository assets without changing what the contract means.
- Keep lifecycle management in the downstream system, not in `goal-contract-writer`.
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
- Preserve any runtime `Goal Progress Log` when the receiving person needs the execution handoff and recovery record.
