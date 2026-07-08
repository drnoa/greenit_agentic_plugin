---
description: >-
  Apply the Green IT "technological understanding" principle — software runs on physical hardware with
  properties you can exploit for free efficiency gains. Use when choosing UI colors / dark mode or CSS
  animations, selecting a CPU architecture (ARM vs x86) or instance type, configuring TLS/crypto
  libraries, deciding hot/warm/cold data placement (RAM vs disk), or tuning BIOS/cooling on
  self-managed hardware.
---

# Technological understanding (hardware-near optimization)

Software runs on concrete hardware with specific physical characteristics. Knowing them yields
efficiency gains without architectural change; ignoring them leaves free savings on the table.

## Frontend

- **OLED true black** — OLED pixels emit individually; a black pixel is physically off, a white one
  draws maximum. A true-black dark mode using `#000000` (not `#1a1a1a`) can cut display energy up to
  ~40% on OLED devices. Most modern phones are OLED; the change is a CSS variable, the impact scales
  to millions of devices.
- **Animate only `transform` and `opacity`** — animating `width`/`height`/`top`/`margin` triggers
  layout recalculation (the most expensive phase, on the CPU). `transform`/`opacity` skip layout and
  paint and run as GPU composition — faster and more energy-efficient. Rule: never animate
  layout-affecting properties.
- **Let the CPU sleep** — frequent timers, continuous animation, and short polling intervals prevent
  sleep states; an app that fires a request every 5s in the background is not really idle.

## Backend

- **ARM CPUs (Graviton, Ampere)** — designed for mobile where efficiency was mandatory; AWS Graviton 3
  delivers ~60% better performance-per-watt than comparable x86. All common runtimes support ARM64 and
  container images ship for both, so it's usually a pure infra decision — often the single most
  effective backend measure for x86-only shops.
- **Use CPU crypto/SIMD units** — AES-NI (every TLS connection), SHA extensions, AVX/NEON. OpenSSL with
  AES-NI handles TLS handshakes 5–10× faster at the same energy. Check your crypto lib uses hardware
  acceleration.
- **Hot data in RAM** — RAM access is orders of magnitude faster and cheaper than disk (even NVMe).
  Keep hot data (config, current rows, sessions) in Redis: microseconds and no disk-stack heat vs
  milliseconds hitting two systems. Separate hot/warm/cold by access pattern.

## Infrastructure

- **ARM instances** — ~60% better perf/W at up to ~20% lower price; ARM64 base images make migration
  code-free for most container workloads.
- **Cooling / PUE (on-prem)** — hot-aisle/cold-aisle containment can drop PUE from ~1.8 to ~1.3;
  raising supply air to 25–27 °C (ASHRAE A2) saves cooling energy — modern servers are rated for it;
  the common 18–20 °C is a historical relic.
- **BIOS/OS tuning** — enable CPU C-states (sleep on idle); dynamic fan control instead of static 100%
  saves cooling energy and extends fan life. Often unset at procurement despite measurable effect.

## Anti-patterns to flag

Dark mode using dark-grey instead of `#000000` on OLED targets; animating `width`/`top`/`margin`;
x86-only deployment where ARM fits; crypto lib without hardware acceleration; frequent disk reads of
hot data; disabled C-states; over-cooled data center.

## Quick wins

Switch dark-mode background to `#000000`; convert an animation to `transform`/`opacity`; move a
container workload to an ARM instance type; cache hot data in Redis; enable C-states.

## Related

Measure before/after with [calculate-sci]. Region grid mix pairs with hardware choice:
[carbon-aware-scheduling]. Caching hot data also cuts recompute: [efficient-compute].

## Sources

- AWS — EC2 Graviton processors: https://aws.amazon.com/ec2/graviton/
- Google/Android — Dark theme guidelines: https://developer.android.com/develop/ui/views/theming/darktheme
- IEA — Energy and AI (2025), on data-center PUE: https://www.iea.org/reports/energy-and-ai
