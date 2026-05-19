# Goal-Driven

Goal-Driven is a contract-first skill package for coding agents. Its public entrypoint turns an ambiguous request into a standard Goal Contract.

This repository is the single source of truth for the package. `skills/goal-driven/` holds the public authoring skill. `skills/goal-contract-verifier/` holds the paired verifier skill for isolated runtime verification; it may still be visible in host skill listings. `.claude-plugin/` and `.codex-plugin/` are thin wrappers for Claude Code and Codex.

`goal-driven` is the only normal user-facing entrypoint. Its job is to turn an ambiguous request into the canonical Goal Contract artifact. `goal-contract-verifier` is a separate paired skill that runtimes may call with isolated context when they need independent verification.

## Core Idea

`goal-driven` is the narrow layer between clarification and execution.

Its value is not asking more questions. Its value is producing a stable contract that downstream systems and humans can understand, verify, execute, or turn into more detailed work without rediscovering the original intent.

The verifier layer is intentionally separate from the canonical contract. Runtime artifacts such as `Verifier Verdict`, subagent isolation, and fail-closed behavior belong to adapters and host workflows, not to the Goal Contract schema itself.

The project follows four principles:

- Contract over conversation: discussion only exists to fill the contract.
- Verifiability over intuition: completion must be judged by evidence, not agent confidence.
- Execution-neutral by default: the contract defines the goal, evidence, and boundaries while downstream systems decide how to act on it.
- Upstream first: `goal-driven` defines the work; downstream tools plan, implement, test, review, or persist specs.

It is not a replacement for Superpowers, OpenSpec, Spec Kit, Codex goal state, or any execution workflow. It is the goal artifact those systems can consume.

## What It Produces

Every successful `goal-driven` run produces a Goal Contract with these fields:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

`success_criteria` are the only canonical basis for deciding whether the `goal` is complete. `evidence` proves those criteria; it does not add extra acceptance rules.

Runtimes may later pair the Goal Contract with a companion `Verifier Verdict` artifact, but that runtime artifact stays outside the canonical contract. Keep the behavior details in [skills/goal-driven/SKILL.md](skills/goal-driven/SKILL.md), verifier behavior in [skills/goal-contract-verifier/SKILL.md](skills/goal-contract-verifier/SKILL.md), and host-facing runtime rules in [skills/goal-driven/INSTALL.md](skills/goal-driven/INSTALL.md).

## Example

Request:

```text
Improve checkout so users understand failed payments.
```

Goal Contract:

```yaml
Goal Contract:
  goal: "Improve the checkout payment-failure flow so users understand why payment failed and what they can do next."
  scope:
    in_scope:
      - "Clarify payment failure messaging in checkout."
      - "Define retry and alternate-payment guidance for failed payments."
      - "Keep the work limited to the checkout failure experience."
    out_of_scope:
      - "Redesign the full checkout flow."
      - "Change payment processor behavior."
      - "Add new payment methods."
  success_criteria:
    - "Payment failure messages explain the failure state and next action."
    - "Retry or alternate-payment guidance is visible in the checkout failure path."
    - "The final plan excludes unrelated checkout redesign."
  evidence:
    - criterion: "Payment failure messaging and guidance"
      proof:
        - "Review the affected checkout failure screen or copy artifact."
        - "Map each success criterion to the planned implementation or test coverage."
    - criterion: "Scope control"
      proof:
        - "Inspect the plan or diff for unrelated checkout, processor, or payment-method changes."
  guardrails:
    - "Escalate if the desired failure copy or supported next actions are still undefined."
    - "Do not change payment processor integrations without explicit approval."
```

## When To Use It

Use `goal-driven` when:

- the request has a concrete outcome but starts ambiguous
- the next agent, system, or person needs a stable goal artifact
- success must be judged by evidence, not intuition

Do not use it as a replacement for planning, execution, testing, or review.

## Quick Start

To understand the agent-facing behavior, follow [skills/goal-driven/SKILL.md](skills/goal-driven/SKILL.md).

To install or invoke the public `goal-driven` entrypoint in Claude Code or Codex, follow [skills/goal-driven/INSTALL.md](skills/goal-driven/INSTALL.md).

The installed package also includes `skills/goal-contract-verifier/` as the paired verifier skill for subagent use. It may still appear in the installed skills tree. Normal users should invoke only `goal-driven` unless their runtime explicitly asks for verifier input.

For complete before-and-after examples, see [skills/goal-driven/examples/](skills/goal-driven/examples/).

## Repository Layout

- `skills/goal-driven/`
  Public authoring skill, references, examples, and install notes.
- `skills/goal-contract-verifier/`
  Paired verifier skill for isolated runtime verification; it may still appear in host skill listings.
- `.claude-plugin/`
  Claude Code wrapper.
- `.codex-plugin/`
  Codex wrapper.

## Adapter Use

`goal-driven` is upstream of execution.

- Use a workflow system when follow-up work is planning, implementation, testing, or review.
- Use a specification system when follow-up work is persistent spec or task work.
- Use a human operator or host-native path when execution belongs outside the skill.

Adapters may add optional routing, owner, priority, suggested-next-action metadata, or runtime verifier artifacts outside the canonical contract. Do not make that metadata part of the required Goal Contract.

Adapters must not add, replace, or override canonical `success_criteria`. Any host-specific checks stay outside the contract and do not redefine goal completion.

## Source Of Truth

- Repository: https://github.com/MAX0MAX/goal-driven
- Canonical public skill directory: `skills/goal-driven/`
- Paired verifier skill: `skills/goal-contract-verifier/` (intended for isolated runtime verification and may still appear in the installed skills tree)
- Agent entrypoint: `skills/goal-driven/SKILL.md`
- Install guide: `skills/goal-driven/INSTALL.md`
- Claude Code wrapper: `.claude-plugin/`
- Codex wrapper: `.codex-plugin/`
