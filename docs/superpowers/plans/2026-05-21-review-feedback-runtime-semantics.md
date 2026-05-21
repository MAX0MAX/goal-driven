# Minimal Runtime Semantics Fix Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Fix the remaining review issues with the smallest useful docs change: blocked verifier status, tracker overreach, and missing verifier outcome examples.

**Architecture:** Keep `Goal Contract` unchanged and upstream-only. Make the smallest edits on the surfaces that currently teach the wrong runtime behavior. Do not change manifests, package version, schema fields, or add new runtime machinery.

**Tech Stack:** Markdown skill docs, PowerShell verification commands, repository-wide text search with `rg`.

---

## File Structure

- Modify `skills/goal-contract-verifier/SKILL.md`
  - Add one explicit sentence that `pass` + `blocked` is valid.
- Modify `skills/goal-contract-writer/INSTALL.md`
  - Fix the conflicting fail-closed wording.
  - Make tracker maintenance conditional on maintained handoff state.
- Modify `skills/goal-contract-writer/references/downstream-adapters.md`
  - Fix adapter fail-closed wording.
  - Make tracker maintenance conditional.
- Modify `skills/goal-progress-tracker/SKILL.md`
  - Remove the implication that every execution host must maintain a tracker.
- Modify `README.md`
  - Fix the same public `status: blocked` conflict if it is still present.
- Modify `skills/goal-contract-verifier/examples/README.md`
  - Add compact outcome examples instead of creating new example files.

No new example files. No manifest edits unless final search shows a direct contradiction.

---

### Task 1: Fix Blocked Verifier Semantics

**Files:**
- Modify: `skills/goal-contract-verifier/SKILL.md`
- Modify: `skills/goal-contract-writer/INSTALL.md`
- Modify: `skills/goal-contract-writer/references/downstream-adapters.md`
- Modify: `README.md`

- [ ] **Step 1: Add the missing verifier SKILL sentence**

In `skills/goal-contract-verifier/SKILL.md`, after the legitimate-combinations list, add exactly:

```markdown
`pass` + `blocked` is a valid verifier result. It means the Goal Contract still frames the runtime state correctly, but execution remains blocked; consumers must preserve the blocked status and must not summarize the goal as complete or progress as unblocked.
```

- [ ] **Step 2: Fix INSTALL fail-closed wording while preserving fresh-subagent rule**

In `skills/goal-contract-writer/INSTALL.md`, preserve the opening fresh-subagent rule:

```markdown
The verifier must run in a fresh subagent. The main execution context must not self-verify in place.
```

Then replace the outcome-policy wording that says `status: blocked` means the runtime cannot claim verification passed with this simpler rule:

```markdown
If verifier dispatch fails or returns anything other than a structured `Verifier Verdict`, fail closed for that verification request and do not claim verification passed or that the goal is complete. If the verifier returns `revise contract` or `escalate`, preserve the verdict and route the state accordingly. If the verifier returns `status: blocked`, preserve the blocked execution state; do not claim the goal is complete or progress is unblocked. A `pass` verdict may still be paired with `status: blocked`.
```

- [ ] **Step 3: Fix adapter fail-closed wording**

In `skills/goal-contract-writer/references/downstream-adapters.md`, keep fail-closed limited to dispatch/non-standard output:

```markdown
- Adapters must fail closed for a verification request when verifier dispatch fails or verifier output is non-standard.
- Adapters must not downgrade `revise contract`, `escalate`, or `status: blocked` verifier outputs into ordinary notes.
- `status: blocked` preserves blocked execution state. It does not turn a `pass` verdict into verifier failure, but it prevents claims that the goal is complete or progress is unblocked.
```

- [ ] **Step 4: Fix README only where it repeats the conflict**

If `README.md` still says `status: blocked` means the runtime cannot claim verification passed, replace that sentence with the same short semantics:

```markdown
If verifier dispatch fails or returns a non-standard artifact, fail closed for that verification request. Preserve `revise contract`, `escalate`, and `status: blocked` as verifier outputs. A `pass` verdict may still be paired with `status: blocked`; that means the contract still frames the state, but execution is blocked and the goal is not complete.
```

- [ ] **Step 5: Verify blocked wording**

Run:

```powershell
rg -n "verification passed|fail closed|status: blocked|pass.*blocked|blocked.*pass" README.md skills
```

Expected:

- Fail closed only refers to dispatch failure or non-standard verifier output.
- `revise contract` and `escalate` are preserved and routed.
- `pass` + `blocked` is explicitly valid but not complete.

---

### Task 2: Make Tracker Maintenance Conditional

**Files:**
- Modify: `skills/goal-progress-tracker/SKILL.md`
- Modify: `skills/goal-contract-writer/INSTALL.md`
- Modify: `skills/goal-contract-writer/references/downstream-adapters.md`

- [ ] **Step 1: Soften tracker overview**

In `skills/goal-progress-tracker/SKILL.md`, replace the sentence that starts `Once a Goal Contract enters execution` with:

```markdown
When a runtime or operator maintains handoff state for a Goal Contract, this tracker is the protocol for keeping that state current from the initial checkpoint onward rather than reconstructing it only after the work stops.
```

- [ ] **Step 2: Soften post-hoc reconstruction wording**

In `skills/goal-progress-tracker/SKILL.md`, replace the protocol-failure sentence with:

```markdown
Post-hoc reconstruction after execution has already finished is a recovery fallback when no maintained log exists. It is not compliant primary workflow for a runtime or operator that elected to maintain tracker handoff state.
```

- [ ] **Step 3: Make INSTALL tracker rule conditional**

In `skills/goal-contract-writer/INSTALL.md`, replace the tracker requirement with:

```markdown
When a runtime or operator maintains handoff state for an executing Goal Contract, use `goal-progress-tracker` to record current execution state.
```

- [ ] **Step 4: Make adapter tracker rule conditional**

In `skills/goal-contract-writer/references/downstream-adapters.md`, replace `Once the runtime begins execution...` tracker wording with:

```markdown
- When the runtime or operator maintains handoff state, use `goal-progress-tracker` against `Goal Contract + current_status + completed_steps + attempted_paths + verified_evidence + active_blockers + resume_hint` and keep updating it at meaningful checkpoints and handoffs.
```

- [ ] **Step 5: Verify tracker wording**

Run:

```powershell
rg -n "must maintain|protocol failure|Once a Goal Contract enters execution|Once the runtime begins execution|Post-hoc reconstruction" skills README.md
```

Expected:

- No wording makes tracker mandatory for every execution host.
- Any maintained-log requirement is scoped to runtimes/operators that maintain handoff state.

---

### Task 3: Add Minimal Verifier Outcome Coverage

**Files:**
- Modify: `skills/goal-contract-verifier/examples/README.md`

- [ ] **Step 1: Add a compact outcome section**

In `skills/goal-contract-verifier/examples/README.md`, keep the existing full example link. Add this section below it:

````markdown
## Minimal Outcome Coverage

All verifier examples use the same input shape:

```yaml
Verification Package:
  goal_contract:
    Goal Contract:
      goal: "<goal>"
      scope:
        in_scope: []
        out_of_scope: []
      success_criteria: []
      evidence: []
      guardrails: []
  current_progress: []
  current_evidence: []
  current_blockers: []
```

### `pass` + `blocked`

```yaml
Verifier Verdict:
  verdict: "pass"
  status: "blocked"
  reasons:
    - "The Goal Contract still frames the runtime state correctly."
    - "Execution is blocked, so the goal is not complete."
  criteria_gaps: []
  evidence_gaps:
    - criterion: "Blocked criterion"
      gap: "blocked"
      reason: "The runtime cannot complete this criterion until the blocker is removed."
```

### `revise contract` + `blocked`

```yaml
Verifier Verdict:
  verdict: "revise contract"
  status: "blocked"
  reasons:
    - "The Goal Contract lacks observable success criteria for the runtime state."
  criteria_gaps:
    - criterion: "Unclear criterion"
      gap: "subjective"
      reason: "The criterion must be rewritten before execution can be judged."
  evidence_gaps: []
```

### `escalate` + `blocked`

```yaml
Verifier Verdict:
  verdict: "escalate"
  status: "blocked"
  reasons:
    - "The runtime state depends on missing input or approval outside verifier authority."
  criteria_gaps: []
  evidence_gaps:
    - criterion: "External decision"
      gap: "missing approval"
      reason: "The verifier cannot resolve this approval boundary."
```
````

- [ ] **Step 2: Verify example coverage**

Run:

```powershell
rg -n "Verification Package:|goal_contract:|verdict:|status:" skills/goal-contract-verifier/examples
```

Expected:

- Existing full example still has `Verification Package.goal_contract`.
- README now covers `pass + blocked`, `revise contract + blocked`, and `escalate + blocked`.

---

### Task 4: Final Minimality Check

**Files:**
- Review: changed files only

- [ ] **Step 1: Confirm no unrelated surfaces changed**

Run:

```powershell
git diff --name-only
```

Expected changed files are limited to:

```text
README.md
skills/goal-contract-verifier/SKILL.md
skills/goal-contract-verifier/examples/README.md
skills/goal-contract-writer/INSTALL.md
skills/goal-contract-writer/references/downstream-adapters.md
skills/goal-progress-tracker/SKILL.md
docs/superpowers/plans/2026-05-21-review-feedback-runtime-semantics.md
```

Do not modify plugin manifests, package metadata, or version files unless a search finds a direct contradiction.

- [ ] **Step 2: Run final text checks**

Run:

```powershell
rg -n "isolated runtime validation|verification passed|fail closed|status: blocked|pass.*blocked|blocked.*pass|must maintain|protocol failure|Once a Goal Contract enters execution|Once the runtime begins execution" README.md skills
```

Expected:

- Old contradictions are gone.
- Remaining hits teach the simplified rules from Tasks 1 and 2.

- [ ] **Step 3: Run whitespace check**

Run:

```powershell
git diff --check
```

Expected: exit code `0`.

---

## Self-Review Checklist

- [ ] No schema fields changed.
- [ ] No new runtime protocol was introduced.
- [ ] No new example files were created.
- [ ] `status: blocked` no longer means verifier failure.
- [ ] Tracker is only mandatory for workflows that maintain handoff state.
