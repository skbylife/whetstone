# Whetstone · 磨刀石

> Evidence-led recovery for coding-agent runs blocked by tool, context, dependency, rate-limit, or permission constraints.

When an agent is blocked, the failure is often not in the user’s objective. A tool may no-op, a context or turn budget may be exhausted, a dependency may be absent, or a provider may return a rate limit. The unhelpful defaults are to give up, repeat the same call, or make a broad environment change that exceeds authority.

We made Whetstone to make the recovery step explicit: **preserve the user’s objective, establish evidence, attempt the smallest safe remedy, prove the blocked operation works, then return to the work.** Recovery is never the deliverable by itself.

## What Whetstone is—and is not

Whetstone is a policy/workflow skill for a concrete, observed execution blocker. It is not an autonomous reliability platform, a generic debugger, or a task-completion system.

| If you need to… | Use |
|---|---|
| Recover from an evidenced tool/no-op, context/turn, rate-limit, dependency, provider/authentication, permission, or documented runtime constraint | **Whetstone** |
| Keep delivering after the execution path is usable but work repeatedly stalls or fails | **PUA** |
| Diagnose or configure Hermes itself | **Hermes troubleshooting** |

Do **not** use Whetstone merely because work is difficult, ambiguous, long, or uncertain.

## The recovery contract

Whetstone turns a blocker into a bounded decision loop:

1. **Preserve the objective.** Record the original goal, constraints, acceptance criteria, and the exact failed result or observed state.
2. **Classify the blocker.** Choose one path: local/reversible recovery, user action required, or hard stop.
3. **Recover once.** Apply the lowest-risk documented remedy. For a transient failure, make one bounded retry; do not repeat an identical failure.
4. **Prove recovery.** Rerun the original blocked operation, or an explicitly equivalent focused check. A configuration edit, plan, or HTTP 200 alone is not proof.
5. **Resume or hand off.** Return immediately to the original objective. If still blocked, provide the objective, symptom, evidence, attempted remedies, and the smallest exact user action required.

The complete contract lives in [SKILL.md](SKILL.md).

## Failure patterns: evidence → allowed action → proof

| Pattern | Start with evidence | Lowest-risk path | Recovery is proven when… |
|---|---|---|---|
| Tool failure or no-op | Failed result plus relevant prerequisite state | Inspect the documented prerequisite; make one local remedy | The same tool operation succeeds |
| Context or turn exhaustion | Goal, constraints, completed evidence, active blocker, next verified action | Create a compact handoff and continue only with those facts preserved | The next action runs with the preserved state; requirements were not silently dropped |
| Rate limit | Provider response and `Retry-After` when present | Honor the documented wait/retry path within the task budget | The same operation succeeds, or the verified next retry time is handed off |
| Missing dependency | Exact import/build failure plus project tooling/lockfile state | Use declared project tooling in an isolated local environment | An import, build, or focused test passes |
| Permission or secret constraint | The consent/credential boundary and redacted failure state | Stop and request the minimum user action | Retry happens only after visible authorization or available credentials |

## Authority is deliberately bounded

Whetstone starts with read-only diagnostics and local, reversible remedies. It does **not** automatically:

- change approval or YOLO modes, permissions, credentials, account selection, quotas, or billing;
- change provider/model/profile settings, browser trust, global runtime/configuration, OS/network policy, or shell setup;
- restart services, reset/delete state, spend money, or modify production data, deployments, migrations, git history, or task acceptance criteria.

A permission or secret boundary is a user-action-required blocker—not something to work around. See [Authority Boundaries](SKILL.md#authority-boundaries) for the canonical list.

## Install

Whetstone is a `SKILL.md` instruction artifact. Load it through an agent runtime that supports skills.

### Hermes Agent

```bash
hermes skills install \
  https://raw.githubusercontent.com/skbylife/whetstone/main/SKILL.md \
  --category autonomous-ai-agents \
  --yes
```

This installation path is verified against an isolated Hermes home. After installing, start a new session or load the skill explicitly according to your Hermes workflow.

### Other skill-capable agents

Use the platform’s documented skill-installation workflow with [SKILL.md](SKILL.md). This repository does not provide an SDK, MCP server, package, CLI, HTTP API, or a universal compatibility promise.

## Use it

Give Whetstone a real blocker, not a vague request to “try harder.” A good invocation includes the original task and the observed failure:

```text
The objective is <original user goal>.
The blocked operation returned <exact error or observed no-op>.
Use Whetstone: preserve the goal and constraints, diagnose from live evidence,
try the lowest-risk authorized recovery once, rerun the blocked operation, then
continue the original task or give me the smallest exact handoff.
```

Whetstone should treat these as non-negotiable:

- do not invent a runtime limit without tool output, logs, or current documentation;
- do not blindly retry, restart-loop, rotate accounts/keys, or weaken security;
- do not turn recovery into a nested project or report the diagnosis as success;
- do not claim recovery until the blocked path is rerun successfully.

## Current scope and limitations

- Whetstone does not guarantee recovery, task completion, or any safety outcome.
- It does not diagnose every root cause and does not retry indefinitely.
- Its effectiveness depends on truthful tool output, current documentation/live state, the host agent’s available tools, and the authority already granted.
- It has no published compatibility matrix, benchmark, telemetry service, managed support channel, or release history. Do not infer support for a host, model, provider, operating system, or marketplace from this repository.

## Contributing and feedback

Whetstone is MIT licensed and welcomes focused issues or pull requests. For a behavior change, include the relevant blocker scenario and show that the change preserves:

1. objective preservation;
2. bounded authority;
3. rerun-based proof of recovery; and
4. a clear resume-or-handoff outcome.

If you report an issue, include the blocker category, redacted evidence, the host agent/runtime, what recovery was attempted, and whether the original operation was rerun.

## Project files

- [SKILL.md](SKILL.md) — canonical workflow, constraint patterns, evidence standard, and completion checklist
- [LICENSE](LICENSE) — MIT

## License

[MIT](LICENSE)
