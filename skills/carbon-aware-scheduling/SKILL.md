---
description: >-
  Apply the Green IT "Carbon Awareness" principle — the same kWh emits very different CO₂ depending on
  when and where it runs (grid mix varies by factor ~15–30). Use when choosing a cloud region, writing
  a cron/batch job, backup, index rebuild, ML training run, or any time-flexible workload; when
  deferring non-urgent network sync on a device; or when adapting behavior to connection/data-saver
  state.
---

# Carbon Awareness (manage demand)

Electricity does not carry a constant CO₂ footprint. Sweden (hydro) is under 20 gCO₂/kWh; Poland over
700 — factor ~30 for identical server-side energy. Time matters too: many European grids are 20–50%
lower-carbon at night (low industrial load, higher wind share). The levers: **spatial shifting** (run
in greener regions) and **temporal shifting** (run in greener time windows). Both require the workload
to be location- or time-flexible — real-time requests can't shift, but batch jobs, reports, backups,
and training runs usually can.

## Frontend

- **Background Sync API** — defer syncs to a favorable moment: on Wi-Fi instead of mobile, while
  charging, while the user is idle. Spares the power-hungry radio.
- **NetworkInformation API** — on `slow-2g`/`2g` or `saveData: true`, drop to lower image quality,
  stop autoplay, pause background work. Ignoring `saveData` ignores an explicit user preference.
- **`prefers-reduced-motion` + eco-mode** — respect the OS motion setting; offer a switch that swaps
  HD media for compressed variants and background video for static posters.

## Backend

- **Carbon-aware batch scheduling** — a report that "runs at midnight" runs then for historical
  reasons, not energy ones. A carbon-aware scheduler picks the lowest-intensity window within a
  tolerance; job logic unchanged, only the start moves.
- **Spatial shifting** — for stateless tasks without strict latency needs, moving region is the single
  most effective carbon measure: Frankfurt (~300–400 g) vs Stockholm (<20 g) is factor 15–20 at
  identical code. A 120 kWh ML training job: 48 kg CO₂ in Frankfurt vs 2.4 kg in Stockholm.
- **Demand shaping** — at high carbon intensity, throttle non-essential background work (less
  aggressive prefetch, lower worker parallelism). Read carbon-intensity as a config parameter. Most
  complex; worth it at very high transaction volume.

## Infrastructure

- **Region choice** — the fastest, highest-impact, no-code-change measure. Check a target region's
  carbon intensity / CFE% alongside latency and data-protection before initial deployment.
- **Temporal shifting** — start backups, index rebuilds, cleanup jobs when the grid is greenest, not
  rigidly at 02:00. A 15 kWh daily index rebuild: 5.7 kg CO₂ (Frankfurt, 14:00) vs 0.27 kg (Stockholm,
  night) — factor 21, region + start time only.
- **Carbon-intensity monitoring** — export Electricity Maps data as a Prometheus metric, visualize in
  Grafana, and trigger scale-down actions when intensity is high.

## Enabler

Use the **Green Software Foundation Carbon Aware SDK** (Electricity Maps data) to pick the optimal
region/time window automatically. Prime candidates: batch jobs, reports, backups, training runs,
spot/preemptible-friendly work.

## Anti-patterns to flag

Deploying batch/stateless workloads to a carbon-heavy region by default; fixed-time cron for
time-flexible jobs; device apps ignoring `saveData`/connection type; no region carbon check before deploy.

## Quick wins

Move a batch/stateless workload to a greener region (one config line); reschedule a nightly job to the
greenest window; honor `saveData` and `prefers-reduced-motion`.

## Related

Measure before/after with [calculate-sci]. Queue-based prioritization by intensity:
[optimize-resource-usage]. Region PUE and grid mix: [hardware-aware-optimization].

## Sources

- Electricity Maps — live carbon intensity by region: https://electricitymaps.com
- Green Software Foundation — Carbon Aware SDK: https://greensoftware.foundation
- Google Cloud — Carbon Free Energy by region: https://cloud.google.com/sustainability/region-carbon
