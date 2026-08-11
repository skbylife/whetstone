---
name: whetstone
description: "Evidenced execution blocker: recover safely, verify, resume."
version: 1.0.2
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [agent-reliability, execution-recovery, tool-failure, safe-retry, context, rate-limit, blockers]
    related_skills: [pua, hermes-agent]
---

# 磨刀石 · Whetstone

Keep the user's original objective primary. This skill removes a demonstrated execution blocker; it is not a generic troubleshooting project or a reason to stop delivery.

## Positioning

**Whetstone** is safe execution recovery: establish evidence, make one lowest-risk recovery, prove the blocked path works, and return to the original task.

| Need | Use |
|---|---|
| A concrete tool, context, rate-limit, dependency, or permission blocker | Whetstone |
| The blocked operation now runs, but task implementation or delivery still fails or needs persistence | `pua` |
| Diagnose or configure Hermes itself | `hermes-agent` troubleshooting |

For a suspected Hermes CLI, gateway, provider, tool-registration, or runtime-configuration fault, load `hermes-agent` troubleshooting first; use Whetstone only once that diagnosis identifies a bounded safe recovery.

Do not call Whetstone “self-healing,” “production-safe,” or a general agent-reliability framework. Its safety comes from explicit authority limits and proof of recovery—not an outcome guarantee.

## Trigger

If this skill is loaded after concrete evidence that an execution constraint prevents safe progress, use it to recover the blocked path: a tool error/no-op, context or turn budget exhaustion, missing capability/dependency, authentication/provider failure, rate limit, or documented runtime/concurrency limit.

Do not use merely because work is hard, long, or uncertain. After the blocked operation succeeds, use `pua` only if the remaining task is still stalled or has repeated failures.

## Recovery Gate

1. **Preserve objective.** State the original objective and the exact blocker with its tool result, error, or observed state. Done when the blocker is testable rather than inferred.
2. **Classify.** Choose one: local reversible recovery; user action required; hard stop. Read current native documentation and live state before acting. Done when the chosen path does not expand authority.
3. **Recover once.** Before retrying, determine whether the original operation could already have taken effect. For uncertain or non-idempotent operations, verify resulting state or use its status/idempotency mechanism; do not retry if completion cannot be safely ruled out. Then attempt the lowest-risk documented remedy and retry the original blocked operation. For transient failures, use one bounded retry; change hypothesis after any repeat-identical failure.
4. **Verify and resume.** Call the blocker cleared only if the original operation now succeeds (or a focused equivalent check exercises the same tool route, authorization scope, and prerequisite state without introducing side effects). Immediately resume the user objective; do not call recovery itself delivery.
5. **Handoff if blocked.** Return the objective; operation/tool and target, redacted as needed; observation time and relevant limit/reset time; symptom; evidence; remedies attempted; and the smallest exact user decision/action needed.

## Constraint Patterns

| Blocker | First safe move | Proof of recovery |
|---|---|---|
| Context / max turns | Preserve goal, constraints, completed evidence, and next verified action; continue in a fresh or resumed session when available. Never discard requirements to fit. | The resumed agent can execute the next action with the preserved state. |
| Tool failure / no-op | Distinguish an expected empty result from an unexpected no-op; then classify transient versus deterministic and inspect tool prerequisites and one alternate diagnostic path. | Rerun the failed operation successfully. |
| Rate limit | Honor `Retry-After`; otherwise use bounded backoff. Do not evade quotas, rotate accounts, or create accounts. | The same operation succeeds, or report its verified next retry time. |
| Missing dependency | Reuse project tooling and an isolated local environment. Install only declared, non-secret, project-scoped, version-resolved dependencies. | Import, build, or focused test passes. |
| Provider / authentication failure | Record the exact provider error and inspect the configured route read-only. Request user action for credentials, provider/profile, or approval changes. | The same operation succeeds with visible authorization or available credentials. |
| Permission / secret | Treat as user action required. Never print, request, store, transmit, or weaken secrets/protection. | Retry only after visibly granted consent or available credentials. |

## Evidence-Led Examples

- **Tool no-op:** Capture the failed result and relevant prerequisite state; apply one documented local remedy; rerun that exact tool action. Do not report the diagnosis as success.
- **Rate limit:** Record the response and `Retry-After` when provided; wait only within the task budget, retry once, then hand off the verified next retry time. Never rotate keys or accounts.
- **Context / turn exhaustion:** Preserve objective, constraints, completed evidence, active blocker, and next verified action in a compact handoff. Resume or continue only with those facts intact; do not silently remove requirements.
- **Missing dependency:** Reuse the project lockfile/tooling in an isolated local environment; prove recovery with an import, build, or focused test. Do not modify shared/global runtimes by default.

## Authority Boundaries

Never automatically change approval or YOLO modes, permissions, credentials, provider/model/profile settings, browser trust, account/quota/billing settings, global runtimes, OS/network policy, production data, deployments, migrations, git history, or task acceptance criteria.

Do not restart services, delete/reset state, change global configuration, or spend money without explicit user authorization. Prefer read-only diagnostics and local reversible remedies. Preserve workspace state and prompt/session invariants.

## Evidence Standard

Every recovery claim names:

- symptom: exact failed result;
- cause basis: live state, log, documented limit, or focused check;
- action: remedy attempted;
- proof: successful rerun of the blocked path.

Never invent a runtime limit or treat a plan, HTTP 200, or configuration edit as proof. A repeated failure requires a materially different hypothesis; two failed recovery paths end in a compact blocker handoff.

## Completion Checklist

- [ ] Original objective remains intact.
- [ ] Blocker was evidenced and classified.
- [ ] Any remedy stayed within local, reversible authority.
- [ ] Original blocked path was rerun or the limit is explicitly handed off.
- [ ] Work resumed, or the exact user action needed is stated.
