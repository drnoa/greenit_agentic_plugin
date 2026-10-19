# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working on this repository.

> Note: this file is developer documentation for **working on** the plugin. It lives in `.claude/`
> (not at the plugin root) on purpose: Claude Code still loads `.claude/CLAUDE.md` as project memory,
> but a `CLAUDE.md` at the plugin root is **not** shipped as plugin context and makes
> `claude plugin validate --strict` fail with a warning. Plugins ship context via skills and agents.

## What this repository is

The **`greenit`** Claude Code plugin (see <https://claude.com/plugins>). Its skills and agent drive the
consistent application of **Green IT patterns** — energy-, carbon-, and resource-efficient design,
implementation, and review — based on 7 technical principles + supporting best practices.

The repository is **both the plugin and its own marketplace**: `.claude-plugin/plugin.json` is the
plugin manifest, `.claude-plugin/marketplace.json` lists that plugin with `"source": "./"`.

## Inventory

Manifest: `.claude-plugin/plugin.json` (name `greenit`; skills namespace as `/greenit:<skill>`).
Marketplace: `.claude-plugin/marketplace.json` (name `greenit-plugins`; install id
`greenit@greenit-plugins`).

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
principles and reports impact-ranked findings, each routed to its fix skill. Agents load in Claude
Code and Cowork only; claude.ai chat ignores them (skills load everywhere).

Each principle skill covers the principle across **frontend / backend / infrastructure** with
anti-patterns, quick wins, tools, and cross-links to related skills and `calculate-sci`.

## Authoring conventions

- **Self-contained & shippable.** Skills/agent must not depend on repo-local files that aren't plugin
  components. The `Knowledge/` folder is authoring source material **only** — it is not a plugin
  component and must never be referenced from `skills/` or `agents/`. Embed the guidance and cite the
  **primary external source** (Green Software Foundation, Electricity Maps, Boavizta, web.dev, W3C,
  AWS Graviton), never `Knowledge/…`.
- **Trigger-rich `description`.** A skill's frontmatter `description` decides *when* Claude invokes it;
  list concrete triggering situations. Vague descriptions never fire. Keep `description` a single
  text value (the directory validator rejects lists).
- **Pareto ordering.** Put structural levers (region, 24/7 idle, sizing, retention) before code-level
  tuning in guidance.
- **Keep tight.** ~60–110 lines per SKILL.md; scannable bullets over prose. Tie every pattern to a
  measurable outcome (fewer requests, less data, less compute, less energy), not generic "clean code".
- **Structure rule.** Only `plugin.json` and `marketplace.json` live in `.claude-plugin/`; `skills/`,
  `agents/`, `assets/` stay at the plugin root. Nesting components inside `.claude-plugin/` is the
  most common load failure.
- **No executable code.** The plugin is static Markdown + one SVG icon: no hooks, scripts, MCP
  servers, `bin/`, or lockfiles. Keeping it that way keeps the directory's security scan trivial and
  the privacy policy true. If you ever add one, update `README.md` ("What the plugin runs") and
  `PRIVACY.md` in the same change.
- **Directory hygiene.** No `.DS_Store`/`Thumbs.db`, no symlinks or submodules, every non-image file
  under 256 KiB, README ≥ 40 words, `LICENSE` present.

## Development commands

```bash
claude --plugin-dir .                 # run Claude Code with this plugin loaded (no install)
claude plugin validate --strict .     # validate marketplace + manifest + skills/agent; must print "Validation passed"
```

In-session after edits: `/reload-plugins`. Verify components:

- Skills: invoke `/greenit:<skill-name>`.
- Agent: confirm `green-reviewer` under `/context` → Custom Agents, or @-mention by scoped name.
- Auto-invocation spot-check: editing image/bundle assets should surface `reduce-data-transfer`;
  adding a cron/batch job should surface `carbon-aware-scheduling`.

End-to-end install test from the local marketplace (what users get):

```bash
claude plugin marketplace add .                 # registers "greenit-plugins"
claude plugin install greenit@greenit-plugins
claude plugin details greenit                   # expect Skills (9), Agents (1)
claude plugin marketplace remove greenit-plugins  # clean up (also uninstalls the plugin)
```

## Releasing

1. Bump `version` in `.claude-plugin/plugin.json` (semver) and add a `CHANGELOG.md` entry. Users on a
   pinned version receive **nothing** until the field changes. Don't set `version` in the marketplace
   entry (plugin.json wins and the validator warns on a mismatch).
2. `claude plugin validate --strict .` must pass (CI runs the same check in
   `.github/workflows/validate.yml`).
3. Merge to `main`. Both distribution channels follow `main`: the own marketplace
   (`claude plugin marketplace add drnoa/greenit_agentic_plugin`) and the Anthropic directory
   submission (tracked branch).
4. Optional: `claude plugin tag --push` creates the `greenit--v<version>` tag (only needed if other
   plugins declare a version range on this one).
5. Never rename the plugin (`name`). Change `displayName` instead; a rename needs a `renames` map in
   `marketplace.json`.

## Distribution channels

- **Own marketplace (live as soon as it's on `main`):** users run
  `claude plugin marketplace add drnoa/greenit_agentic_plugin` then
  `claude plugin install greenit@greenit-plugins`.
- **Anthropic plugin directory (claude.ai, Cowork, Claude Code via account sync):** submit from the
  developer portal at <https://claude.ai/directory/manage> (**Submit new → Plugin bundle**), repository
  `drnoa/greenit_agentic_plugin`, plugin path empty (root), tracked branch `main`. Needs a paid
  claude.ai plan and the GitHub account connected on claude.ai with push access; the repo must be
  public before publishing. The portal's **Validate** runs more checks than the CLI
  (README/license/name uniqueness/security scan) — see
  <https://claude.com/docs/plugins/pre-submission-checklist>. Listing fields read from `plugin.json`:
  `displayName`, `description`, `icon`, `documentationUrl`, `supportUrl`, `privacyPolicyUrl`.
  Direct PRs to `anthropics/claude-plugins-community` are closed automatically; the directory is the
  only route in.
