---
description: >-
  Apply the Green IT "Network Efficiency" principle — every transferred and every stored byte costs
  energy across server, network, and device. Use when handling images/media, JS bundles or imports,
  writing database queries or ORM code, designing APIs/response payloads, choosing a protocol
  (HTTP/1.1 vs HTTP/2 vs gRPC), setting up polling, or configuring compression, CDNs, caching,
  storage tiers, retention, or backups.
---

# Network Efficiency (reduce data volume & transfer)

Every byte over the network costs energy on the server, in routers/switches, and on the device —
mobile (LTE) far more per byte than Wi-Fi, which is more than wired. Every stored byte costs energy
continuously (disk controllers, cooling, replication, backups). The recurring question in every
layer: which data is actually needed, and which is moved or stored only because leaving it out
required a decision?

## Frontend (images and JS dominate page weight)

- **Modern image formats + responsive delivery** — images are 50–70% of page weight. AVIF is 50–80%
  smaller than JPEG at comparable quality (a 150 KB JPEG avatar → ~12 KB AVIF). Use `srcset`/`sizes`
  so a phone loads the displayed size, not a 4K image in a 300px column. CDNs can auto-convert.
- **Code splitting / lazy loading** — don't ship the whole bundle up front; load per route/interaction.
- **Bundle hygiene + tree-shaking** — a 75 KB lib pulled in for one date format is waste; use native
  APIs or lean alternatives, and ES-module imports so tree-shaking drops unused code (date-fns
  75 KB → ~3 KB). Chrome DevTools **Coverage** tab shows unused JS.
- **Page-weight budgets** — enforce a byte limit in the build so the next feature can't silently
  erase the gains.

## Backend

- **Avoid `SELECT *`** — select only needed columns; cuts DB work, payload, serialization at once.
- **Fix N+1 queries** — 200 objects + 200 follow-up queries = 201 instead of 1. Use JOINs or a
  DataLoader batch. Detect with ORM profilers / query logs.
- **Webhooks/events over polling** — polling floods the stack with "no change" requests that still
  cost auth, load-balancer, and DB work. Push on change; Message Queues decouple sender/receiver.
- **Protocol choice** — HTTP/2 multiplexes many requests over one persistent connection (no
  per-request TCP+TLS setup). For high-volume internal calls, gRPC + Protocol Buffers is 3–10× smaller
  than JSON.

## Infrastructure

- **CDNs + compression** — keep static assets regionally close; Brotli for text, zstd for binary.
- **Storage tiering** — lifecycle-tier by access frequency (SSD → object store → tape); cuts energy
  per stored GB by 10–100×. Idle tape libraries draw near-zero for rarely-read compliance data.
- **Incremental backups** — weekly full + daily incrementals transfers 80–90% fewer daily bytes than
  daily fulls, with equivalent recoverability.

## Anti-patterns to flag

`SELECT *`; ORM loops causing N+1; JPEG/PNG where AVIF/WebP fits; missing `srcset`; heavy libs for
trivial tasks; client polling for rarely-changing data; daily full backups; no compression/CDN.

## Quick wins

Convert hero/list images to AVIF + `srcset`; replace a `SELECT *`; add Brotli/zstd; add a JOIN to
kill an N+1; add a page-weight budget to CI.

## Related

Measure before/after with [calculate-sci]. Not-storing-at-all: [minimize-footprint]. Avoiding
recomputation via caching: [efficient-compute]. Polling wakeups on device: [optimize-resource-usage].

## Sources

- Google web.dev — Choose the right image format: https://web.dev/articles/choose-the-right-image-format
- The Green Web Foundation — CO2.js: https://www.thegreenwebfoundation.org/
- W3C — Web Sustainability Guidelines 1.0: https://www.w3.org/TR/sustainable-web-design/
