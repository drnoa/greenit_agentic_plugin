# Changelog

All notable changes to the `greenit` plugin are documented here. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versioning follows
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Bump the `version` in `.claude-plugin/plugin.json` with every release — pinned versions mean users
receive no update until the field changes.

## [Unreleased]

## [1.1.0] — 2026-10-19

Distribution release: the repository is now installable as its own marketplace and prepared for
submission to the Anthropic plugin directory. No change to skill or agent behavior.

### Added

- `.claude-plugin/marketplace.json` — the repository is its own marketplace (`greenit-plugins`).
  Install with `claude plugin marketplace add drnoa/greenit_agentic_plugin` and
  `claude plugin install greenit@greenit-plugins`.
- Directory listing metadata in `plugin.json`: `displayName`, `author.url`, `icon`,
  `documentationUrl`, `supportUrl`, `privacyPolicyUrl`.
- `assets/icon.svg` plugin icon.
- GitHub Actions workflow running `claude plugin validate --strict .` on every push and pull request.
- README sections: marketplace and directory install paths, platform support table, and a
  "what the plugin runs, sends, and fetches" disclosure for the directory security scan.
- `.gitignore` for OS artifacts the directory validator rejects and local Claude Code settings.

### Changed

- Developer guide moved from `CLAUDE.md` to `.claude/CLAUDE.md` so `claude plugin validate --strict`
  passes without warnings (a plugin-root `CLAUDE.md` is never shipped as plugin context). Claude Code
  still loads it as project memory. Guide updated with the release and submission process.

## [1.0.0] — 2026-07-08

Initial public release.

### Added

- `calculate-sci` skill — compute Software Carbon Intensity `SCI = (E×I+M)/R` (ISO/IEC 21031:2024)
  for the current project.
- Seven principle skills implementing the technical Green IT principles across frontend, backend, and
  infrastructure: `minimize-footprint`, `reduce-data-transfer`, `efficient-compute`,
  `optimize-resource-usage`, `carbon-aware-scheduling`, `hardware-aware-optimization`,
  `extend-longevity`.
- `find-green-levers` skill — Pareto-based lever discovery before optimizing.
- `green-reviewer` agent — read-only audit of a diff/project against the 7 principles with
  impact-ranked, fix-routed findings.
- Plugin manifest, README (with example use cases), and developer guide (CLAUDE.md).

[Unreleased]: https://github.com/drnoa/greenit_agentic_plugin/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/drnoa/greenit_agentic_plugin/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/drnoa/greenit_agentic_plugin/releases/tag/v1.0.0
