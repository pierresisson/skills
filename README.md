# Codex Skills

Personal, reusable skills for OpenAI Codex.

## Repository structure

Each skill lives in its own folder so the repository can grow without mixing skill packages with repository-level documentation.

```text
skills/
├── README.md
└── agent-factory/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

## Agent Factory

`agent-factory` is an explicit, cost-aware orchestration skill for complex work that benefits from focused subagents. The primary Sol agent remains accountable for strategy, task decomposition, approvals, arbitration, integration, and the final response.

The skill is intentionally opt-in. It activates only when `$agent-factory` is invoked and never because a task merely looks difficult.

### Model routing

| Role | Default configuration | Responsibilities |
| --- | --- | --- |
| Orchestrator | Sol High | Strategy, planning, arbitration, coordination, verification, and synthesis |
| Engineer | Terra High | Production coding, debugging, tests, code review, conflict resolution, and integration decisions |
| Explorer | Luna Medium or High | Repository exploration, research, documentation, logs, extraction, and bounded analysis |
| Git/GitHub operator | Luna High | Execute an exact authorized plan for commits, pushes, PR updates, or merges |
| Architect or exceptional reviewer | Sol High or XHigh | Complex architecture and high-stakes adversarial review |

Luna may execute a Git or GitHub decision, but the root orchestrator must make and authorize that decision first.

### How it works

The orchestrator uses the smallest useful team and generally works in shallow waves:

1. Explore the problem with read-heavy workers.
2. Assign one owner to each writable surface.
3. Implement with Terra High.
4. Review and test the stable result independently.
5. Let Sol verify and authorize any final Git or GitHub operation.
6. Let Luna High execute the exact authorized operation when appropriate.

Agents receive bounded task packets with a concrete objective, exclusive scope, authority limits, expected evidence, and a stop condition. Parallel writes are allowed only on explicitly disjoint paths.

### Good use cases

Use Agent Factory for work such as:

- implementing a multi-file or cross-layer feature;
- investigating a difficult bug through independent hypotheses;
- reviewing a pull request across correctness, security, performance, and test coverage;
- coordinating frontend, backend, data, or infrastructure changes;
- planning and validating a migration;
- exploring a large or unfamiliar repository before implementation;
- researching several independent sources or technical options;
- diagnosing an incident from code, logs, configuration, and documentation;
- executing a high-value task where an independent verification pass is worthwhile.

### Poor use cases

Prefer a single agent for:

- a tiny or obvious one-file change;
- a single factual lookup;
- work where every step depends on the previous step;
- tasks dominated by one slow external operation;
- changes where several agents would contend for the same files;
- routine work whose value does not justify additional token usage.

### Design review

The current design has strong safeguards:

- explicit activation prevents accidental multi-agent spending;
- Sol retains decisions and final accountability;
- Terra owns production engineering rather than mechanical operations;
- Luna handles fast, bounded work and authorized Git/GitHub execution;
- one-writer ownership protects the shared filesystem;
- peer communication is limited to useful handoffs;
- independent verification is proportional to risk;
- the team remains shallow unless a genuinely independent subtree warrants another level.

The main limitations are operational:

- subagents consume more tokens than a comparable single-agent run;
- the service tier is inherited because Fast mode cannot currently be selected per child;
- model routing improves the tradeoff but does not guarantee lower total cost;
- the benefit depends on finding real parallelism rather than creating artificial roles;
- the skill still needs representative usage data before further optimization.

### Potential additions

Useful additions after several real runs could include:

- `fast`, `balanced`, and `deep` execution profiles;
- optional limits for workers, waves, or token-sensitive work;
- model fallbacks when a preferred worker is unavailable;
- a compact post-run report covering elapsed time, assignments, duplicated work, conflicts, and verification results;
- a small benchmark set based on representative coding, review, research, and incident tasks.

These additions should be driven by observed failures or measurements rather than added preemptively.

## Future factory ideas

The following are ideas only and are not implemented yet:

| Factory | Purpose |
| --- | --- |
| `research-factory` | Parallel source discovery, evidence extraction, fact checking, citation review, and synthesis |
| `review-factory` | Independent PR lanes for correctness, security, performance, maintainability, and tests |
| `incident-factory` | Log and telemetry analysis, competing root-cause hypotheses, remediation, and verification |
| `product-factory` | User research synthesis, product requirements, UX flows, technical design, and delivery planning |
| `data-factory` | Data-quality checks, analysis, visualization, interpretation, and independent validation |
| `release-factory` | Versioning, changelog preparation, build verification, release checks, and authorized publication |
| `content-factory` | Research, outline, drafting, editing, fact checking, localization, and final QA |

Specialized factories should be created only when their workflow is repeated often enough to justify a dedicated skill. Until then, `agent-factory` remains the general-purpose option.

## Installation

Clone the repository and copy the skill into the user-level Codex skills directory:

```sh
git clone https://github.com/pierresisson/skills.git
cp -R skills/agent-factory ~/.codex/skills/
```

## Usage

Invoke the skill explicitly:

```text
$agent-factory handle this task with an appropriate agent team
```

Without `$agent-factory`, Codex keeps its normal single-agent workflow.
