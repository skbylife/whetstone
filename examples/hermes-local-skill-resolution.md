# Verified recovery trace: local-skill inspection

> **Scope:** a real Hermes execution trace recorded on 2026-08-11. It demonstrates a deterministic tool-resolution blocker and a bounded recovery. It does **not** claim that Whetstone was automatically selected.

## Objective

Inspect the locally enabled `whetstone` skill before continuing the distribution work.

## Blocker: exact observed result

```text
$ hermes skills inspect whetstone
Resolving 'whetstone'...
Error: No skill named 'whetstone' found in any source.

command_exit=0
```

The diagnostic text, rather than the zero process status, establishes the blocker.

## Evidence and classification

- `hermes skills inspect` resolves a short name through its configured Skills Hub sources; it does not read the local skill directory in this path. See [`do_inspect`](https://github.com/NousResearch/hermes-agent/blob/main/hermes_cli/skills_hub.py#L796-L812).
- The active Hermes agent's `skill_view("whetstone")` lookup returned `success: true`, the local path `autonomous-ai-agents/whetstone/SKILL.md`, and `readiness_status: available`.

This is a **local, deterministic tool-resolution boundary**. It needs no credential, permission, configuration, provider, or runtime change.

## One lowest-risk recovery

Use the agent's native `skill_view("whetstone")` path for an installed local skill. Do not repeat the Hub-only lookup or change sources to force a result.

## Proof and return to objective

```text
skill_view("whetstone")
success: true
name: whetstone
path: autonomous-ai-agents/whetstone/SKILL.md
readiness_status: available
```

The local skill content and readiness were then available for the original distribution work.

## What this trace proves

- A tool error can arise from an execution-path mismatch even when the target artifact is locally available.
- Reading the diagnostic and checking the documented resolution path identifies a minimal, reversible recovery.
- The recovery is complete only when the required local content is actually available to the original work.

---

# 已验证恢复记录：本地 skill 检查

> **范围：** 这是一条于 2026-08-11 记录的真实 Hermes 执行记录。它展示了确定性的工具解析阻断与有边界恢复；它不表示 Whetstone 被自动选中。

## 原目标

在继续分发工作前，检查已在本地启用的 `whetstone` skill。

## 阻断：精确观察结果

```text
$ hermes skills inspect whetstone
Resolving 'whetstone'...
Error: No skill named 'whetstone' found in any source.

command_exit=0
```

阻断由诊断文字确认，而不是由零进程退出状态确认。

## 证据与分类

- `hermes skills inspect` 通过其配置的 Skills Hub source 解析短名称；该路径不读取本地 skill 目录。参见 [`do_inspect`](https://github.com/NousResearch/hermes-agent/blob/main/hermes_cli/skills_hub.py#L796-L812)。
- 活跃 Hermes agent 的 `skill_view("whetstone")` 返回 `success: true`、本地路径 `autonomous-ai-agents/whetstone/SKILL.md` 与 `readiness_status: available`。

这是一个**本地、确定性的工具解析边界**，不需要改动凭据、权限、配置、provider 或 runtime。

## 一次风险最低的恢复

对已安装的本地 skill，使用 agent 原生的 `skill_view("whetstone")` 路径。无需重复 Hub-only 查询，也不需要通过修改 source 强行得到结果。

## 证明并回到原目标

```text
skill_view("whetstone")
success: true
name: whetstone
path: autonomous-ai-agents/whetstone/SKILL.md
readiness_status: available
```

本地 skill 内容与就绪状态随后可用于原分发工作。

## 这条记录证明了什么

- 即使目标制品已在本地可用，执行路径不匹配仍可能产生工具错误。
- 阅读诊断并检查已文档化的解析路径，可以找到最小、可逆的恢复动作。
- 只有原工作实际拿到所需的本地内容后，恢复才算完成。
