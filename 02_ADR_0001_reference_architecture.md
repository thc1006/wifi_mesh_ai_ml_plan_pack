# 02 — ADR-0001: Reference Architecture for Cloud-Edge Wi-Fi AI/ML Management

## Status

Proposed

## Context

The project needs to let a cloud-side Wi-Fi AI/ML component configure and monitor AP-side ML data collection services and eventually control Multi-AP / EasyMesh-related behavior. The meeting scope includes ML data management, ML-enabled Multi-AP management, anomaly resolver lifecycle management, and integration testing.

The standards landscape suggests using USP/TR-181 for device management and data models. Multi-AP topology/control should align with EasyMesh/prplMesh concepts. Custom ML data transport should not be forced into IEEE 1905.1 if it is not mesh-control data.

## Decision

Adopt a layered architecture:

```text
Cloud Wi-Fi AI/ML
  -> USP Controller / Test Controller
    -> USP Agent / Data Model Adapter
      -> ML Gateway / Collector / Streamer / LCM Adapter
        -> Metrics Store + Multi-AP Control Adapter + Anomaly Resolver Container
```

Separate management plane from data plane:

- Management plane: USP/TR-181 custom objects.
- Multi-AP mesh control plane: EasyMesh/prplMesh/IEEE 1905.1-compatible behavior where appropriate.
- ML data plane: explicit TCP/HTTP/gRPC-like transport prototype, starting with TCP or a simple local equivalent.

## Consequences

Positive:

- Easier to test with fake adapters.
- Avoids abusing IEEE 1905.1 for arbitrary ML data.
- Supports root AP first, extender AP later.
- Produces clearer research evidence.

Negative:

- Requires adapter layer and more test scaffolding.
- Custom TR-181 extension may need rework after vendor review.
- Simulation may hide firmware/platform constraints.

## Alternatives considered

### Put all behavior directly in the gateway data model

Rejected for first serious implementation because the meeting identified a need to split Gateway, Collector, and Streamer data models.

### Use IEEE 1905.1 for custom ML data

Rejected for first implementation because the meeting already distinguishes custom data transport from mesh-control semantics.

### Build only on real hardware from day one

Rejected because the research needs repeatable CI and staged validation before hardware availability is guaranteed.

## Validation

- Data model schema tests prove separate Gateway/Collector/Streamer entities.
- USP simulation proves configuration/readback path.
- E2E test proves cloud config can alter collection/streaming behavior and produce metrics.
- Hardware smoke test compares simulation with root AP behavior.
