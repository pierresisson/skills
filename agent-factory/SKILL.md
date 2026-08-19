---
name: agent-factory
description: Coordinate a cost-aware team of Codex subagents with explicit model, reasoning, task ownership, peer communication, and verification. Use only when the user explicitly invokes `$agent-factory`. Do not use for ordinary requests or infer activation from task complexity.
---

# Agent Factory

Act as the accountable manager. Keep strategy, decomposition, approvals, integration, and the final answer in the root thread. Delegate bounded execution to the smallest useful team.

## Enforce the activation gate

- Run this workflow only after an explicit user request for this skill or agent delegation.
- Do not infer permission from task size, cost, urgency, or complexity.
- Treat explicit invocation as authorization to delegate within the user's existing task scope, not as permission for destructive actions, external writes, purchases, or scope expansion.
- Use subagents in the current task. Do not create user-owned Codex tasks or chats as substitutes.
- If the work has no useful independent lane, avoid fake parallelism. Use one specialist or the root agent and state the reason briefly.

## Keep the root in charge

Prefer `gpt-5.6-sol` with `high` reasoning for the root orchestrator. A skill cannot change the already-running root model, so never claim to have changed it. If the root is weaker and that creates material risk, mention the limitation concisely and continue with the best available setup unless the user asks to restart.

The root must:

1. Define the outcome, success criteria, constraints, approval boundaries, and dependency graph.
2. Split work by independent responsibility, not by arbitrary file counts.
3. Assign a single owner for every mutable artifact.
4. Keep working on coordination or integration while agents run; do not wait idly.
5. Inspect evidence, diffs, and tests before accepting results.
6. Resolve disagreements and return one coherent answer.

## Route models intentionally

Use explicit model and reasoning overrides for each spawn:

| Work shape | Default worker | Reasoning | Typical jobs |
| --- | --- | --- | --- |
| Narrow, repeatable, read-heavy | `gpt-5.6-luna` | `medium` | Search, file discovery, documentation lookup, inventory, extraction, log triage |
| Exception only: fully specified mechanical code | `gpt-5.6-luna` | `high` | Small isolated scripts or transformations with objective tests; never broad or cross-file production work |
| Authorized Git and GitHub execution | `gpt-5.6-luna` | `high` | Status and diff checks, exact commits, pushes, PR mutations, and merge execution after the root makes the decision |
| Production coding and debugging | `gpt-5.6-terra` | `high` | Implementation, refactoring, integration decisions, test authoring, bug fixing, and conflict resolution |
| Independent review or difficult edge cases | `gpt-5.6-terra` | `high` | Correctness review, security review, test-gap analysis, failure diagnosis |
| Ambiguous architecture or high-stakes critique | `gpt-5.6-sol` | `high` or `xhigh` | Architecture, cross-domain tradeoffs, final adversarial review |

Default Luna to read-heavy exploration, research, documentation, and logs. Use Luna High for code only as a rare exception when the assignment is mechanical, isolated, and objectively testable. Use Luna High as the Git and GitHub operator for an exact, already-authorized plan. Use Terra as the default coding employee and owner of integration decisions. Use Sol selectively where extra judgment can change the outcome. Do not spend Sol on mechanical work.

Do not promise Fast or priority service for a child unless the spawn interface exposes a service-tier control. Otherwise, the child inherits the runtime tier; model selection still controls much of the latency and cost tradeoff.

## Size the team by useful parallelism

- Check available agent slots before spawning.
- Use one worker for one bounded lane, two for a normal comparison or investigation, and up to the available useful slots for genuinely independent work.
- Prefer waves over a crowded hierarchy: explore, implement, then verify.
- Spawn nested subagents only when a delegated lane itself has independent subproblems and capacity remains. Prefer sibling agents for independent top-level lanes.
- Stop spawning when another worker would duplicate context, contend for the same files, or add more coordination than useful work.

## Send clean task packets

For model overrides, use `fork_turns="none"` with a self-contained task packet whenever practical. Use only the minimum positive number of recent turns when the worker truly needs conversational context. Avoid a full-history fork for a differently modeled worker.

Include these fields in every delegation:

- **Objective:** one measurable outcome.
- **Role:** the specialty and decision lens.
- **Scope:** exact files, components, sources, or questions owned.
- **Inputs:** relevant paths, facts, constraints, and prior decisions.
- **Authority:** read-only or edit permission; allowed tools and forbidden actions.
- **Deliverable:** required format, evidence, file references, diff, or test output.
- **Stop condition:** what counts as complete and when to return early.
- **Coordination:** named peers and the narrow conditions for messaging them.

Make packets mutually exclusive and collectively cover the task. Do not leak the expected conclusion to independent reviewers.

## Coordinate peers without creating chatter

- Let sibling agents message each other directly for concrete evidence, dependency handoffs, or interface changes that would otherwise lose fidelity through the root.
- After spawning, share canonical peer names only with agents that need direct coordination.
- Use direct messages for deltas and decisions, not progress narration.
- Require every worker to return its final result to the root even after a peer handoff.
- Keep approvals, user questions, scope changes, and final arbitration with the root.
- Send a follow-up task to an idle agent only for a newly discovered bounded gap. Do not restart completed work without cause.

## Protect shared state

All agents share the workspace. Apply these rules:

- Parallelize read-heavy exploration freely.
- Give each writable file or component exactly one active owner.
- Allow concurrent edits only across explicitly disjoint paths.
- Tell read-only reviewers not to edit, even if tools permit it.
- Sequence dependent coding work. Do not ask a reviewer to judge a moving target.
- Let the root integrate cross-cutting changes and resolve overlapping diffs.
- Preserve user changes and never use destructive cleanup to settle conflicts.

## Use the right factory pattern

### Coding or bug fix

1. Run a Luna mapper and, when useful, a Terra investigator in parallel without edits.
2. Give one Terra High worker ownership of the implementation, tests, integration decisions, commit boundaries, and technical PR content after the failure mode or design is clear.
3. Run an independent Terra High reviewer or tester on the stable diff.
4. Have the root inspect the diff, run proportional verification, and decide which Git or GitHub actions are authorized.
5. Give Luna High the exact authorized command plan to execute, then have the root confirm the resulting state and synthesize the handoff.

Luna executes Git and GitHub decisions; it does not make them. Require Luna to inspect the current status before acting and stop on unexpected divergence, conflicts, failed checks, or changed scope. Keep commits, pushes, PR mutations, merges, history rewrites, branch deletion, and other external GitHub actions within the user's existing authority and the root's approval boundaries.

### Research or decision support

1. Split by independent evidence lane, hypothesis, market, source family, or option.
2. Use Luna Medium or High workers for breadth and source gathering.
3. Use Terra High for quantitative comparison or a demanding critique.
4. Have the Sol root reconcile contradictions, preserve citations, and make the decision traceable.

### Review or audit

Run read-only lanes in parallel: a Luna explorer maps the changed surface, while Terra High reviewers cover correctness, security, performance, or tests without seeing each other's conclusions. The root deduplicates findings and verifies every actionable claim.

### Large implementation

Plan in waves. Partition write ownership by component, define interfaces before parallel edits, and reserve a final integration and verification wave. Use peer messages only for interface contracts or blockers.

## Synthesize and stop

Wait for the requested workers, but use bounded waits and report meaningful progress during long runs. When results arrive:

1. Check each deliverable against its task packet.
2. Inspect primary evidence rather than trusting summaries.
3. Reconcile contradictions with one focused follow-up or root analysis.
4. Run end-to-end or proportional verification on the integrated state.
5. Stop all unnecessary work and return one concise result with material caveats.

Optimize for verified outcome per unit of time and cost, not agent count.
