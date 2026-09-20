---
name: service-observability
description: Reliability and observability conventions for any service that runs as a long-lived server (APIs, web services, background daemons, etc.). Always implement liveness and readiness probes, and instrument the service with metrics using OpenTelemetry (OTEL), exported to an OTEL collector or Prometheus. Always apply this whenever building, scaffolding, or modifying a service that runs continuously as a server/daemon rather than a one-shot CLI.
---

# Service Observability

Applies to any long-running server process (typically alongside `go-conventions` and `go-web-interfaces`).

## Health probes

- Implement separate **liveness** and **readiness** endpoints/checks for every server-type service:
  - Liveness: is the process alive and not deadlocked/stuck (should it be restarted)?
  - Readiness: is the service currently able to serve traffic (dependencies up, warmed up, etc.)?
- Wire these into whatever orchestrator is in play (e.g. Kubernetes `livenessProbe`/`readinessProbe`) when relevant.

## Metrics

- Instrument the service with metrics using **OpenTelemetry (OTEL)**.
- Export metrics to an **OTEL collector** or directly to **Prometheus**, depending on what the surrounding infrastructure already uses — ask the user if it's not established by the project yet.
- Cover the basics by default (request counts/latencies for servers, error rates) and add domain-specific metrics as the service's logic warrants.
