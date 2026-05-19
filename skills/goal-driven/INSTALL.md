# Goal-Driven Installation

Install `goal-driven` for the current harness, then use it to produce a canonical Goal Contract.

`goal-driven` is the only normal user-facing entrypoint. The installed package also includes the paired `goal-contract-verifier` skill for runtimes that want isolated verification later. That verifier skill may still appear in the installed skills tree even though normal invocation starts with `goal-driven`.

## Claude Code

Use the local plugin wrapper in `.claude-plugin/`.

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
/goal-driven:goal-driven Turn this task into a Goal Contract with explicit scope, success_criteria, evidence, and guardrails.
```

## Codex

Use the repository root so Codex can read `.codex-plugin/plugin.json` and `skills/goal-driven/` from the same source tree.

If you manage skills directly, install the full `skills/` tree so both `goal-driven` and `goal-contract-verifier` are available from the same source tree.

Invoke:

```text
$goal-driven Turn this task into a Goal Contract with explicit scope, success_criteria, evidence, and guardrails.
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
      scope: {}
      success_criteria: []
      evidence: []
      guardrails: []
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
