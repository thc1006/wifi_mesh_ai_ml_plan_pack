# 01 — Software Design / Development Research Plan

## Title

Cloud-Edge ML Data Management and ML-enabled Multi-AP Control over USP/TR-181 for Wi-Fi Mesh APs

## Purpose

This plan defines a rigorous, test-first development strategy for implementing a cloud-edge Wi-Fi AI/ML management path for Multi-AP / EasyMesh environments. The goal is not only to produce a working prototype, but to produce a research-grade artifact with traceable requirements, repeatable tests, small reviewable changes, and falsifiable evidence.

## Research objective

Build and evaluate a prototype in which a cloud-side Wi-Fi AI/ML application can configure and monitor ML data collection components on APs through USP/TR-181-style data models, while preserving clean separation between management-plane control and ML data-plane transport.

## Scope

### In scope

- Repository skeleton and reproducible developer workflow.
- Requirements and architecture documentation.
- Custom vendor-extension style TR-181 model design for:
  - MLDataGateway
  - MLDataCollector
  - MLDataStreamer
  - MLDataCollectionService status and metrics
- USP simulation harness using an available controller/agent or a fake interface when hardware is unavailable.
- Local adapters for Gateway, Collector, and Streamer lifecycle/configuration.
- TCP-based custom transport prototype for ML data/control not covered by IEEE 1905.1 mesh-control semantics.
- Metrics-store ingestion contract and local test implementation.
- Multi-AP management abstraction for channel, TX power, credential, and topology.
- Containerization and lifecycle prototype for a generic agent that evolves into an anomaly resolver agent.
- Root AP first, extender AP second, then two-AP E2E validation.
- Academic experiment logging and adversarial review.

### Out of scope for the first iteration

- Claiming full Wi-Fi Alliance EasyMesh compliance.
- Production firmware integration without hardware vendor confirmation.
- Final ML anomaly detection quality claims.
- Security certification.
- Real customer deployment.

## Architectural hypothesis

The system should separate four layers:

1. Cloud management intent: Wi-Fi AI/ML decides configuration and observes metrics.
2. USP/TR-181 management plane: exposes configuration/status data models.
3. AP service plane: Gateway/Collector/Streamer and lifecycle manager apply configuration.
4. ML data plane: collector/streamer sends samples and telemetry to cloud metrics store, using TCP or another explicit non-IEEE-1905.1 transport for custom data.

This separation is important because IEEE 1905.1/EasyMesh is appropriate for Multi-AP topology and mesh-control semantics, while arbitrary ML data transport is a separate concern.

## Proposed system components

### Cloud side

- `wifi-ai-ml-orchestrator`: sends configuration intents and reads metrics/status.
- `usp-controller-adapter`: speaks to USP Controller or test controller.
- `metrics-store`: receives and indexes sample metrics for tests.
- `experiment-runner`: executes reproducible E2E scenarios.

### AP side

- `usp-agent-adapter`: exposes or maps TR-181 custom objects.
- `ml-data-gateway`: central AP-side service for local coordination.
- `ml-data-collector`: collects Wi-Fi/AP/service metrics.
- `ml-data-streamer`: streams samples to cloud metrics store.
- `multi-ap-control-adapter`: abstracts channel, TX power, credential, and topology operations.
- `lifecycle-manager-adapter`: abstracts LXC/prplLCM-like lifecycle operations.
- `anomaly-resolver-agent`: initially a generic containerized service, later anomaly logic.

## Data model design direction

Use vendor-extension style names until the team confirms final TR-181 path conventions. A candidate namespace is:

```text
Device.X_NYCU_WNC.WiFiML.
Device.X_NYCU_WNC.WiFiML.Gateway.
Device.X_NYCU_WNC.WiFiML.Collector.{i}.
Device.X_NYCU_WNC.WiFiML.Streamer.{i}.
Device.X_NYCU_WNC.WiFiML.CollectionService.{i}.
Device.X_NYCU_WNC.WiFiML.AnomalyResolver.{i}.
Device.X_NYCU_WNC.WiFiML.MultiAPIntent.{i}.
```

Minimum first-pass parameters:

```text
Gateway.Enable
Gateway.Status
Gateway.LastError
Collector.{i}.Enable
Collector.{i}.Status
Collector.{i}.CollectionFrequencyMs
Collector.{i}.SourceInterface
Collector.{i}.LastSampleTimestamp
Collector.{i}.LastError
Streamer.{i}.Enable
Streamer.{i}.Status
Streamer.{i}.EndpointURI
Streamer.{i}.BackoffPolicy
Streamer.{i}.LastUploadTimestamp
Streamer.{i}.LastError
CollectionService.{i}.Enable
CollectionService.{i}.Status
CollectionService.{i}.Metrics.{i}.Name
CollectionService.{i}.Metrics.{i}.Value
CollectionService.{i}.Metrics.{i}.Timestamp
AnomalyResolver.{i}.Enable
AnomalyResolver.{i}.ImageRef
AnomalyResolver.{i}.ContainerStatus
AnomalyResolver.{i}.TargetAPRole
MultiAPIntent.{i}.Channel
MultiAPIntent.{i}.TxPower
MultiAPIntent.{i}.CredentialProfile
MultiAPIntent.{i}.TopologyIntent
```

## Development stages

### Stage 0 — Repository skeleton and research guardrails

Deliverables:

- `README.md`
- `CLAUDE.md`
- `docs/requirements.md`
- `docs/architecture.md`
- `docs/experiment_log.md`
- CI with formatting, linting, unit tests, and artifact capture.

Acceptance:

- CI runs locally.
- No product code behavior exists yet.
- Documentation states assumptions and unknowns.

### Stage 1 — Requirements and acceptance matrix

Deliverables:

- Machine-readable requirements file.
- Acceptance matrix mapping requirement → test → evidence artifact.

Acceptance:

- Every requirement has a test strategy.
- Unknowns are explicitly marked as unknown rather than guessed.

### Stage 2 — TR-181 custom data model schema

Deliverables:

- Versioned schema files for Gateway, Collector, Streamer, CollectionService, MultiAPIntent, AnomalyResolver.
- Parser/validator tests.

Acceptance:

- Tests reject invalid object paths, invalid enum values, invalid units, and missing required fields.
- Gateway/Collector/Streamer are separate model entities.

### Stage 3 — USP simulation harness

Deliverables:

- Local fake USP path or integration with OB-USP-Agent/Test Controller.
- Repeatable command files or scripts for Set/Get/Operate-like flows.

Acceptance:

- Test can apply config to fake/device model and observe expected status.
- Failure conditions are captured.

### Stage 4 — Gateway/Collector/Streamer lifecycle and config adapters

Deliverables:

- Interfaces and implementations for start/stop/configure/status.
- Tests for lifecycle transitions.

Acceptance:

- Lifecycle state machine is deterministic.
- Invalid config fails safely.

### Stage 5 — ML data transport and metrics-store contract

Deliverables:

- TCP or equivalent explicit custom data transport prototype.
- Metrics-store local test receiver.
- Serialization contract.

Acceptance:

- Collector → Streamer → metrics-store path works locally.
- Backoff/retry behavior is tested.
- IEEE 1905.1 is not used for arbitrary ML data.

### Stage 6 — Multi-AP management abstraction

Deliverables:

- Interface for channel, TX power, credential, topology intent.
- Mock implementation first; prplMesh/hardware implementation later.

Acceptance:

- AI/ML intent is translated into a bounded adapter call.
- Unsafe or unsupported values are rejected.

### Stage 7 — Container lifecycle and generic anomaly agent

Deliverables:

- Generic containerized agent.
- Lifecycle operations: deploy, start, stop, update, remove, status.
- Local LXC/prplLCM-like adapter or fake adapter.

Acceptance:

- Lifecycle operations are idempotent where appropriate.
- Root AP target is tested before extender AP target.

### Stage 8 — Anomaly resolver integration

Deliverables:

- Minimal anomaly-resolver behavior: ingest metrics, detect trivial rule-based anomaly, emit proposed action.
- No ML quality claim yet.

Acceptance:

- The resolver action is observable and reversible.
- Tests distinguish between detection, recommendation, and actuation.

### Stage 9 — Root AP smoke test

Deliverables:

- Hardware smoke-test script.
- Captured logs.

Acceptance:

- One AP can receive configuration and report status.
- Test can be rerun after reboot.

### Stage 10 — Root + extender E2E test

Deliverables:

- Two-AP scenario.
- Cloud config → AP service config → metrics flow → status readback.

Acceptance:

- E2E evidence bundle generated.
- Failure mode report included.

### Stage 11 — Report/demo package

Deliverables:

- Section 4.3 report text.
- Architecture diagram source.
- Demo script.
- Screenshots/log excerpts.

Acceptance:

- Another lab member can rerun the demo using documented steps.

## Evidence policy

Every stage must produce:

- CL/commit ID.
- Objective.
- Test list.
- Red test output when applicable.
- Green test output.
- Refactor summary.
- Boy Scout cleanup summary or explicit “none”.
- Risk/unknown update.

## Metrics for the academic experiment

- Time per stage.
- Number of prompts per stage.
- Number of AI-introduced defects found by tests/review.
- Number of review iterations.
- Diff size per CL.
- Test coverage of core requirements.
- Reproducibility rate by another developer.
- Hardware vs simulation divergence.

## Threats to validity

- Simulation may not match real Wi-Fi RF behavior.
- AI coding assistant may fabricate APIs or model paths.
- Tests may encode assumptions rather than standard behavior.
- Hardware/vendor platform may differ from open-source references.
- Custom TR-181 vendor extensions may not map cleanly to production naming.
- Small CL discipline may slow initial prototyping but improves reviewability.

## Exit criteria

The project reaches a defensible first research milestone when:

- Custom Gateway/Collector/Streamer data model split is implemented and validated.
- USP simulation or test-controller path can set config and read status.
- Collector/Streamer can deliver test metrics to a cloud metrics store.
- Root AP smoke test is complete or a documented blocker proves why it is not possible.
- E2E demo has reproducible evidence.
- ADRs document every major architectural decision and alternative rejected.
