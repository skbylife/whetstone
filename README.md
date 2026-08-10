# Whetstone · 磨刀石

A safe execution-recovery skill for coding-agent runs blocked by evidenced tool failures, context/turn exhaustion, rate limits, missing dependencies, or permission constraints.

Whetstone preserves the original objective, applies one lowest-risk recovery, proves the blocked operation works, then returns to the task. It does **not** change approvals, credentials, provider settings, quotas, global configuration, production data, deployments, or acceptance criteria automatically.

## Scope

- Use **Whetstone** for a concrete execution blocker.
- Use **PUA** when the execution path is usable but delivery has stalled or repeatedly failed.
- Use **Hermes troubleshooting** to diagnose/configure Hermes itself.

The canonical workflow, boundaries, and evidence contract are in [SKILL.md](SKILL.md).

## License

[MIT](LICENSE)
