---
description: >-
  Apply the Green IT "Longevity" principle — embodied (manufacturing) carbon dominates a device's
  lifetime footprint, so extending hardware and software lifetime is one of the strongest levers. Use
  when setting performance budgets, targeting older/low-end devices, choosing framework/runtime
  versions (LTS vs bleeding edge), designing API payloads/versioning, building container images, or
  planning hardware refresh/procurement.
---

# Longevity (extend device & software lifetime)

Before a device is switched on, significant CO₂ is already emitted (mining, manufacturing, transport).
For end devices, embodied carbon typically exceeds the total operating emissions of 3–4 years of use;
for servers it exceeds the first 2–3 years. In the SCI formula embodied carbon `M` is divided by
lifetime — a laptop used 6 instead of 4 years has ~33% lower `M` per year with zero technical change.
Software drives premature replacement: an app that runs sluggishly on a 5-year-old device pushes a
hardware upgrade the device didn't need. Software-induced obsolescence is real but rarely explicit.

## Frontend

- **Performance budgets** — define a binding target on a defined reference device, e.g. Time to
  Interactive < 2s on a mid-range 2020 phone with 4× CPU throttling. That device represents the weakest
  realistic part of the user base; optimizing for it keeps older hardware usable. Enforce with
  **Lighthouse CI** in the build.
- **Progressive enhancement** — core function works as server-rendered HTML without JS; modern features
  are additive, not required. Keeps old devices/browsers productive.
- **Service workers / offline** — extend usability on poor connections and cut radio energy from
  constant reconnection attempts.

## Backend

- **LTS stacks** — forced upgrades (framework drops LTS, runtime requires newer OS) are often the real
  driver of infrastructure renewal, not hardware. A minimal stack on LTS versions runs 3–5 years stable
  without forced hardware upgrades.
- **Lightweight, long-versioned APIs** — massive JSON payloads, complex auth, and short deprecation
  windows effectively exclude older clients and force their early replacement. Lean REST endpoints,
  simple token auth, and long-term versioning keep older client hardware productive.
- **Slim container images** — Alpine / Distroless need less memory and CPU overhead, running well on
  older hardware.

## Infrastructure

- **Extend server lifetime** — industry swaps servers every 3–4 years on depreciation cycles, not
  need; modern servers run well beyond 6 years with proper maintenance. Going 3→6 years halves embodied
  carbon per year. Use proactive monitoring (SMART for disks, IPMI thermal alerts) for preventive vs
  reactive replacement. Targeted component upgrades (more RAM, NVMe over HDD) add 2–3 years cheaply.
- **Refurbished for non-prod** — test/CI/staging have no availability SLA and refurbished hardware's
  embodied carbon is already amortized from a prior context: each further year costs zero new
  manufacturing emissions.
- **IaC with clear resource profiles** — prevents creeping accumulation where instances grow over time
  because nobody downsized them.

## Anti-patterns to flag

No performance budget / no reference-device target; features that hard-require the latest JS; APIs with
bloated payloads and short deprecation windows; bleeding-edge non-LTS dependencies; depreciation-driven
hardware refresh; new hardware for non-prod where refurbished fits.

## Quick wins

Add a Lighthouse CI performance budget on a mid-range reference device; pin to LTS versions; slim a
container image to Alpine/Distroless; extend a server refresh cycle; buy refurbished for CI.

## Related

Measure `M` per year with [calculate-sci]. Keeping resource use low aids old devices:
[optimize-resource-usage] and [efficient-compute]. Not shipping heavy features: [minimize-footprint].

## Sources

- Boavizta — environmental impact of IT hardware: https://boavizta.org
- Green Software Foundation: https://greensoftware.foundation
