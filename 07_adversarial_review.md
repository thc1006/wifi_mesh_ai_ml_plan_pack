# 07 — Adversarial Review and Failure Modes

## Main counterarguments

### Counterargument 1: The custom TR-181 model may not match production vendor conventions

This is likely. The first implementation should use a clearly marked vendor-extension namespace and avoid claiming compatibility until reviewed by the team or vendor.

Mitigation:

- Keep model definitions versioned.
- Keep a mapping layer between internal config objects and external TR-181 paths.
- Create an ADR when the final namespace is chosen.

### Counterargument 2: Simulation may not predict real AP behavior

This is true, especially for Wi-Fi and embedded AP platforms. Simulation validates logic and integration boundaries, not RF behavior or firmware constraints.

Mitigation:

- Separate fake-mode, root-AP, and extender-AP evidence.
- Do not make performance or real-world Wi-Fi claims from fake tests.
- Add smoke tests before any destructive hardware test.

### Counterargument 3: TCP for custom ML data may be too naive

TCP is a reasonable first prototype because the meeting distinguished ML data transport from IEEE 1905.1 mesh control. However, production may require MQTT, WebSocket, gRPC, USP events, or another channel.

Mitigation:

- Put transport behind an interface.
- Version the message contract.
- Benchmark and compare alternatives later.

### Counterargument 4: AI coding may create plausible but wrong standards integrations

Very likely unless the workflow forces investigation, tests, and source-grounded claims.

Mitigation:

- Use standards-sensitive verification prompt after every stage touching TR-181/USP/EasyMesh/LCM.
- Require source links or code references for every standards claim.
- Prefer official docs and reference implementations.

### Counterargument 5: Anomaly resolver may become a fake ML demo

If the system only implements a rule-based resolver, it must not claim ML anomaly detection performance.

Mitigation:

- Label Stage 8 as integration placeholder.
- Separate detection, recommendation, and actuation.
- Add real ML experiments later only with dataset, baseline, metrics, and statistical analysis.

## Failure modes checklist

| Area | Failure mode | Detection | Mitigation |
|---|---|---|---|
| Data model | Gateway/Collector/Streamer remain coupled | Schema tests | Separate model tests and interfaces |
| USP | Fake harness does not resemble real USP flows | Standards review | Later OB-USP-Agent/Test Controller integration |
| Data plane | Test only verifies local function calls | E2E evidence | Require metrics-store record evidence |
| Multi-AP | Mock adapter hides unsupported real operations | Hardware smoke test | Keep unsupported explicit |
| LCM | Fake lifecycle not portable to LXC/prplLCM | Adapter contract review | Add real adapter as separate CL |
| AI coding | Hard-coded test passing | TDD audit | Generality check prompt |
| Review | CLs too large | PR template | Split before merge |
| Experiment | Missing logs | Evidence policy | Every stage writes artifact bundle |

## What would change the architecture

- Official vendor docs require a different TR-181 namespace or parameter structure.
- prplMesh exposes suitable APIs that make part of the custom Multi-AP adapter unnecessary.
- USP events/notifications are a better fit than explicit polling for monitoring.
- Hardware cannot support LXC/prplLCM on target APs.
- Metrics latency/volume makes simple TCP insufficient.

## Minimum reviewer standard

A reviewer should reject the milestone if:

- There is no E2E evidence bundle.
- Simulation results are presented as hardware results.
- Tests do not cover data model separation.
- Any standards-sensitive claim lacks source or code evidence.
- The final report hides known unknowns.
