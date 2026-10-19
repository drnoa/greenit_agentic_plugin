# greenit — Green IT patterns for Claude Code

A [Claude Code](https://claude.com/plugins) plugin that makes **Green IT patterns** the default,
low-friction path during software development — energy-, carbon-, and resource-efficient design,
implementation, and review.

It encodes the **7 technical Green IT principles** (plus measurement and impact-focus best practices)
as model-invoked skills, so Claude applies the right pattern automatically when your task matches —
and ships a reviewer agent that audits a diff or project against all of them.

## What's inside

### Skills

Invoke explicitly as `/greenit:<skill>`, or let Claude invoke them automatically based on task context.

| Skill | Purpose |
| :-- | :-- |
| `calculate-sci` | Compute Software Carbon Intensity `SCI = (E×I+M)/R` (ISO/IEC 21031:2024) for your project |
| `find-green-levers` | Find the highest-impact levers **before** optimizing (Pareto / focus on impact) |
| `minimize-footprint` | Less is more — don't build, store, or run what isn't needed |
| `reduce-data-transfer` | Network efficiency — cut transferred and stored bytes |
| `efficient-compute` | Algorithmic efficiency — minimize and avoid computation |
| `optimize-resource-usage` | Hardware efficiency — maximize utilization, kill idle draw |
| `carbon-aware-scheduling` | Carbon awareness — shift workloads to greener regions/times |
| `hardware-aware-optimization` | Exploit hardware (OLED, ARM/Graviton, crypto units, PUE) |
| `extend-longevity` | Extend device & software lifetime to cut embodied carbon |

Each principle skill covers **frontend, backend, and infrastructure** with concrete patterns,
anti-patterns, quick wins, tools, and before/after measurement via `calculate-sci`.

### Agent

- **`green-reviewer`** — read-only agent that audits a diff, branch, or project against the 7
  principles and reports impact-ranked findings, each routed to its fix skill. Ask for a
  "green review" / "Green IT audit", or @-mention it.

## Example use cases

- **Estimate a service's carbon footprint** — "What's the SCI of this API?" → `calculate-sci` sets a
  system boundary and functional unit, gathers/estimates energy, grid intensity, and embodied carbon,
  and reports gCO₂eq per 1000 requests with a confidence level.
- **Decide where to start** — "Where should I optimize this project for energy?" → `find-green-levers`
  surfaces the biggest structural levers (24/7 idle environments, carbon-heavy region, high-volume
  queries, oversized instances) and ranks them by impact before anyone touches code.
- **Get the right pattern while coding, automatically** — editing image assets or a JS bundle surfaces
  `reduce-data-transfer` (AVIF + `srcset`, tree-shaking, page-weight budgets); adding a cron/batch job
  surfaces `carbon-aware-scheduling` (shift to a greener region/time); writing an ORM query flags an
  N+1 or `SELECT *`.
- **Review a change for energy/carbon regressions** — "Run a Green IT audit on my diff" → the
  `green-reviewer` agent reports impact-ranked findings (`path:line — principle — problem — fix`), each
  routed to the skill that fixes it.
- **Cut mobile battery drain** — building a dark mode or UI animations surfaces
  `hardware-aware-optimization` (true-black `#000000` on OLED, animate only `transform`/`opacity`).
- **Lower infrastructure emissions without code changes** — choosing a cloud region or instance type
  surfaces `carbon-aware-scheduling` (region grid mix), `hardware-aware-optimization` (ARM/Graviton),
  and `optimize-resource-usage` (right-sizing, scale-to-zero, autoscaling).
- **Keep older hardware viable** — setting performance budgets or picking dependency versions surfaces
  `extend-longevity` (Lighthouse CI budgets, LTS stacks, slim images) to avoid software-induced
  hardware obsolescence and its embodied carbon.

## Install

### Claude Code — from this repository's marketplace

This repository is its own plugin marketplace (`greenit-plugins`). Add it once, then install:

```bash
claude plugin marketplace add drnoa/greenit_agentic_plugin
claude plugin install greenit@greenit-plugins
```

Or in one step (Claude Code 2.1.292 or later):

```bash
claude plugin install greenit --marketplace drnoa/greenit_agentic_plugin
```

Inside a session the same works as `/plugin marketplace add drnoa/greenit_agentic_plugin` and
`/plugin install greenit@greenit-plugins`. Machines without a GitHub SSH key should set
`CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1` so the clone runs over HTTPS.

Update to a new release:

```bash
claude plugin update greenit@greenit-plugins
```

### claude.ai, Cowork, and Claude Code — from the Anthropic plugin directory

Once listed in [Anthropic's plugin directory](https://claude.ai/directory), open
**Customize → Plugins → Discover** on claude.ai and add **Green IT**. A plugin added there also loads
in your Claude Code sessions as a synced plugin, or browse it with `/plugin directory` in Claude Code.

### Try it without installing

```bash
git clone https://github.com/drnoa/greenit_agentic_plugin.git
claude --plugin-dir ./greenit_agentic_plugin
```

## Usage

```text
/greenit:calculate-sci            # estimate the current project's carbon intensity
/greenit:find-green-levers        # where should I start optimizing?
/greenit:reduce-data-transfer     # guidance while working on assets, queries, payloads
```

Or just work normally — skills surface themselves (e.g. editing images → `reduce-data-transfer`,
adding a cron/batch job → `carbon-aware-scheduling`). For a review, ask Claude to run a Green IT
audit and it delegates to `green-reviewer`.

## Platform support

| Component | Claude Code | Cowork | claude.ai chat |
| :-- | :-- | :-- | :-- |
| 9 skills | ✅ | ✅ | ✅ |
| `green-reviewer` agent | ✅ | ✅ | ignored (agents don't load in chat) |

## What the plugin runs, sends, and fetches

Nothing. The plugin consists only of Markdown skill and agent files plus an SVG icon. It ships no
hooks, scripts, MCP servers, executables, or package installs, and it makes no network requests of
its own. Skills cite public reference documentation (Green Software Foundation, Electricity Maps,
Boavizta, web.dev, W3C, AWS) as links; nothing is fetched unless you ask Claude to open a link. The
`green-reviewer` agent is read-only: it reads files and runs `git diff`-style inspection commands in
your project and never edits anything.

## Development

```bash
claude --plugin-dir .                 # load the plugin (no install)
claude plugin validate --strict .     # validate marketplace, manifest, skills, and agent
```

After edits, run `/reload-plugins` in-session. See [.claude/CLAUDE.md](.claude/CLAUDE.md) for
authoring conventions and the release/submission process, and [CHANGELOG.md](CHANGELOG.md) for
release history. CI runs the same strict validation on every push.

## Privacy

The plugin collects no data, has no telemetry, and makes no network calls of its own. See
[PRIVACY.md](PRIVACY.md).

## License

[GPL-3.0](LICENSE).
