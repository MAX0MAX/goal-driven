# Goal-Driven

Goal-Driven is a contract-first skill package for coding agents. Its public authoring entrypoint `goal-contract-writer` turns an ambiguous request into a standard Goal Contract.

This repository is the single source of truth for the package. `skills/goal-contract-writer/` holds the public authoring skill. `skills/goal-contract-verifier/` holds the paired verifier skill for mandatory fresh-subagent runtime gate checks. `skills/goal-progress-tracker/` holds the paired runtime tracking skill for mandatory execution-state logs. Paired runtime skills may still be visible in host skill listings. `.claude-plugin/` and `.codex-plugin/` are thin wrappers for Claude Code and Codex.

`goal-contract-writer` is the only normal user-facing entrypoint. Its job is to turn an ambiguous request into the canonical Goal Contract artifact. `goal-contract-verifier` is the paired mandatory runtime validation skill. `goal-progress-tracker` is the paired mandatory runtime tracking skill that must be maintained once the Goal Contract enters execution so the runtime preserves durable handoff state instead of trying to reconstruct it after the fact.

## Core Idea

`goal-contract-writer` is the narrow layer between clarification and execution.

Its value is not asking more questions. Its value is producing a stable contract that downstream systems and humans can understand, verify, execute, or turn into more detailed work without rediscovering the original intent.

The verifier and tracker layers are intentionally separate from the canonical contract. Runtime artifacts such as `Verifier Verdict` and `Goal Progress Log` belong to adapters and host workflows, not to the Goal Contract schema itself. In runtime use, `goal-contract-writer` defines the objective, `goal-progress-tracker` records execution state at required checkpoints, and `goal-contract-verifier` must run in a fresh subagent at required runtime gates before execution may start or continue.

The project follows four principles:

- Contract over conversation: discussion only exists to fill the contract.
- Verifiability over intuition: completion must be judged by evidence, not agent confidence.
- Execution-neutral by default: the contract defines the goal, evidence, and boundaries while downstream systems decide how to act on it.
- Upstream first: `goal-contract-writer` defines the work; downstream tools plan, implement, test, review, or persist specs.

It is not a replacement for Superpowers, OpenSpec, Spec Kit, Codex goal state, or any execution workflow. It is the goal artifact those systems can consume.

## What It Produces

Every successful `goal-contract-writer` run produces a Goal Contract with these fields:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

`success_criteria` are the only canonical basis for deciding whether the `goal` is complete. `evidence` proves those criteria; it does not add extra acceptance rules.

Runtimes may wrap the Goal Contract with companion runtime artifacts such as `Verifier Verdict` and `Goal Progress Log`, but those artifacts stay outside the canonical contract. If a runtime executes a Goal Contract, it must create a `Goal Progress Log` at execution start, update it at required checkpoints, dispatch `goal-contract-verifier` in a fresh subagent at required runtime gates, and fail closed when verification is missing, malformed, blocked, `revise contract`, or `escalate`. Keep the behavior details in [skills/goal-contract-writer/SKILL.md](skills/goal-contract-writer/SKILL.md), verifier behavior in [skills/goal-contract-verifier/SKILL.md](skills/goal-contract-verifier/SKILL.md), tracker behavior in [skills/goal-progress-tracker/SKILL.md](skills/goal-progress-tracker/SKILL.md), and host-facing runtime rules in [skills/goal-contract-writer/INSTALL.md](skills/goal-contract-writer/INSTALL.md).

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

Use `goal-contract-writer` when:

- the request has a concrete outcome but starts ambiguous
- the next agent, system, or person needs a stable goal artifact
- success must be judged by evidence, not intuition

Do not use it as a replacement for planning, execution, testing, or review.

## Quick Start

Codex Marketplace plugin install:

```bash
npx codex-marketplace add MAX0MAX/goal-driven --plugin --project -y
```

Default Codex skill install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker -a codex
```

Default Claude Code install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent claude-code
```

Use `-g` for a user-wide install.

After installation, ask the agent in plain language:

```text
Use goal-contract-writer to turn this request into a Goal Contract: <your request>
```

In Codex, explicit invocation also works:

```text
$goal-contract-writer Turn this request into a Goal Contract: <your request>
```

In Claude Code, explicit invocation also works:

```text
/goal-driven:goal-contract-writer Turn this request into a Goal Contract: <your request>
```

The installed package also includes `skills/goal-contract-verifier/` as the paired verifier skill for mandatory fresh-subagent gate checks and `skills/goal-progress-tracker/` as the paired runtime tracking skill for mandatory handoff and recovery logs. They may still appear in the installed skills tree. Normal users should invoke only `goal-contract-writer`; runtimes that execute a Goal Contract must invoke tracker and verifier per the runtime protocol.

## Runtime Protocol

Once a runtime decides to execute a Goal Contract, this order is mandatory:

1. Create or update `Goal Progress Log` with the current execution state.
2. Dispatch `goal-contract-verifier` in a fresh subagent with the current verification package.
3. Read exactly one structured `Verifier Verdict`.
4. Continue execution only when the verifier result allows it.

Required verifier gates are:

- execution start before the first work step
- execution path changes
- blocker appearance or blocker-state change
- any claim that the goal is complete

At each required gate, update the tracker first, then run the fresh verifier subagent. If verifier dispatch fails, returns a non-standard artifact, returns `revise contract` or `escalate`, or returns `status: blocked`, the runtime must fail closed and stop optimistic execution.

For complete install, prompt, and marketplace options, follow [skills/goal-contract-writer/INSTALL.md](skills/goal-contract-writer/INSTALL.md). To understand the agent-facing behavior, follow [skills/goal-contract-writer/SKILL.md](skills/goal-contract-writer/SKILL.md).

For complete before-and-after examples, see [skills/goal-contract-writer/examples/](skills/goal-contract-writer/examples/).

## Marketplace Readiness

- Codex Marketplace reads `.codex-plugin/plugin.json` at the repository root and installs this package as a plugin.
- Open Agent Skill can read `skill.json` for discovery metadata once the repository meets its submission requirements.
- The Open Agent Skill category is `Developer Tools`; recommended tags are `goal-contract`, `agent-skills`, `codex`, `claude-code`, `verification`, and `workflow`.

## Repository Layout

- `skills/goal-contract-writer/`
  Public authoring skill, references, examples, and install notes.
- `skills/goal-contract-verifier/`
  Paired verifier skill for mandatory fresh-subagent runtime gate verification; it may still appear in host skill listings.
- `skills/goal-progress-tracker/`
  Paired tracker skill for mandatory runtime handoff and recovery logs; it may still appear in host skill listings.
- `.claude-plugin/`
  Claude Code wrapper.
- `.codex-plugin/`
  Codex wrapper.
- `.agents/plugins/marketplace.json`
  Repo-local Codex marketplace entry for company-internal plugin catalogs and local install testing.
- `skill.json`
  Open Agent Skill manifest for public discovery metadata.

## Adapter Use

`goal-contract-writer` is upstream of execution.

- Use a workflow system when follow-up work is planning, implementation, testing, or review.
- Use a specification system when follow-up work is persistent spec or task work.
- Use a human operator or host-native path when execution belongs outside the skill.

Adapters may add optional routing, owner, priority, suggested-next-action metadata, or runtime artifacts such as `Verifier Verdict` and `Goal Progress Log` outside the canonical contract. Do not make that metadata part of the required Goal Contract.

Adapters must not add, replace, or override canonical `success_criteria`. Any host-specific checks stay outside the contract and do not redefine goal completion. If an adapter executes a Goal Contract, it must maintain tracker state, dispatch fresh verifier subagents at the required gates, and fail closed when verification does not authorize continuation.

## Source Of Truth

- Repository: https://github.com/MAX0MAX/goal-driven
- Canonical public skill directory: `skills/goal-contract-writer/`
- Paired verifier skill: `skills/goal-contract-verifier/` (intended for mandatory fresh-subagent runtime verification and may still appear in the installed skills tree)
- Paired tracker skill: `skills/goal-progress-tracker/` (intended for mandatory runtime handoff and recovery logs and may still appear in the installed skills tree)
- Agent entrypoint: `skills/goal-contract-writer/SKILL.md`
- Install guide: `skills/goal-contract-writer/INSTALL.md`
- Claude Code wrapper: `.claude-plugin/`
- Codex wrapper: `.codex-plugin/`
- Codex marketplace entry: `.agents/plugins/marketplace.json`
