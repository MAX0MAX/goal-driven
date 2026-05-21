# Goal-Driven Installation

Install `goal-driven` for the current harness, then use `goal-contract-writer` to produce a canonical Goal Contract.

`goal-contract-writer` is the only normal user-facing entrypoint. The installed package also includes the paired `goal-contract-verifier` skill for mandatory fresh-subagent runtime gate checks and the paired `goal-progress-tracker` skill for mandatory execution-state logging while the goal is being executed. These runtime skills may still appear in the installed skills tree even though normal invocation starts with `goal-contract-writer`.

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
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker -a codex
```

User-wide Codex install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker -g -a codex
```

Claude Code project install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent claude-code
```

User-wide Claude Code install:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent claude-code -g
```

Codex and Claude Code together:

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent codex --agent claude-code
```

Company-internal Git repo install:

```bash
npx skills add https://github.com/<company>/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent codex --agent claude-code
```

Keep `goal-contract-writer`, `goal-contract-verifier`, and `goal-progress-tracker` in the install command so any runtime that executes a Goal Contract can enforce the mandatory tracker and verifier protocol from the same source tree. Normal users should still invoke only `goal-contract-writer`.

## Simple Prompt Use

Users do not need to know the schema first. They can ask in plain language:

```text
Use goal-contract-writer to turn this request into a Goal Contract: <your request>
```

Explicit Codex invocation:

```text
$goal-contract-writer Turn this request into a Goal Contract: <your request>
```

Explicit Claude Code invocation:

```text
/goal-driven:goal-contract-writer Turn this request into a Goal Contract: <your request>
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
/goal-driven:goal-contract-writer Turn this request into a Goal Contract: <your request>
```

## Output Standard

A successful `goal-contract-writer` run returns one canonical artifact:

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

## Mandatory Runtime Protocol

If a runtime executes a Goal Contract, this protocol is mandatory:

1. Produce or update `Goal Progress Log` before the first work step.
2. Dispatch `goal-contract-verifier` in a fresh subagent.
3. Read exactly one structured `Verifier Verdict`.
4. Start or continue execution only when that verifier result allows it.

The same sequence repeats at every required runtime gate:

- execution start before the first work step
- execution path changes
- blocker appearance or blocker-state change
- any claim of completion

At each required gate, tracker update happens first and verifier dispatch happens second.

Use this verification package:

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

Use the same three runtime states for `goal-progress-tracker`. If execution stops without completion, keep tracker status `blocked` and record the failed handoff context in the log instead of inventing a fourth runtime state.

A runtime that executes the contract must preserve the resulting companion artifact outside the contract body:

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

The verifier must run in a fresh subagent. The main execution context must not self-verify in place. If verifier dispatch fails, returns anything other than a structured `Verifier Verdict`, returns `revise contract`, returns `escalate`, or returns `status: blocked`, the runtime must fail closed and stop optimistic execution.

## Adapter Metadata

Host-specific metadata belongs outside the canonical Goal Contract.

Examples of optional wrapper metadata:

- execution routing
- owner
- priority
- suggested next action
- runtime verifier artifacts
- runtime progress artifacts

Adapters must not add, replace, or override canonical `success_criteria`.

## Runtime Tracking During Execution

Once a Goal Contract enters execution, the runtime must maintain `goal-progress-tracker` with current execution state.

Recommended package:

```yaml
Tracking Package:
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
  current_status: "on_track"
  completed_steps: []
  in_flight_step: "<current focus>"
  attempted_paths: []
  verified_evidence: []
  active_blockers:
    - "<blocking issue or none>"
  resume_hint:
    - "<where to continue from>"
```

The resulting companion artifact also stays outside the contract body:

```markdown
# Goal Progress Log

## Goal Summary
- Goal: ...
- Status: on_track
- Last checkpoint: 2026-05-20T15:00:00Z

## Current Execution State
- Completed since last checkpoint: ...
- Current focus: ...

## Attempted Paths
- Path: ...
  Result: worked
  Evidence: ...

## Verified Evidence
- Criterion: ...
  Proof: ...

## Active Blockers
- none

## Resume From Here
- Next action: ...
- Avoid repeating: ...

## Checkpoint History
### 2026-05-20T15:00:00Z - on_track
- Summary: ...
```

Start `Goal Progress Log` when execution starts, then update it before every required verifier gate and at any other meaningful checkpoint instead of reconstructing it only after completion or failure. Use it to show current execution state, attempted paths, verified evidence, blockers, and where a later agent or human should resume. Do not treat it as a replacement for the Goal Contract or the Verifier Verdict.

Required tracker updates include:

- execution start before the first verifier run
- execution path changes
- blocker appearance or blocker-state change
- verifier status changes
- session exit or handoff
- any claim of completion

If a verifier result blocks continuation, or verifier dispatch fails, update the tracker again to reflect the blocked state and resume guidance before handing off.

For the initial checkpoint, empty `completed_steps`, `attempted_paths`, and `verified_evidence` are valid. The runtime should render those sections explicitly as `none yet` instead of escalating.

## Failure Conditions

A `goal-contract-writer` run is incomplete if:

- the output is not a Goal Contract
- `success_criteria` are not binary, observable, and complete enough to define goal completion
- `evidence` does not prove the criteria or introduces hidden acceptance rules
- `scope` does not define meaningful boundaries
- `guardrails` omit known constraints or escalation triggers

Runtime verifier dispatch failures are fail-closed runtime events. If the host cannot obtain a fresh verifier result at a required gate, it must stop optimistic execution, record the blocked state in the tracker, and escalate outside the verifier.

## Updating

This repository is the single source of truth.

After changes, review the skill, references, plugin manifests, and install notes together, then reload the plugin or skill in Claude Code or Codex.
