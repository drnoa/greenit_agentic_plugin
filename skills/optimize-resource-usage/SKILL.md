---
description: >-
  Apply the Green IT "Hardware Efficiency" principle — idle resources waste 30–50% of peak power for
  no work, so maximize utilization and avoid idle draw. Use when sizing cloud instances, configuring
  autoscaling or serverless/scale-to-zero, designing for load spikes (queues), reviewing memory leaks
  or event listeners, replacing polling with observers/push, or setting Kubernetes resource
  requests / bin-packing.
---

# Hardware Efficiency (optimize resource usage)

An idle server draws 30–50% of its peak power — mainboard, RAM, NIC, OS interrupts run regardless of
useful work. A server at 10% load consumes almost as much as at 80%, doing one-ninth the work. Base
load and embodied carbon are paid once, so the rule is: **maximize utilization** (one server at 80%
beats two at 40%) and run nothing when there's no work.

## Frontend (protect the user's device and battery)

- **Fix memory leaks** — remove event listeners on component teardown; don't grow global arrays
  forever. On 2–4 GB phones, growing memory forces the OS to swap and kill background processes,
  costing energy device-wide. Profile in the Chrome Memory panel.
- **Native observers over manual polling** — `IntersectionObserver` (visibility) and `ResizeObserver`
  batch checks and sync with the render cycle instead of firing a scroll/resize handler hundreds of
  times per second. Structurally cheaper.
- **Avoid client polling** — each interval wakes the CPU and fires a request (even when empty); the
  radio is especially power-hungry on connection setup. Prefer WebSockets / Server-Sent Events.

## Backend

- **Serverless / scale-to-zero** — no requests → zero instances → zero energy. Cold start
  (~100–500 ms) is negligible for CRUD/batch; account for it on latency-critical paths. A 14h/night
  idle internal API on a static instance wastes base-load energy every night.
- **Message queues to smooth spikes** — synchronous APIs must be over-provisioned for peaks or break.
  Queues decouple intake from processing: small, evenly-loaded workers instead of large instances idle
  80% of the time. Bonus: queued jobs can be prioritized by carbon intensity.
- **Right-size instances** — over-provisioning is the norm; instances are chosen generously and never
  downsized. AWS Compute Optimizer / Azure Advisor recommend smaller types from historical usage,
  usually with no performance loss.

## Infrastructure

- **Autoscaling** — scale instance count to real traffic (K8s HPA on CPU/RAM/custom metrics; KEDA for
  scale-to-zero on queue length). Typically saves 40–60% of compute energy.
- **Bin-packing + accurate resource requests** — maximize pack density on existing hardware; pods
  that reserve 10× what they use block node capacity. Right-size requests to actual need.
- **Prefer PaaS/serverless where in-house infra skill is thin** — shared platforms reach higher base
  utilization than self-managed IaaS instances at suboptimal load.

## Anti-patterns to flag

Static 24/7 instances for spiky/daytime-only traffic; instances at chronic <20% CPU; synchronous
processing sized for peak; K8s pods reserving far more than they use; client polling loops;
un-removed listeners / unbounded caches.

## Quick wins

Right-size an oversized instance; add scheduled or queue-based autoscaling; move a spiky sync task
behind a queue; move an idle-at-night API to serverless; fix a leaking listener.

## Related

Measure before/after with [calculate-sci]. Not running at all: [minimize-footprint]. Longer hardware
life reduces embodied carbon: [extend-longevity]. Prioritizing queued work by grid mix:
[carbon-aware-scheduling]. Finding the biggest idle waste first: [find-green-levers].

## Sources

- AWS — Compute Optimizer / Graviton: https://aws.amazon.com/ec2/graviton/
- Green Software Foundation: https://greensoftware.foundation
