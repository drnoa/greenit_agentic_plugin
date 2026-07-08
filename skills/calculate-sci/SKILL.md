---
description: >-
  Calculate the Software Carbon Intensity (SCI, ISO/IEC 21031:2024) of the current
  project — the CO₂ emitted per unit of useful work. Use when the user asks to
  measure, estimate, compute, or track the carbon footprint / SCI / carbon intensity
  of this codebase, a service, an endpoint, or a feature, or to compare emissions
  before and after an optimization.
---

# Calculate Software Carbon Intensity (SCI)

Compute the SCI for the project this skill runs in. SCI is the leading metric for green
software: it measures carbon emitted **per unit of useful work**, not absolute emissions. A
system that grows in absolute usage but emits less per request improves its SCI.

## The formula

```
SCI = (E × I + M) / R
```

| Symbol | Meaning | Typical unit |
| :----- | :------ | :----------- |
| `E` | Energy consumed by the software | kWh |
| `I` | Carbon intensity of the electricity mix (location- or market-based) | gCO₂eq / kWh |
| `M` | Embodied carbon — the share of hardware manufacturing emissions attributed to this software over the measurement window | gCO₂eq |
| `R` | Functional unit — the useful work delivered | e.g. per 1000 requests, per active user, per transaction, per build |

`E × I` = operational emissions. `M` = embodied (manufacturing) emissions. Dividing by `R` makes
the number comparable across time and scale.

**SCI is a rate, never a total.** Always report it as "X gCO₂eq per <functional unit>". `M` is
almost always an estimate — say so; the value's worth is that it forces you to define system
boundaries and attribute emissions to work.

## Procedure

Work through these steps for the current project. State every assumption; a transparent estimate
beats a false-precision number.

### 1. Understand the project and set the system boundary

Inspect the repository to classify it (frontend, backend/API, batch/CI, infra, ML/AI) and identify
the dominant workload. Then decide what is **in scope**: which processes, servers, client devices,
and time window count. Write the boundary down — it determines everything downstream.

### 2. Choose the functional unit `R`

Pick the unit that best represents the software's useful work, and match it to the project type:

- **Backend / API** → per 1000 requests, per transaction, per job.
- **Frontend / web** → per page view, per session, per active user.
- **Batch / CI/CD / ML training** → per pipeline run, per model trained, per GB processed.

Get the count from real telemetry if available (logs, APM, analytics, request metrics). Otherwise
estimate from expected load and label it an estimate.

### 3. Determine energy `E`

Prefer direct measurement; fall back to proxies:

- **Direct:** power-reading tools that read hardware registers — **Scaphandre**, **Kepler** (K8s),
  Intel RAPL, cloud provider energy/carbon dashboards (AWS/Azure/GCP).
- **Proxy (good enough to start):** CPU-time, container CPU/memory allocation × runtime, payload
  size, or instance power draw × utilization × time. Convert to kWh over the measurement window.

Measure the actual workload driving `R` in step 2, not idle background load — unless idle load is
part of the boundary (long-running services often make it significant).

### 4. Determine carbon intensity `I`

Use the electricity mix of the region where the software actually runs.

- Real-time / regional values: **Electricity Maps** (https://electricitymaps.com), cloud region
  carbon data, or national grid averages.
- If the region is unknown, use a documented grid-average default and state it (e.g. Frankfurt/DE
  ≈ 400 gCO₂/kWh as used in the reference example; EU average differs).

### 5. Estimate embodied carbon `M`

Attribute a share of hardware manufacturing emissions to this software over the window:

- Hardware manufacturing footprints: **Boavizta** (https://boavizta.org), vendor LCA datasheets,
  cloud provider embodied-carbon figures.
- Attribute by: `(device manufacturing CO₂ / expected lifetime) × (time & resource share used by
  this software)`. Longer hardware lifetime → lower `M` per unit (extending a server from 3 to 6
  years roughly halves its embodied share per year).
- For refurbished / already-amortized hardware (common in CI/test), `M` for new manufacturing is
  effectively zero — note it.

### 6. Compute and report

Plug in consistent units and compute `SCI = (E × I + M) / R`. Report:

- The **SCI value** with its functional unit (e.g. "2.2 gCO₂eq per 1000 requests").
- The **breakdown**: operational (`E × I`) vs embodied (`M`), and each input with its source.
- **System boundary, functional unit, and every assumption.**
- **Confidence** (measured vs proxy vs estimated), so the number can be trusted appropriately.

### 7. Recommend reductions and track them

Point to the largest contributor and propose concrete levers:

- High `E`: caching, algorithmic efficiency, fewer/lighter requests, right-sizing, scale-to-zero.
- High `I`: carbon-aware scheduling (shift batch load to low-intensity windows/regions), greener
  region selection.
- High `M`: extend hardware lifetime, use refurbished for non-prod, higher utilization density.

Establish measurement points **before and after** each change so the effect is provable. Improving
SCI means less carbon *per result* — not necessarily less absolute energy.

## Worked example

REST endpoint: 500,000 requests/day, 2 kWh/day, Frankfurt grid (I ≈ 400 gCO₂/kWh), embodied
M ≈ 0.3 gCO₂ per 1000 requests → per-day M = 300 g. With R in thousands of requests (R = 500):

```
SCI = (2 × 400 + 300) / 500 = 2.2 gCO₂eq per 1000 requests
```

After a caching change dropping energy to 0.8 kWh:

```
SCI = (0.8 × 400 + 300) / 500 = 1.24 gCO₂eq per 1000 requests   → 44% reduction
```

## Sources

- Green Software Foundation — *Software Carbon Intensity Specification* (ISO/IEC 21031:2024):
  https://greensoftware.foundation
- Electricity Maps — live carbon intensity by region: https://electricitymaps.com
- Boavizta — environmental impact of IT hardware: https://boavizta.org
- Scaphandre (process power via RAPL): https://github.com/hubblo-org/scaphandre — Kepler (K8s pod
  power via eBPF): https://sustainable-computing.io