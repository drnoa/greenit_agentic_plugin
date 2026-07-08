# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> Note: this `CLAUDE.md` is developer documentation for **working on** the plugin. Claude Code does
> **not** load a plugin-root `CLAUDE.md` as context — plugins ship context via skills, agents, and
> hooks. `claude plugin validate` warns about this file; the warning is expected and benign.

## What this repository is

The **`greenit`** Claude Code plugin (see https://claude.com/plugins). Its skills and agent drive the
consistent application of **Green IT patterns** — energy-, carbon-, and resource-efficient design,
implementation, and review — based on 7 technical principles + supporting best practices.

## Inventory

Manifest: `.claude-plugin/plugin.json` (name `greenit`; skills namespace as `/greenit:<skill>`).

**Skills** (`skills/<name>/SKILL.md`, model-invoked by task context):

| Skill | Green IT principle / role |
| :-- | :-- |
| `calculate-sci` | Software Carbon Intensity `SCI = (E×I+M)/R`, ISO/IEC 21031:2024 — the measurement metric |
| `find-green-levers` | Pareto: find the highest-impact levers before optimizing |
| `minimize-footprint` | Less is more (minimalism) |
| `reduce-data-transfer` | Network efficiency (data volume & transfer) |
| `efficient-compute` | Algorithmic efficiency (minimize/avoid compute) |
| `optimize-resource-usage` | Hardware efficiency (utilization, no idle draw) |
| `carbon-aware-scheduling` | Carbon awareness (spatial/temporal shifting) |
| `hardware-aware-optimization` | Technological understanding (OLED, ARM, crypto units, PUE) |
| `extend-longevity` | Longevity (extend device & software lifetime → lower embodied carbon) |

**Agent** (`agents/green-reviewer.md`): read-only reviewer that audits a diff/project against the 7
principles and reports impact-ranked findings, each routed to its fix skill.

Each principle skill covers the principle across **frontend / backend / infrastructure** with
anti-patterns, quick wins, tools, and cross-links to related skills and `calculate-sci`.

## Authoring conventions

- **Self-contained & shippable.** Skills/agent must not depend on repo-local files that aren't plugin
  components. The `Knowledge/` folder is authoring source material **only** — it is not a plugin
  component and must never be referenced from `skills/` or `agents/`. Embed the guidance and cite the
  **primary external source** (Green Software Foundation, Electricity Maps, Boavizta, web.dev, W3C,
  AWS Graviton), never `Knowledge/…`.
- **Trigger-rich `description`.** A skill's frontmatter `description` decides *when* Claude invokes it;
  list concrete triggering situations. Vague descriptions never fire.
- **Pareto ordering.** Put structural levers (region, 24/7 idle, sizing, retention) before code-level
  tuning in guidance.
- **Keep tight.** ~60–110 lines per SKILL.md; scannable bullets over prose. Tie every pattern to a
  measurable outcome (fewer requests, less data, less compute, less energy), not generic "clean code".
- **Structure rule.** Only `plugin.json` lives in `.claude-plugin/`; `skills/`, `agents/`, `hooks/`
  stay at the plugin root. Nesting them is the most common load failure.

## Development commands

```bash
claude --plugin-dir .        # run Claude Code with this plugin loaded (no install)
claude plugin validate .     # validate manifest + skill/agent frontmatter (run before commit/submit)
```

In-session after edits: `/reload-plugins`. Verify components:

- Skills: invoke `/greenit:<skill-name>`.
- Agent: confirm `green-reviewer` under `/context` → Custom Agents, or @-mention by scoped name.
- Auto-invocation spot-check: editing image/bundle assets should surface `reduce-data-transfer`;
  adding a cron/batch job should surface `carbon-aware-scheduling`.

## Submission readiness (community marketplace)

Submit via the in-app form (claude.ai directory or platform.claude.com/plugins/submit). The review
pipeline runs `claude plugin validate` + automated safety screening; approved plugins are pinned to a
commit SHA in `anthropics/claude-plugins-community`.

Current status against the guidance:

- ✅ `plugin.json` with required `name`; `claude plugin validate .` passes (only the benign CLAUDE.md
  warning).
- ✅ `LICENSE` present (GPL-3.0).
- ✅ Explicit `version` set — **bump it on every release you want users to receive** (pushing commits
  alone is ignored when a version is pinned). Follow semver and record changes in `CHANGELOG.md`.
- ✅ `README.md` (install + usage).
- ✅ `plugin.json` discovery/metadata: `keywords`, `repository`, `homepage` set.
- ✅ `CHANGELOG.md` present.
