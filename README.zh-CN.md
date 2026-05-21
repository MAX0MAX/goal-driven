# Goal-Driven 中文文档

[English README](README.md)

Goal-Driven 是一个面向编码代理的 contract-first skill package。它的公开写作入口是 `goal-contract-writer`，用于把模糊请求整理成标准的 Goal Contract。

这个仓库是该 package 的单一事实来源。`skills/goal-contract-writer/` 保存公开的目标契约写作 skill；`skills/goal-contract-verifier/` 保存配套的执行状态验证 skill；`skills/goal-progress-tracker/` 保存配套的运行进度记录 skill。`.claude-plugin/` 和 `.codex-plugin/` 是分别面向 Claude Code 和 Codex 的轻量包装。

`goal-contract-writer` 是普通用户唯一应该直接调用的入口。它负责把请求整理成 canonical Goal Contract。`goal-contract-verifier` 用于运行时或操作员在需要检查当前执行状态时进行 fresh-subagent verification。`goal-progress-tracker` 用于保存可恢复、可交接的执行状态记录。

## 核心理念

`goal-contract-writer` 是澄清与执行之间的窄层。

这个 package 不实现 Goal-Driven 的主代理/子代理运行循环。它提供的是上游契约和执行记录 artifacts，供 Goal-Driven loop、workflow engine、host goal system 或人工操作员消费。

它的价值不是问更多问题，而是产出一个稳定的 contract，让下游系统和人可以理解、验证、执行，或在不重新发现原始意图的情况下继续拆解工作。

verifier 和 tracker 层刻意与 canonical contract 分离。`Verifier Verdict` 和 `Goal Progress Log` 这样的运行时 artifact 属于 adapter 和 host workflow，不属于 Goal Contract schema 本身。

项目遵循四条原则：

- Contract over conversation: 对话只是为了填满 contract。
- Verifiability over intuition: 完成状态必须由证据判断，而不是由代理信心判断。
- Execution-neutral by default: contract 定义目标、证据和边界，下游系统决定如何执行。
- Upstream first: `goal-contract-writer` 定义工作；下游工具负责计划、实现、测试、评审或持久化 spec。

它不是 Superpowers、OpenSpec、Spec Kit、Codex goal state 或任何执行工作流的替代品。它是这些系统可以消费的 goal artifact。

## 输出内容

每次成功的 `goal-contract-writer` 运行都会产出一个 Goal Contract，包含且只包含这些 canonical 字段：

- `goal`
- `scope`
- `success_criteria`
- `evidence`
- `guardrails`

`success_criteria` 是判断 `goal` 是否完成的唯一 canonical basis。`evidence` 用来证明这些 criteria，不应该偷偷添加额外验收规则。

运行时可以在 Goal Contract 外层包装 `Verifier Verdict` 和 `Goal Progress Log` 等 companion artifacts，但这些 artifact 不进入 canonical contract。

## 示例

请求：

```text
Improve checkout so users understand failed payments.
```

Goal Contract：

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

## 什么时候使用

在这些情况下使用 `goal-contract-writer`：

- 请求有明确产出，但一开始还比较模糊。
- 下一个代理、系统或人需要一个稳定的 goal artifact。
- 成功必须由可观察证据判断，而不是靠直觉判断。

不要把它当作计划、执行、测试或评审的替代品。

## 快速开始

Codex Marketplace plugin 安装：

```bash
npx codex-marketplace add MAX0MAX/goal-driven --plugin --project -y
```

默认 Codex skill 安装：

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker -a codex
```

默认 Claude Code 安装：

```bash
npx skills add MAX0MAX/goal-driven --skill goal-contract-writer --skill goal-contract-verifier --skill goal-progress-tracker --agent claude-code
```

使用 `-g` 可以进行 user-wide 安装。

安装后，可以用自然语言调用：

```text
Use goal-contract-writer to turn this request into a Goal Contract: <your request>
```

Codex 中也可以显式调用：

```text
$goal-contract-writer Turn this request into a Goal Contract: <your request>
```

Claude Code 中也可以显式调用：

```text
/goal-driven:goal-contract-writer Turn this request into a Goal Contract: <your request>
```

安装包同时包含 `goal-contract-verifier` 和 `goal-progress-tracker`。它们可能会出现在 host 的 skill 列表里，但普通用户通常只需要调用 `goal-contract-writer`。

## 运行时验证

当 runtime 或 operator 需要用 `goal-contract-verifier` 验证当前执行状态时，按这个顺序处理：

1. 收集已有 Goal Contract，以及当前 progress、evidence 和 blockers。
2. 在 fresh subagent 中派发 `goal-contract-verifier`。
3. 读取且只读取一个结构化的 `Verifier Verdict`。
4. 把 verdict 和 status 当作当前执行状态的验证结果。

verifier 不是自动的预执行 gate。它只在 runtime 或 operator 已经有 progress、blockers 或 completion claim 等状态需要检查时使用。

如果 verifier 派发失败、返回非标准 artifact、返回 `revise contract` 或 `escalate`，或者返回 `status: blocked`，runtime 或 operator 都不能声称验证已通过，也不能声称 goal 已完成。

完整安装、prompt 和 marketplace 说明见 [skills/goal-contract-writer/INSTALL.md](skills/goal-contract-writer/INSTALL.md)。agent-facing 行为见 [skills/goal-contract-writer/SKILL.md](skills/goal-contract-writer/SKILL.md)。

完整示例见 [skills/goal-contract-writer/examples/](skills/goal-contract-writer/examples/)。

## Marketplace 准备状态

- Codex Marketplace 会读取仓库根目录的 `.codex-plugin/plugin.json`，并把这个 package 作为 plugin 安装。
- Open Agent Skill 可以读取根目录的 `skill.json` 作为 discovery metadata。
- Open Agent Skill category 是 `Developer Tools`；推荐 tags 是 `goal-contract`、`agent-skills`、`codex`、`claude-code`、`verification` 和 `workflow`。

## 仓库结构

- `skills/goal-contract-writer/`
  公开 authoring skill，包含 references、examples 和 install notes。
- `skills/goal-contract-verifier/`
  配套 verifier skill，用于 fresh-subagent execution-state verification。
- `skills/goal-progress-tracker/`
  配套 tracker skill，用于 runtime handoff 和 recovery logs。
- `.claude-plugin/`
  Claude Code wrapper。
- `.codex-plugin/`
  Codex wrapper。
- `.agents/plugins/marketplace.json`
  repo-local Codex marketplace entry，用于公司内部 plugin catalog 或本地安装测试。
- `skill.json`
  Open Agent Skill public discovery metadata。

## Adapter 使用方式

`goal-contract-writer` 位于执行之前。

- 后续工作是计划、实现、测试或评审时，交给 workflow system。
- 后续工作需要持久化 spec 或 task 时，交给 specification system。
- 执行属于外部流程时，交给人工操作员或 host-native path。

adapter 可以在 canonical contract 外添加 routing、owner、priority、suggested next action，或 `Verifier Verdict`、`Goal Progress Log` 等 runtime artifacts。不要把这些 metadata 变成 Goal Contract 的 required fields。

adapter 不能添加、替换或覆盖 canonical `success_criteria`。任何 host-specific checks 都应留在 contract 外部，不能重新定义 goal completion。

## Source Of Truth

- Repository: https://github.com/MAX0MAX/goal-driven
- Canonical public skill directory: `skills/goal-contract-writer/`
- Paired verifier skill: `skills/goal-contract-verifier/`
- Paired tracker skill: `skills/goal-progress-tracker/`
- Agent entrypoint: `skills/goal-contract-writer/SKILL.md`
- Install guide: `skills/goal-contract-writer/INSTALL.md`
- Claude Code wrapper: `.claude-plugin/`
- Codex wrapper: `.codex-plugin/`
- Codex marketplace entry: `.agents/plugins/marketplace.json`
