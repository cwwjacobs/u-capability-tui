# U-Capability TUI

Model-first local capability inventory for agent skill systems.

This project is not primarily a human dashboard. The human view is secondary. The source of truth is a machine-readable capability registry, per-skill manifests, and a relationship graph that local agents can use to audit, compare, equip, and plan skills.

## Core principle

> Folders are for humans. Manifests are for machines. Graphs are for planning.

## What it answers

- What capability/skill systems exist on this machine?
- Which skills are installed, enabled, duplicated, missing, stale, or unknown?
- Which skills are user-authored, generated, repo-imported, or unresolved?
- Which skills support KSL stages such as map, walk, verify, receipt, boundary, repair, and ultra?
- Which skills consume or emit artifacts needed for a task?
- Which skills expose authority surfaces such as shell, network, file writes, deletion, token/env handling, or configuration mutation?
- Which skills should be equipped into a target folder for a given audit or KSL plan?

## Machine-first outputs

The viewer must emit these artifacts before any UI polish:

```text
registry.jsonl
registry.json
skill-graph.json
skill-graph.mmd
review-queue.md
index.html
normalized-preview/
```

The UI reads the registry. The registry does not define the registry.

## Vocabulary

```text
Domain
  Broad operational area such as audit, boundary, intake, mcp, workflow, token, planning, replay, claim, dependency, orchestration.

Capability Class
  Job family such as dependency-map, claim-map, web-intake, prompt-injection, skillchain, replay-eval, token-compression.

Skill
  A concrete executable or instruction bundle with a stable skill_id.

Mode
  How the skill runs, such as read-only scan, dry-run, copy, equip, serve, or mutate-with-apply.

Surface
  Where the skill can be equipped, such as Qwen, Codex, AFR, repo-local .agent/skills, or generic agent.

Artifact
  What the skill emits, such as reports, JSONL, Mermaid graphs, receipts, plans, manifests, or patches.

Authority Surface
  What the skill may touch or affect: file read, file write, shell, network, env/token handling, config mutation, deletion.
```

## Intended command shape

```bash
ucap scan \
  --root ~/.qwen/skills \
  --root ~/.codex/skills \
  --root ~/Desktop/agent-flight-recorder \
  --root ~/Desktop/skills-and-tools-mcp \
  --out ~/.u-capability-viewer

ucap audit ~/.codex/skills --against ~/.skill-vault --needs repo-audit,boundary,claim-map,ksl-plan

ucap equip --target ~/.codex/skills --from ~/.skill-vault --missing --dry-run

ucap serve --root ~/.skill-vault --host 127.0.0.1 --port 8787
```

## Safety boundary

- Read-only by default.
- No deletion by default.
- No overwrite by default.
- Mutating actions require `--apply`.
- Network is disabled except explicit local `serve`.
- API keys, tokens, and secret values must be redacted.
- Findings are review items, not accusations.
- Authority surfaces are observed patterns, not proof of unsafe behavior.
- This project is an inventory, audit, equip, and planning helper. It is not a proof system or security scanner.

## Relationship to KSL / U-KSL

U-Capability TUI gives agents a machine-readable view of operational capability. It supports KSL and U-KSL planning by mapping:

```text
capability -> inputs -> outputs -> authority surfaces -> artifacts -> KSL stage -> related skills
```

A local agent should be able to ask:

> I need to audit this project. Which capabilities do I already have? Which are missing? Which skills should I equip? What artifacts will they produce? What authority surfaces do they touch?
