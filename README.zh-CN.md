# Whetstone · 磨刀石

[English](README.md) | [简体中文](README.zh-CN.md)

> 面向被工具、上下文、依赖、限流或权限约束阻断的编码 Agent 的证据驱动恢复技能。

当 Agent 被卡住，失败通常不在用户的目标本身：工具可能 no-op，上下文或 turn 预算可能耗尽，依赖可能缺失，provider 也可能返回 rate limit。最糟糕的默认反应是放弃、重复相同调用，或做超出权限范围的大规模环境改动。

我们做 Whetstone，是为了把恢复这一步明确下来：**保留用户目标，建立证据，尝试风险最低的补救，证明被阻断的操作已恢复，然后回到原工作。** 恢复本身从来不是交付物。

名字来自中国古语 **“工欲善其事，必先利其器”** —— 要把事做好，先把工具磨利。这也是 Whetstone 的工作哲学：在对被阻断的目标加码之前，先安全地恢复工具路径，并证明它真的可用。

## Whetstone 是什么，不是什么

Whetstone 是一个面向具体、已观察到的执行阻断的 policy/workflow skill；它不是自治可靠性平台、通用 debugger，也不是任务完成系统。

| 你需要做什么 | 应使用 |
|---|---|
| 从有证据的工具/no-op、context/turn、rate limit、依赖、provider/authentication、权限或已文档化 runtime 约束中恢复 | **Whetstone** |
| 执行路径可用，但任务持续停滞或反复失败时继续推进交付 | **PUA** |
| 诊断或配置 Hermes 本身 | **Hermes troubleshooting** |

不要仅因为任务困难、模糊、耗时或不确定而使用 Whetstone。

## 恢复契约

Whetstone 将阻断变成一个有边界的决策循环：

1. **保留目标。** 记录原始目标、约束、验收标准，以及精确失败结果或观察到的状态。
2. **分类阻断。** 只能选择一种路径：本地/可逆恢复、需要用户动作，或硬停止。
3. **仅恢复一次。** 使用风险最低、已有文档支撑的补救。瞬态失败只允许一次有边界的重试；相同失败不能重复。
4. **证明恢复。** 重新运行原先被阻断的操作，或明确等价的聚焦检查。改了配置、写了计划或得到 HTTP 200 都不足以证明成功。
5. **继续或交接。** 立即回到原目标；若仍阻断，则给出目标、症状、证据、已尝试补救及最小且精确的用户动作。

完整契约在 [SKILL.md](SKILL.md)。

## 失败模式：证据 → 允许的动作 → 证明

| 模式 | 先取得的证据 | 风险最低的路径 | 何时算恢复已证明 |
|---|---|---|---|
| 工具失败或 no-op | 失败结果与相关前置状态 | 检查已文档化的前置条件；只做一个本地补救 | 同一工具操作成功 |
| Context 或 turn 耗尽 | 目标、约束、已完成证据、当前阻断、下一个已验证动作 | 创建精简交接，只带着这些事实继续 | 下一步在保留状态下运行；需求没有被暗中丢弃 |
| Rate limit | Provider 响应；如有则记录 `Retry-After` | 在任务预算内遵守已文档化的等待/重试路径 | 同一操作成功，或已交接经验证的下一次重试时间 |
| 依赖缺失 | 精确 import/build 失败与项目 tooling/lockfile 状态 | 在隔离的本地环境中复用项目声明的 tooling | import、build 或聚焦测试通过 |
| 权限或 secret 约束 | 同意/凭据边界与脱敏失败状态 | 停止并请求最小用户动作 | 仅在可见授权或凭据可用后重试 |

## 权限边界是刻意收紧的

Whetstone 从只读诊断和本地、可逆补救开始。它**不会**自动：

- 修改 approval 或 YOLO mode、权限、凭据、账号选择、quota 或 billing；
- 修改 provider/model/profile 设置、browser trust、全局 runtime/configuration、OS/network policy 或 shell 配置；
- 重启服务、重置/删除状态、花钱，或修改生产数据、部署、迁移、Git 历史或任务验收标准。

权限或 secret 边界属于“需要用户动作”的阻断，而不是可以绕过的问题。完整列表见 [Authority Boundaries](SKILL.md#authority-boundaries)。

## 安装

Whetstone 是一个 `SKILL.md` 指令制品；请在支持 skills 的 Agent runtime 中加载。

### Hermes Agent

```bash
hermes skills install \
  https://raw.githubusercontent.com/skbylife/whetstone/main/SKILL.md \
  --category autonomous-ai-agents \
  --yes
```

此安装路径已经在隔离 Hermes home 中验证。安装后，请开启新会话，或按你的 Hermes workflow 显式加载该 skill。

### 其他支持 skill 的 Agent

请通过平台已文档化的 skill-installation workflow 使用 [SKILL.md](SKILL.md)。本仓库不提供 SDK、MCP server、package、CLI、HTTP API，也不承诺通用兼容性。

## 如何使用

给 Whetstone 一个真实阻断，而不是模糊地要求它“再努力一点”。一次合格调用应包含原任务与观察到的失败：

```text
原始目标是 <用户目标>。
被阻断的操作返回 <精确错误或观察到的 no-op>。
使用 Whetstone：保留目标和约束；根据实时证据诊断；只尝试一次
被授权的最低风险恢复；重新运行被阻断操作；然后继续原任务，
或给我最小且精确的交接。
```

Whetstone 必须遵守以下不可协商原则：

- 没有工具输出、日志或当前文档，就不能臆造 runtime limit；
- 不盲目重试、不做 restart loop、不轮换账号/keys、不削弱安全性；
- 不把恢复变成嵌套项目，也不把诊断报告当作成功；
- 没有成功重跑被阻断路径，就不能声称已恢复。

## 当前范围与限制

- Whetstone 不保证恢复、任务完成或任何安全结果。
- 它不诊断所有 root cause，也不会无限重试。
- 它的有效性依赖真实的工具输出、当前文档/实时状态、host agent 可用的工具，以及已授予的权限。
- 它没有发布兼容性矩阵、benchmark、telemetry service、托管支持渠道或 release history。请不要从本仓库推断对某个 host、model、provider、操作系统或 marketplace 的支持。

## 贡献与反馈

Whetstone 采用 MIT 许可证，欢迎聚焦的 issue 或 pull request。若要改变行为，请附上相关 blocker 场景，并证明该变更仍保留：

1. 目标保留；
2. 有边界的权限；
3. 基于重跑的恢复证明；以及
4. 清晰的继续或交接结果。

提交 issue 时，请提供 blocker 类别、脱敏证据、host agent/runtime、已尝试的恢复动作，以及原操作是否已被重跑。

## 项目文件

- [SKILL.md](SKILL.md) — canonical workflow、constraint patterns、evidence standard 与 completion checklist
- [LICENSE](LICENSE) — MIT

## 许可证

[MIT](LICENSE)
