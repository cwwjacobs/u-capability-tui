# Machine Model

U-Capability is machine-first. The terminal UI or HTML viewer is a projection over the registry, not the source of truth.

## Layers

```text
human folder tree
  useful for browsing and manual review

skill.manifest.yaml / skill.manifest.json
  per-skill machine contract

registry.jsonl
  canonical vault index; one skill per line

skill-graph.json
  relationship graph for planning and equip decisions

viewer
  human projection over the registry and graph
```

## Why not use folders as truth?

Folder names are useful but lossy. A skill may support multiple domains, multiple KSL stages, multiple surfaces, and multiple artifact types. A single path cannot express that without forcing bad taxonomy decisions.

The folder path may say:

```text
skills/audit/dependency-map/dependency-boundary-mapper/
```

But the manifest can say:

```yaml
id: dependency-boundary-mapper
domains:
  - audit
  - boundary
capability_classes:
  - dependency-map
  - architecture-map
  - review-queue
ksl_stage:
  - map
  - verify
  - receipt
compatible_surfaces:
  - qwen
  - codex
  - afr
  - generic-agent
```

## Graph relationships

A local agent should be able to reason over edges:

```text
safe-web-intake -> upstream of -> dif-defense
dif-defense -> emits_for -> agent-flight-recorder
project-audit-mapper -> requires -> dependency-boundary-mapper
claim-boundary-guard -> related to -> sourceboundary
```

## Required machine questions

The registry and graph should let an agent answer:

1. Can I use this skill for this task?
2. What inputs does it require?
3. What outputs and artifacts will it emit?
4. Does it mutate files or only write to an output directory?
5. Does it need network, shell, tokens, config mutation, or deletion authority?
6. Which KSL stage does it support?
7. Is it already installed on this surface?
8. Is there a canonical copy in the vault?
9. Is there a duplicate, alias, superseded version, or conflict?
10. Which missing skills should be equipped for this plan?

## Default safety contract

- scan is read-only
- collect defaults to copy
- audit is read-only
- equip defaults to dry-run
- serve binds to 127.0.0.1
- move, overwrite, delete, or target mutation require explicit `--apply`
- secrets must be redacted
- review items are not accusations
- authority surfaces are observed patterns, not proof of unsafe behavior
