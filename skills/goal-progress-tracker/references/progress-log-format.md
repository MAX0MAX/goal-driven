# Goal Progress Log Format

`Goal Progress Log` is the durable Markdown companion artifact for runtime visibility and recovery.

It exists outside the canonical Goal Contract. Its job is to show where execution stands now and how to continue safely later.

## Required Sections

Every `Goal Progress Log` must contain these sections in this order:

1. `Goal Summary`
2. `Current Execution State`
3. `Attempted Paths`
4. `Verified Evidence`
5. `Active Blockers`
6. `Resume From Here`
7. `Checkpoint History`

## Section Guidance

### `Goal Summary`

Summarize the tracked goal and current runtime state.

Required fields:

- `Goal`
- `Status`
- `Last checkpoint`

Valid status values:

- `on_track`
- `blocked`
- `complete`

The tracker uses the same three runtime states as `goal-contract-verifier`.

If execution ends unsuccessfully without reaching completion, keep `Status: blocked` and record the failed handoff context in `Current Execution State`, `Active Blockers`, and `Checkpoint History`.

### `Current Execution State`

Describe where execution stands right now.

Include:

- the most important work completed since the last checkpoint
- the current focus or latest in-flight step
- any meaningful state change since the prior checkpoint

This section must answer: "What is the executor doing or waiting on right now?"

For an initial checkpoint, it is valid to say that no steps are complete yet and execution is beginning with the current focus.

### `Attempted Paths`

List the approaches already taken.

Each path should record:

- the approach itself
- the result: `worked`, `failed`, or `partial`
- the concrete evidence supporting that result

Use this section to prevent repeated dead ends.

For an initial checkpoint, write `none yet` instead of escalating.

### `Verified Evidence`

Map actual evidence back to the Goal Contract's `success_criteria`.

For each criterion or criterion group, show:

- what has already been proven
- what remains unverified, if any

Do not use this section to create new acceptance rules. Evidence supports the Goal Contract; it does not replace it.

For an initial checkpoint, write `none yet` when no criterion has been verified.

### `Active Blockers`

Record blockers that currently prevent completion or continued progress.

Each blocker should explain:

- what is blocking
- why it matters now
- what missing input, permission, dependency, or decision would unblock it

If there is no blocker, write `none`.

### `Resume From Here`

This is the most important recovery section.

It must tell the next person or agent:

- what to inspect first
- what to do next
- which attempted paths should not be repeated without new evidence

If the goal is unfinished and this section is vague, the log is not good enough.

This section may record a `next action`, but it must not replace a detailed execution plan, add new success criteria, or authorize a new execution path on its own.

### `Checkpoint History`

Keep a compact history of meaningful checkpoints.

Each checkpoint entry should include:

- timestamp
- status at that checkpoint
- short summary of what changed

The history is append-only, but the earlier sections must always reflect the latest state.

## Update Protocol

Update the log only at meaningful checkpoints:

- key sub-goal completed
- execution path changed
- new blocker introduced
- verifier status changed
- session exit or handoff

Do not append a new entry for every minor action. The log is a recovery artifact, not a raw transcript.

At each update:

1. Refresh `Goal Summary` with current status and timestamp.
2. Rewrite `Current Execution State` to reflect the latest checkpoint.
3. Append any new attempted paths or update their result.
4. Refresh `Verified Evidence` with newly confirmed proof.
5. Refresh `Active Blockers`.
6. Rewrite `Resume From Here` so the next executor can continue immediately.
7. Append one condensed item to `Checkpoint History`.

## Failure Standard

A good `Goal Progress Log` lets a new executor answer all of these without extra guesswork:

- What is the goal?
- Is it on track, blocked, or complete?
- What has already been done?
- What has already been tried?
- What evidence is already real?
- What is still blocking?
- What should happen next?

If the available package cannot support those answers, the tracker should escalate instead of generating a hollow log.

An initial checkpoint with empty completed work, empty attempted paths, and empty verified evidence is still valid as long as the package clearly states the current focus, blocker state, and resume guidance.

## Example Skeleton

```markdown
# Goal Progress Log

## Goal Summary
- Goal: Improve the checkout payment-failure flow so users understand why payment failed and what they can do next.
- Status: blocked
- Last checkpoint: 2026-05-20T15:00:00Z

## Current Execution State
- Completed since last checkpoint: Mapped the current checkout failure states and updated the copy proposal for card-declined cases.
- Current focus: Waiting on product approval for the supported retry and alternate-payment actions.

## Attempted Paths
- Path: Drafted failure copy directly from engineering assumptions.
  Result: failed
  Evidence: Product review rejected the draft because the supported next actions differ by market.
- Path: Derived retry guidance from the current payment-provider error map.
  Result: partial
  Evidence: The map covers retryable errors but does not define alternate-payment guidance.

## Verified Evidence
- Criterion: Payment failure messages explain the failure state and next action.
  Proof: Updated copy proposal covers card-declined and insufficient-funds states.
- Criterion: Retry or alternate-payment guidance is visible in the failure path.
  Proof: Retry guidance is drafted; alternate-payment guidance is still unverified.

## Active Blockers
- Product has not approved which alternate-payment actions are valid per market.

## Resume From Here
- Next action: Resolve the market-specific alternate-payment policy with product, then update the failure-path copy and validation review.
- Avoid repeating: Do not keep revising copy from engineering assumptions alone without the policy decision.

## Checkpoint History
### 2026-05-20T15:00:00Z - blocked
- Summary: Copy draft is partially complete, but alternate-payment guidance remains blocked on product approval.
```
