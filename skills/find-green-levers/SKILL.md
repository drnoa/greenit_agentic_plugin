---
description: >-
  Find the highest-impact Green IT levers in a project before optimizing (Pareto / focus-on-impact).
  Use when the user asks where to start on energy/carbon, what the biggest levers are, how to reduce a
  project's footprint, or to prioritize a green optimization effort. Counters the bias of tuning
  interesting-but-marginal code while ignoring bigger structural waste.
---

# Find the biggest Green IT levers (focus on impact)

~20% of measures deliver ~80% of the effect, but teams systematically pick the wrong 20%: they
optimize what is technically interesting, controllable, and inside their own code (refactor a query,
shrink a bundle) while the largest energy drivers sit in operational and architectural decisions. A
server running 24/7 with no night traffic, or an always-on test environment, wastes more than dozens
of code optimizations combined — and is invisible because it isn't in the code.

**Do this before an optimization campaign.** Structural levers first, code-level second.

## Procedure

1. **Inspect the project** — classify it (frontend, backend, infra, ML/AI) and its dominant workload,
   from code, IaC/config, CI, deploy manifests, and any telemetry available.
2. **Ask the structural high-impact questions** (these find the big levers):
   - **Runtime & utilization** — which environments/services run 24/7? At what average utilization?
     Any non-prod (test/staging/CI) running outside office hours? → [optimize-resource-usage],
     [minimize-footprint]
   - **Region & grid mix** — which cloud region(s), and how carbon-intensive is that grid? Are
     time-flexible/stateless workloads there by default? → [carbon-aware-scheduling]
   - **Volume hotspots** — which endpoints/queries/jobs carry the highest transaction volume? Any
     `SELECT *`, N+1, uncached hot aggregations, or polling on the busiest paths? → [reduce-data-transfer],
     [efficient-compute]
   - **Sizing** — any over-provisioned or chronically <20% instances? Autoscaling / scale-to-zero
     absent where load is spiky or daytime-only? → [optimize-resource-usage]
   - **Storage & retention** — log/backup retention without a policy? Daily full backups? Hot storage
     for cold data? → [reduce-data-transfer], [minimize-footprint]
   - **Hardware & lifetime** — x86-only where ARM fits? Depreciation-driven refresh? New hardware for
     non-prod? → [hardware-aware-optimization], [extend-longevity]
   - **Client footprint** (frontend) — page weight, unused JS, image formats, OLED dark mode, sluggish
     on old devices? → [reduce-data-transfer], [hardware-aware-optimization], [extend-longevity]
3. **Rank levers by estimated impact** — structural (region, 24/7 idle, sizing, retention) above
   code-level (algorithm tuning, micro-optimizations). Note where a measure needs no code change
   (region switch, right-size, schedule) — those are usually the fastest wins.
4. **Recommend measuring the top levers** with [calculate-sci] to get before/after evidence, then
   point to the specific principle skill for each lever's fix.

## Output

A short ranked list: **lever — estimated impact — effort — which principle skill applies**. Lead with
the single biggest lever. Flag explicitly when a proposed effort is low-impact code tuning while a
larger structural lever is being ignored.

## Related

Every lever routes to a principle skill (linked above). Quantify with [calculate-sci].

## Sources

- Green Software Foundation: https://greensoftware.foundation
- Electricity Maps — grid carbon intensity by region: https://electricitymaps.com
