---
description: >-
  Apply the Green IT "Algorithmic Efficiency" principle — every CPU cycle costs energy, so minimize
  and avoid computation, especially on high-frequency paths. Use when choosing an algorithm or data
  structure, profiling a hot path, rendering long lists, deciding SSR/SSG vs client rendering, adding
  caching, picking a runtime for CPU-bound work, or optimizing a query or render loop.
---

# Algorithmic Efficiency (minimize compute)

More computation → more watts → more kWh → more CO₂. Algorithm choice is an energy decision. The
impact scales with frequency: at 1,000 requests/day O(n) vs O(log n) is negligible; at 1,000,000 it
is decisive. Often more effective than tuning an algorithm is **not recomputing at all** — caching a
result that rarely changes instead of recalculating it every request.

## Frontend (keep the main thread free so the device can sleep)

- **Windowing / virtualized lists** — render only the ~10–20 visible rows, not thousands of DOM
  nodes; CPU returns to idle faster.
- **SSR / SSG** — render static content (docs, landing pages, catalogs) once at build instead of on
  every client; one build step replaces millions of device renders.
- **`requestIdleCallback` / Web Workers** — run non-urgent and heavy work off the critical path /
  off the main thread so rendering isn't blocked and the CPU can enter sleep states.

## Backend

- **Profile before optimizing** — `EXPLAIN ANALYZE` shows index use vs full-table scans; flame graphs
  reveal the real hot paths (rarely the intuitively-suspect ones). Without profiling you optimize the
  wrong thing.
- **Cache over recompute** — a stat aggregation recomputed for every one of 100k daily requests, when
  data barely changes hourly, is pure waste. Redis/Memcached answers in microseconds with no DB hit.
  Cache-hit ratios <80% signal TTL/invalidation needs rework.
- **Right runtime for CPU-bound jobs** — Node/Python are fine for I/O-bound APIs; for CPU-heavy work
  (image transforms, crypto, data analysis) interpreted languages are energy-inefficient. Run those
  in a dedicated worker in Rust/Go/Java. Keep the API in the fast-to-develop language.
- **Algorithm choice on hot paths** — linear search on 1M rows ≈ 1M comparisons; binary search on
  sorted data ≈ 20. Worth it on API endpoints, render loops, high-volume queries; rarely worth it on
  a once-daily batch.

## Infrastructure

- **CPU C-states** — servers often ship in high-performance BIOS profiles with sleep states disabled.
  Most web workloads are idle between requests; letting the CPU enter C-states drops idle draw to
  10–30% of full load, wake cost is a few ms. Free saving via a BIOS setting.
- **Containers over VMs** — containers share the host kernel: no guest OS, no hypervisor overhead.
- **Workload profiling** — many servers run at 5–15% average CPU; flame-graph profiling finds hot
  paths (inefficient ORM queries, unconfigured JSON serializers, missing indexes) fixable cheaply,
  saving on every request for the server's lifetime.

## Anti-patterns to flag

Recomputing rarely-changing results per request; full-table scans / missing indexes; rendering huge
lists into the DOM; CPU-bound work in an interpreted API server; optimizing before profiling.

## Quick wins

Cache a hot aggregation in Redis; add the missing index behind a full-table scan; virtualize a long
list; enable CPU C-states.

## Related

Measure before/after with [calculate-sci]. Caching also cuts transfer: [reduce-data-transfer].
Right-sizing/idle: [optimize-resource-usage]. Hardware-accelerated crypto / hot-data-in-RAM:
[hardware-aware-optimization].

## Sources

- R. Pereira et al. — Ranking Programming Languages by Energy Efficiency (2021): https://doi.org/10.1016/j.scico.2021.102609
- Green Software Foundation: https://greensoftware.foundation
