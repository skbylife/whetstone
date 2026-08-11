# Whetstone · 磨刀石

[English](README.md) | [简体中文](README.zh-CN.md)

> Evidence-led recovery for coding-agent runs blocked by tool, context, dependency, rate-limit, or permission constraints.

When an agent is blocked, the user’s objective can remain sound while the execution path is constrained: a tool may no-op, a context or turn budget may be exhausted, a dependency may be absent, or a provider may return a rate limit.

We made Whetstone to make recovery explicit: **preserve the user’s objective, establish evidence, attempt the smallest safe remedy, prove the blocked operation works, then return to the work.**

The name comes from the Chinese saying **“工欲善其事，必先利其器”** — *to do good work, one must first sharpen one’s tools.* It is Whetstone’s operating philosophy: before adding effort to a blocked objective, restore the tool path safely and prove it works.

## Where Whetstone fits

Whetstone is a policy/workflow skill for a concrete, observed execution blocker. It keeps recovery bounded and moves the agent back to the original work.

| Situation | Route |
|---|---|
| An evidenced tool/no-op, context/turn, rate-limit, dependency, provider/authentication, permission, or documented runtime constraint blocks progress | **Whetstone** |
| The execution path is usable and delivery benefits from continued task-level persistence | **PUA** |
| The work is diagnosing or configuring Hermes itself | **Hermes troubleshooting** |

Whetstone starts from a specific observed blocker and routes broader task persistence and platform diagnosis to their dedicated workflows.

## The recovery contract

Whetstone turns a blocker into a bounded decision loop:

1. **Preserve the objective.** Record the original goal, constraints, acceptance criteria, and the exact failed result or observed state.
2. **Classify the blocker.** Select one path: local/reversible recovery, user action required, or hard stop.
3. **Recover once.** Apply the lowest-risk documented remedy. A transient failure receives one bounded retry; a repeated identical failure receives a new hypothesis.
4. **Prove recovery.** Rerun the original blocked operation, or an explicitly equivalent focused check. Recovery proof comes from the operation itself.
5. **Resume or hand off.** Return immediately to the original objective. A remaining blocker receives a compact handoff with the objective, symptom, evidence, attempted remedies, and smallest exact user action.

The complete contract lives in [SKILL.md](SKILL.md).

## Failure patterns: evidence → allowed action → proof

| Pattern | Start with evidence | Lowest-risk path | Recovery is proven when… |
|---|---|---|---|
| Tool failure or no-op | Failed result plus relevant prerequisite state | Inspect the documented prerequisite; make one local remedy | The same tool operation succeeds |
| Context or turn exhaustion | Goal, constraints, completed evidence, active blocker, next verified action | Create a compact handoff and continue with those facts preserved | The next action runs with the preserved state and requirements intact |
| Rate limit | Provider response and `Retry-After` when present | Honor the documented wait/retry path within the task budget | The same operation succeeds, or a verified next retry time is handed off |
| Missing dependency | Exact import/build failure plus project tooling/lockfile state | Use declared project tooling in an isolated local environment | An import, build, or focused test passes |
| Permission or secret constraint | The consent/credential boundary and redacted failure state | Request the minimum user action | Retry follows visible authorization or available credentials |

## Authority model

Whetstone begins with read-only diagnostics and local, reversible remedies. The following actions require explicit user authorization:

- approval or YOLO modes, permissions, credentials, account selection, quotas, and billing;
- provider/model/profile settings, browser trust, global runtime/configuration, OS/network policy, and shell setup;
- service restarts, state reset/deletion, spending, production data changes, deployments, migrations, Git history changes, and task acceptance-criteria changes.

A permission or secret boundary produces a user-action-required handoff. See [Authority Boundaries](SKILL.md#authority-boundaries) for the canonical list.

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

### skills.sh-compatible agents

```bash
npx skills add skbylife/whetstone
```

The skills CLI resolves skills from GitHub repositories and tracks aggregate installations in the skills.sh directory. Use this path with a supported agent runtime.

### Other skill-capable agents

Use the platform’s documented skill-installation workflow with [SKILL.md](SKILL.md). The repository supplies the instruction artifact; host runtimes supply their own integration surfaces.

## Use it

A precise invocation includes the original task and the observed failure:

```text
The objective is <original user goal>.
The blocked operation returned <exact error or observed no-op>.
Use Whetstone: preserve the goal and constraints, diagnose from live evidence,
try the lowest-risk authorized recovery once, rerun the blocked operation, then
continue the original task or give me the smallest exact handoff.
```

Operating requirements:

- Ground runtime limits in tool output, logs, or current documentation.
- Use one evidence-led recovery attempt per hypothesis.
- Keep account, key, and security controls within their existing authority boundary.
- Report recovery after a successful rerun of the blocked path.

## Scope

Whetstone focuses on bounded recovery for evidenced execution blockers. Recovery outcomes depend on truthful tool output, current documentation/live state, the host agent’s available tools, and the authority already granted.

The project currently publishes a canonical skill, a verified Hermes installation path, and source documentation. Compatibility records, recovery transcripts, benchmarks, telemetry, release history, and managed support will appear as maintained evidence becomes available.

## Contributing and feedback

Whetstone is MIT licensed and welcomes focused issues or pull requests. A behavior change should include the relevant blocker scenario and show that it preserves:

1. objective preservation;
2. bounded authority;
3. rerun-based proof of recovery; and
4. a clear resume-or-handoff outcome.

Issue reports work best with the blocker category, redacted evidence, host agent/runtime, attempted recovery, and result of the original-operation rerun.

## Project files

- [SKILL.md](SKILL.md) — canonical workflow, constraint patterns, evidence standard, and completion checklist
- [LICENSE](LICENSE) — MIT

## License

[MIT](LICENSE)
