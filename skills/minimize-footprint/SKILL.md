---
description: >-
  Apply the Green IT "Less is more" principle — the most effective energy measure is not building,
  storing, or running something in the first place. Use when adding a feature, deciding what data to
  persist, reviewing scope, wiring up a web font or third-party widget, cutting an interaction flow,
  setting log levels, choosing service granularity, or deciding whether an environment/service needs
  to run 24/7.
---

# Less is more (Minimalism)

The most effective energy measure is the decision to leave something out. What is not built consumes
no operational energy; what is not stored is not indexed, replicated, backed up, or cooled; what does
not run consumes zero. This decision is rarely technical — it is usually about product scope and
priorities. Feature creep is the core problem: a feature added once keeps running (and costing 100%
of its operating cost) even when <5% of users touch it.

## Frontend

- **Feature-gating** — put resource-intensive features behind an opt-in / eco-toggle / load-on-demand
  instead of running them always. Autoplay video, continuous animation, third-party chat bubbles run
  even when nobody interacts: pure waste. Gated features cost zero CPU/network on devices that skip them.
- **System fonts over web fonts** — each web font family = several render-blocking HTTP requests plus
  CPU to decode. Drop custom fonts unless they add measurable brand value.
- **Shorter interaction paths** — active time ∝ energy (display, CPU, radio all run). Fewer clicks,
  fewer form fields, more direct navigation → shorter sessions → less energy, no lost function.

## Backend

- **Data minimization** — don't store fields "just in case". Every stored field grows indexes,
  backups, migrations, replication. Data-protection law and Green IT point the same way.
- **Service granularity** — over-fine microservices create a communication mesh: each call crosses
  the network with TLS handshake, serialization, load-balancer hop, and each service carries its own
  runtime/memory idle cost. Two always-co-deployed services are often better as one module.
- **Scale-to-zero for intermittent work** — a monthly 4-hour billing run inside a 24/7 monolith
  causes round-the-clock base load; as a scale-to-zero service it runs 4 hours, then nothing.
- **Log-level policy** — DEBUG only in dev; WARN/ERROR in prod. Per-request debug logging at high
  traffic is a measurable, cumulative load (CPU to format/write, bandwidth, log-system resources).

## Infrastructure

- **Lightswitch ops** — test/staging that runs 24/7 but is used only in office hours wastes ~70% of
  its uptime. Auto start/stop on schedule or on events (e.g. PR creation) turns a 24/7 cost into 9-to-5.
- **Storage lifecycle** — stored bytes cost energy forever (controllers, cooling, replication,
  backups). Lifecycle policies auto-tier to colder classes or auto-delete after defined periods.
- **Service consolidation** — three services that together never exceed 20% CPU can share one host
  via container orchestration without losing deployment independence.

## Anti-patterns to flag

- A dashboard/feature used by a tiny fraction of users on a dedicated always-on VM.
- Fields persisted for hypothetical future features.
- DEBUG logging in production.
- Non-prod environments with no start/stop schedule.

## Quick wins

Opt-in a rarely-used feature and stop its VM overnight; switch to system fonts; add a log-level
policy; add lifecycle/auto-delete policies to logs and buckets.

## Related

Measure before/after with [calculate-sci]. Structural waste discovery: [find-green-levers]. Idle
capacity: [optimize-resource-usage]. Storage/transfer volume: [reduce-data-transfer].

## Sources

- W3C — Web Sustainability Guidelines 1.0: https://www.w3.org/TR/sustainable-web-design/
- Green Software Foundation: https://greensoftware.foundation
