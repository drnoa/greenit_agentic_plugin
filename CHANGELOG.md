# Changelog

All notable changes to the `greenit` plugin are documented here. Format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versioning follows
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Bump the `version` in `.claude-plugin/plugin.json` with every release — pinned versions mean users
receive no update until the field changes.

## [Unreleased]

## [0.1.0] — 2026-07-08

Initial release.

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
- Plugin manifest, README, and developer guide (CLAUDE.md).

[Unreleased]: https://github.com/drnoa/greenit_agentic_plugin/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/drnoa/greenit_agentic_plugin/releases/tag/v0.1.0
