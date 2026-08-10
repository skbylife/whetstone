# Whetstone · 磨刀石

[English](README.md) | [简体中文](README.zh-CN.md)

> 面向被工具、上下文、依赖、限流或权限约束阻断的编码 Agent 的证据驱动恢复技能。

当 Agent 被卡住，用户目标本身依然可能成立，受约束的是执行路径：工具可能 no-op，上下文或 turn 预算可能耗尽，依赖可能缺失，provider 也可能返回 rate limit。

我们做 Whetstone，是为了把恢复这一步明确下来：**保留用户目标，建立证据，尝试风险最低的补救，证明被阻断的操作已恢复，然后回到原工作。**

名字来自中国古语 **“工欲善其事，必先利其器”** —— 要把事做好，先把工具磨利。这也是 Whetstone 的工作哲学：在为被阻断的目标增加投入前，先安全地恢复工具路径，并证明它真的可用。

## Whetstone 适合什么场景

Whetstone 是一个面向具体、已观察到的执行阻断的 policy/workflow skill。它让恢复保持边界清晰，并让 Agent 回到原始工作。

| 场景 | 路由 |
|---|---|
| 有证据的工具/no-op、context/turn、rate limit、依赖、provider/authentication、权限或已文档化 runtime 约束阻断进展 | **Whetstone** |
| 执行路径可用，交付受益于持续的任务级推进 | **PUA** |
| 工作内容是诊断或配置 Hermes 本身 | **Hermes troubleshooting** |

Whetstone 从一个具体的已观察阻断开始，并将更广泛的任务推进与平台诊断路由到各自专用工作流。

## 恢复契约

Whetstone 将阻断变成一个有边界的决策循环：

1. **保留目标。** 记录原始目标、约束、验收标准，以及精确失败结果或观察到的状态。
2. **分类阻断。** 选择一种路径：本地/可逆恢复、需要用户动作，或硬停止。
3. **仅恢复一次。** 使用风险最低、已有文档支撑的补救。瞬态失败获得一次有边界的重试；相同失败再次出现时建立新的假设。
4. **证明恢复。** 重新运行原先被阻断的操作，或明确等价的聚焦检查。恢复证明来自操作本身。
5. **继续或交接。** 立即回到原目标。仍然存在的阻断获得精简交接，包含目标、症状、证据、已尝试补救及最小且精确的用户动作。

完整契约在 [SKILL.md](SKILL.md)。

## 失败模式：证据 → 允许的动作 → 证明

| 模式 | 先取得的证据 | 风险最低的路径 | 何时算恢复已证明 |
|---|---|---|---|
| 工具失败或 no-op | 失败结果与相关前置状态 | 检查已文档化的前置条件；只做一个本地补救 | 同一工具操作成功 |
| Context 或 turn 耗尽 | 目标、约束、已完成证据、当前阻断、下一个已验证动作 | 创建精简交接，只带着这些事实继续 | 下一步在保留状态下运行，需求保持完整 |
| Rate limit | Provider 响应；如有则记录 `Retry-After` | 在任务预算内遵守已文档化的等待/重试路径 | 同一操作成功，或已交接经验证的下一次重试时间 |
| 依赖缺失 | 精确 import/build 失败与项目 tooling/lockfile 状态 | 在隔离的本地环境中复用项目声明的 tooling | import、build 或聚焦测试通过 |
| 权限或 secret 约束 | 同意/凭据边界与脱敏失败状态 | 请求最小用户动作 | 在可见授权或凭据可用后重试 |

## 权限模型

Whetstone 从只读诊断和本地、可逆补救开始。以下动作需要用户显式授权：

- approval 或 YOLO mode、权限、凭据、账号选择、quota 与 billing；
- provider/model/profile 设置、browser trust、全局 runtime/configuration、OS/network policy 与 shell 配置；
- 服务重启、状态重置/删除、花钱、生产数据变更、部署、迁移、Git 历史变更与任务验收标准变更。

权限或 secret 边界会形成“需要用户动作”的交接。完整列表见 [Authority Boundaries](SKILL.md#authority-boundaries)。

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

请通过平台已文档化的 skill-installation workflow 使用 [SKILL.md](SKILL.md)。本仓库提供指令制品；host runtime 提供各自的集成界面。

## 如何使用

一次精确调用应包含原任务与观察到的失败：

```text
原始目标是 <用户目标>。
被阻断的操作返回 <精确错误或观察到的 no-op>。
使用 Whetstone：保留目标和约束；根据实时证据诊断；只尝试一次
被授权的最低风险恢复；重新运行被阻断操作；然后继续原任务，
或给我最小且精确的交接。
```

工作要求：

- 用工具输出、日志或当前文档支撑 runtime limit。
- 每个假设只执行一次证据驱动的恢复尝试。
- 让账号、key 与安全控制保持在既有权限边界内。
- 在成功重跑被阻断路径后报告恢复。

## 范围

Whetstone 聚焦于有证据的执行阻断的有边界恢复。恢复结果依赖真实的工具输出、当前文档/实时状态、host agent 可用的工具，以及已授予的权限。

项目当前发布 canonical skill、经验证的 Hermes 安装路径与源文档。兼容性记录、恢复 transcript、benchmark、telemetry、release history 与托管支持，将随可维护证据逐步出现。

## 贡献与反馈

Whetstone 采用 MIT 许可证，欢迎聚焦的 issue 或 pull request。行为变更应包含相关 blocker 场景，并证明它仍保留：

1. 目标保留；
2. 有边界的权限；
3. 基于重跑的恢复证明；以及
4. 清晰的继续或交接结果。

Issue 报告最好包含 blocker 类别、脱敏证据、host agent/runtime、已尝试恢复动作，以及原操作重跑结果。

## 项目文件

- [SKILL.md](SKILL.md) — canonical workflow、constraint patterns、evidence standard 与 completion checklist
- [LICENSE](LICENSE) — MIT

## 许可证

[MIT](LICENSE)
