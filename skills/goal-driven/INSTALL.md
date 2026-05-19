# Goal-Driven Installation

Install `goal-driven` for the current harness, then use it to produce a canonical Goal Contract.

`goal-driven` is the only normal user-facing entrypoint. The installed package also includes the paired `goal-contract-verifier` skill for runtimes that want isolated verification later. That verifier skill may still appear in the installed skills tree even though normal invocation starts with `goal-driven`.

## Recommended: Codex Marketplace

Use the Codex Marketplace plugin install when you want the full package through Codex's plugin path.

Codex project install:

```bash
npx codex-marketplace add MAX0MAX/goal-driven --plugin --project -y
```

User-wide Codex install:

```bash
npx codex-marketplace add MAX0MAX/goal-driven --plugin --global -y
```

The plugin root is this repository root. It contains `.codex-plugin/plugin.json` and the full `skills/` tree.

## Alternative: npx Skills

Use the open Agent Skills installer when you want a direct skill install instead of the Codex plugin wrapper.

Codex project install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-driven --skill goal-contract-verifier -a codex
```

User-wide Codex install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-driven --skill goal-contract-verifier -g -a codex
```

Claude Code project install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-driven --skill goal-contract-verifier --agent claude-code
```

User-wide Claude Code install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-driven --skill goal-contract-verifier --agent claude-code -g
```

Codex and Claude Code together:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-driven --skill goal-contract-verifier --agent codex --agent claude-code
```

Company-internal Git repo install:

```bash
npx skills add https://github.com/<company>/goal-driven --skill goal-driven --skill goal-contract-verifier --agent codex --agent claude-code
```

Keep both `goal-driven` and `goal-contract-verifier` in the install command so runtimes that use isolated verification can find the paired verifier from the same source tree. Normal users should still invoke only `goal-driven`.

## Simple Prompt Use

Users do not need to know the schema first. They can ask in plain language:

```text
Use goal-driven to turn this request into a Goal Contract: <your request>
```

Explicit Codex invocation:

```text
$goal-driven Turn this request into a Goal Contract: <your request>
```

Explicit Claude Code invocation:

```text
/goal-driven:goal-driven Turn this request into a Goal Contract: <your request>
```

## Publish To Codex Marketplace

This repository is packaged as a root Codex plugin. Public Codex Marketplace submission uses the repository URL:

```text
https://github.com/MAX0MAX/goal-driven
```

Submit it through the signed-in Codex Marketplace submit page:

```text
https://www.codex-marketplace.com/submit
```

Codex Marketplace validates `.codex-plugin/plugin.json`, then adds the repository to the review queue. After approval, users can install it with:

```bash
npx codex-marketplace add MAX0MAX/goal-driven --plugin --project -y
```

## Company Codex Marketplace

For company distribution through the Codex plugin directory, publish a marketplace catalog that points at this plugin package. This repo includes a local marketplace entry at `.agents/plugins/marketplace.json` for local testing or as a starting point for an internal catalog.

Recommended company marketplace entry:

```json
{
  "name": "goal-driven",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/<company>/goal-driven.git",
    "path": "./",
    "ref": "main"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Productivity"
}
```

The marketplace path should point at the plugin root containing `.codex-plugin/plugin.json` and the full `skills/` tree.

## Open Agent Skill Readiness

This repository includes a root `skill.json` manifest for Open Agent Skill discovery. Use these submission values when the repository meets the marketplace star requirement:

- Repository: `https://github.com/MAX0MAX/goal-driven`
- Category: `Developer Tools`
- Tags: `goal-contract`, `agent-skills`, `codex`, `claude-code`, `verification`, `workflow`

## Claude Code

Use the local plugin wrapper in `.claude-plugin/`.

This is the Claude Code plugin path. For the default npx install path, use the Claude Code commands in [Alternative: npx Skills](#alternative-npx-skills).

Marketplace install:

```bash
/plugin marketplace add /Users/you/path/to/goal-driven/.claude-plugin/marketplace.json
/plugin install goal-driven@goal-driven-dev
```

Direct local load:

```bash
claude --plugin-dir /Users/you/path/to/goal-driven
```

Invoke:

```text
/goal-driven:goal-driven Turn this request into a Goal Contract: <your request>
```

## Output Standard

A successful `goal-driven` run returns one canonical artifact:

```yaml
Goal Contract:
  goal: "<one-sentence end state>"
  scope:
    in_scope:
      - "<required work>"
    out_of_scope:
      - "<excluded work>"
  success_criteria:
    - "<observable completion condition>"
  evidence:
    - criterion: "<criterion or group>"
      proof:
        - "<command, artifact, diff, or review gate>"
  guardrails:
    - "<constraint, escalation rule, or approval boundary>"
```

Required fields:

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

Do not stop at advice, brainstorming, or implementation. Do not add runtime verdicts or host routing fields to the canonical contract.

## Optional Runtime Verification

Runtimes that want an isolated check may call `goal-contract-verifier` later with current state.

Recommended package:

```yaml
Verification Package:
  goal_contract:
    Goal Contract:
      goal: "<one-sentence end state>"
      scope:
        in_scope:
          - "<required work>"
        out_of_scope:
          - "<excluded work>"
      success_criteria:
        - "<observable completion condition>"
      evidence:
        - criterion: "<criterion or group>"
          proof:
            - "<planned command, artifact, diff, or review gate>"
      guardrails:
        - "<constraint, escalation rule, or approval boundary>"
  current_progress:
    - "<what has been done or observed so far>"
  current_evidence:
    - criterion: "<criterion or group>"
      proof:
        - "<actual command result, artifact, diff, review note, or observation>"
  current_blockers:
    - "<open blocker, unresolved approval, contradiction, or `none`>"
```

Valid verifier verdicts remain:

- `pass`
- `revise contract`
- `escalate`

Runtime status values are:

- `on_track`
- `complete`
- `blocked`

A runtime may preserve the resulting companion artifact outside the contract body:

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "on_track"
  reasons:
    - "<short runtime validation rationale>"
  criteria_gaps: []
  evidence_gaps: []
```

When gaps are present, use this item shape:

```yaml
criteria_gaps:
  - criterion: "<affected or missing criterion>"
    gap: "<missing, overlapping, subjective, non-binary, or incomplete coverage>"
    reason: "<why this prevents reliable runtime judgment>"
evidence_gaps:
  - criterion: "<affected criterion>"
    gap: "<missing, weak, contradictory, or hidden-rule evidence>"
    reason: "<why current evidence cannot prove or disprove the criterion>"
```

Interpret that artifact with two separate dimensions:

- `verdict` says whether the Goal Contract still frames the runtime state correctly.
- `status` says whether the goal is `on_track`, `complete`, or `blocked` right now.

A runtime may therefore report `pass` while the goal is still `on_track` or `blocked`.

Fresh-subagent isolation and fail-closed behavior are runtime policy choices. Use them when the host needs stronger independence or conservative handling, but do not treat them as part of the Goal Contract schema.

## Adapter Metadata

Host-specific metadata belongs outside the canonical Goal Contract.

Examples of optional wrapper metadata:

- execution routing
- owner
- priority
- suggested next action
- runtime verifier artifacts

Adapters must not add, replace, or override canonical `success_criteria`.

## Failure Conditions

A `goal-driven` run is incomplete if:

- the output is not a Goal Contract
- `success_criteria` are not binary, observable, and complete enough to define goal completion
- `evidence` does not prove the criteria or introduces hidden acceptance rules
- `scope` does not define meaningful boundaries
- `guardrails` omit known constraints or escalation triggers

Runtime verifier dispatch failures are runtime concerns. If a host adopts fail-closed verification, enforce that policy in the runtime or adapter layer rather than by changing the Goal Contract schema.

## Updating

This repository is the single source of truth.

After changes, review the skill, references, plugin manifests, and install notes together, then reload the plugin or skill in Claude Code or Codex.
