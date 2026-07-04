# 04 — Copy-Paste Prompt Playbook

Use these prompts one stage at a time. Replace placeholders in `{braces}`.

---

## Global bootstrap prompt — use first

```text
<context>
I am starting a rigorous academic research prototype from zero.
Project: cloud-edge Wi-Fi AI/ML management for Multi-AP / Wi-Fi Mesh APs.
Architecture direction: Wi-Fi AI/ML -> USP Controller -> USP Agent/TR-181 custom data model -> AP-side ML Gateway/Collector/Streamer and Multi-AP/LCM adapters -> cloud metrics store.
Meeting-derived scope:
1. Cloud-edge ML data management.
2. ML-enabled Multi-AP management.
3. Anomaly resolver agent containerization and lifecycle management.
4. Integration testing.
Important design notes:
- Gateway data model exists conceptually, but Collector and Streamer need independent data models.
- Custom ML data should use an explicit data-plane transport such as TCP, not IEEE 1905.1, unless later evidence proves otherwise.
- Start with simulation/fake adapters, then root AP, then extender AP.
</context>

<rules>
- Follow TDD: list tests, write failing test first, implement minimal general-purpose code, run passing test, refactor if safe.
- Follow Small CLs: one behavior or one preparation task per change.
- Follow Boy Scout Rule: only small cleanup in touched files, and describe it separately.
- Do not claim official standard behavior unless grounded in official docs or existing code.
- Do not fabricate repository APIs. Inspect files first.
- If uncertain, mark as UNKNOWN and create a verification task.
</rules>

<task>
Inspect the current repository, identify what exists, and propose the first three Small CLs needed to bootstrap this project. Do not modify source code yet unless I explicitly approve. Create or update only planning documents if needed.
</task>

<output_format>
1. Repository facts found
2. Missing information
3. Proposed first three Small CLs
4. Recommended test strategy
5. Risks and verification tasks
6. Exact next prompt I should run
</output_format>
```

---

## Stage 0 development prompt — repository skeleton and CI

```text
<context>
We are at Stage 0: repository skeleton and CI. The goal is not feature implementation. The goal is to create a reproducible structure for research-grade development.
</context>

<task>
Create the minimal repository skeleton for this project:
- docs/requirements.md
- docs/architecture.md
- docs/experiment_log_template.md
- docs/adr/0001-reference-architecture.md
- tests/ with a placeholder smoke test
- scripts/ with a local validation script
- CI configuration if this repository already uses a CI system; otherwise create a local script only and document CI as TODO.
</task>

<rules>
- Small CL only.
- Do not implement project behavior yet.
- Add tests/validation that prove the skeleton is structurally valid.
- Keep the diff small and explain every file.
</rules>

<acceptance_criteria>
- A new developer can run one local command to validate the skeleton.
- Documentation states known assumptions and unknowns.
- No hardware-specific claims are made.
</acceptance_criteria>

<verification>
Run the local validation command and show output.
</verification>
```

---

## Stage 1 development prompt — requirements and acceptance matrix

```text
<context>
We are at Stage 1: requirements and acceptance matrix.
The project must map meeting-derived requirements to tests and evidence artifacts.
</context>

<task>
Create a machine-readable requirements and acceptance matrix. Include requirements for:
- Gateway/Collector/Streamer separate TR-181-style entities.
- ML collection setting and monitoring.
- Wi-Fi AI/ML configuration path through USP.
- Metrics/status readback.
- Custom TCP data path for ML data.
- Multi-AP intent abstraction for channel, TX power, credential, topology.
- LCM/container lifecycle for a generic anomaly resolver agent.
- Root AP then extender AP validation.
</task>

<rules>
- Do not implement behavior.
- Use precise IDs, such as REQ-DM-001.
- Every requirement must have at least one planned test or explicit blocker.
</rules>

<acceptance_criteria>
- Every requirement has: rationale, source, test strategy, evidence artifact, and risk level.
- Unknowns are explicitly marked.
</acceptance_criteria>

<verification>
Add a test or script that checks the matrix has no empty requirement IDs, no missing test strategy, and no missing evidence field.
Run it and show output.
</verification>
```

---

## Stage 2 development prompt — TR-181-style schema, tests first

```text
<context>
We are at Stage 2: custom TR-181-style data model schema.
We need Gateway, Collector, Streamer, CollectionService, MultiAPIntent, and AnomalyResolver model definitions.
</context>

<task>
Use TDD to add schema validation for vendor-extension-style data model definitions. Start with Gateway, Collector, and Streamer only in this CL.
</task>

<rules>
- Write failing tests first for valid and invalid schemas.
- Gateway, Collector, and Streamer must be separate entities.
- Do not implement USP transport yet.
- Do not hard-code only the visible test cases; implement a general validator.
</rules>

<acceptance_criteria>
- Valid schema with separate Gateway/Collector/Streamer passes.
- Missing required fields fail.
- Invalid enum values fail.
- Invalid object paths fail.
- Tests show red then green output.
</acceptance_criteria>

<verification>
Run the schema tests and local validation command. Show red output before implementation and green output after implementation.
</verification>
```

---

## Stage 3 development prompt — USP simulation harness

```text
<context>
We are at Stage 3: USP simulation harness.
Goal: prove that a cloud-side intent can be represented as a USP-like set/get flow against the custom model, without requiring hardware yet.
</context>

<task>
Create a minimal USP simulation harness or adapter interface. It must support testable Set/Get behavior for Gateway/Collector/Streamer config and status. Use fake/in-memory mode first. If the repo already integrates OB-USP-Agent or a test controller, inspect it and adapt, but do not invent APIs.
</task>

<rules>
- TDD first.
- Keep this as one Small CL.
- No real hardware assumptions.
- Clearly separate fake simulation from real USP integration.
</rules>

<acceptance_criteria>
- Test can set Collector.CollectionFrequencyMs and read it back.
- Test can enable Streamer and observe status transition.
- Invalid path returns a defined error.
- Evidence captures commands and test output.
</acceptance_criteria>
```

---

## Stage 4 development prompt — lifecycle adapters for Gateway/Collector/Streamer

```text
<context>
We are at Stage 4: AP-side service lifecycle and config adapters.
Gateway, Collector, and Streamer must support start, stop, configure, and status.
</context>

<task>
Using TDD, implement interfaces and fake adapters for Gateway/Collector/Streamer lifecycle. Connect these adapters to the Stage 3 USP simulation harness only through a narrow boundary.
</task>

<rules>
- One Small CL.
- Tests first.
- No real AP or container runtime yet.
- State machine must be deterministic.
</rules>

<acceptance_criteria>
- start/stop/configure/status are tested for each component.
- Invalid config fails safely.
- Status maps back to the data model.
- No unrelated refactor.
</acceptance_criteria>
```

---

## Stage 5 development prompt — ML data transport and metrics store

```text
<context>
We are at Stage 5: ML data transport and metrics-store contract.
The meeting suggested custom ML data should use TCP rather than IEEE 1905.1 for non-mesh-control data.
</context>

<task>
Using TDD, implement the smallest local data path:
Collector -> Streamer -> local metrics-store receiver.
Use a simple explicit transport contract. Start with localhost TCP or a fake transport if the language/runtime makes that more testable.
</task>

<rules>
- Do not use IEEE 1905.1 for arbitrary ML data.
- Keep transport contract versioned.
- Tests must include success and failure/retry/backoff behavior.
- Do not optimize throughput yet.
</rules>

<acceptance_criteria>
- A sample metric reaches the metrics store.
- Timestamp/source/interface fields are preserved.
- Streamer handles temporary receiver failure according to the defined backoff policy.
- Evidence includes logs or captured records.
</acceptance_criteria>
```

---

## Stage 6 development prompt — Multi-AP management abstraction

```text
<context>
We are at Stage 6: ML-enabled Multi-AP management abstraction.
We need an adapter boundary for channel, TX power, credential, and topology intent. Real prplMesh/hardware integration is not required in this CL.
</context>

<task>
Using TDD, define a MultiAPControlAdapter interface and mock implementation. Connect it to a MultiAPIntent data model object. Validate bounds and unsupported operations.
</task>

<rules>
- No direct hardware calls yet.
- Do not claim EasyMesh compliance.
- Keep all real prplMesh integration behind an interface.
</rules>

<acceptance_criteria>
- Valid channel/TX power intent maps to adapter call.
- Invalid values are rejected.
- Unsupported topology intent returns an explicit unsupported error.
- Tests are deterministic.
</acceptance_criteria>
```

---

## Stage 7 development prompt — LCM/container lifecycle generic agent

```text
<context>
We are at Stage 7: lifecycle management and generic anomaly agent.
The meeting says the anomaly resolver agent should first be a general program, containerized, deployed to root AP first, then extender AP.
</context>

<task>
Using TDD, implement a lifecycle adapter interface for a generic containerized agent. Start with a fake LCM/LXC adapter and a minimal generic agent package descriptor.
</task>

<rules>
- Root AP target first; extender AP target should be represented but not required to pass hardware tests yet.
- No real container runtime calls unless already available and isolated in tests.
- Idempotency must be tested for start/stop/status.
</rules>

<acceptance_criteria>
- deploy/start/stop/remove/status lifecycle is tested.
- TargetAPRole=root is supported.
- TargetAPRole=extender is marked planned or fake-only until hardware validation.
- Failures do not leave ambiguous state.
</acceptance_criteria>
```

---

## Stage 8 development prompt — anomaly resolver minimal behavior

```text
<context>
We are at Stage 8: minimal anomaly resolver behavior.
This is not a final ML claim. It is a controlled integration placeholder.
</context>

<task>
Using TDD, add a minimal rule-based anomaly resolver that ingests metrics and emits a recommendation object. It must not directly change Multi-AP settings unless an explicit actuation flag is enabled.
</task>

<rules>
- Separate detection, recommendation, and actuation.
- Do not claim ML performance.
- Make all thresholds configurable and testable.
</rules>

<acceptance_criteria>
- Normal metrics produce no anomaly.
- Threshold-crossing metrics produce a recommendation.
- Actuation is disabled by default.
- Tests prove recommendation can be read through the data model/status path.
</acceptance_criteria>
```

---

## Stage 9 development prompt — root AP smoke test script

```text
<context>
We are at Stage 9: root AP smoke test.
Simulation must now be compared against one real root AP if hardware is available.
</context>

<task>
Create a root AP smoke-test script and checklist. Do not make destructive changes. The script should collect environment facts, apply the safest possible read-only checks first, then optionally run a controlled config test only when explicitly enabled.
</task>

<rules>
- Hardware safety first.
- Default mode must be read-only.
- Any write/config operation must require an explicit flag.
- Capture logs for research evidence.
</rules>

<acceptance_criteria>
- Read-only smoke test runs without changing AP state.
- Write test is gated by explicit opt-in.
- Output includes timestamp, firmware/OS facts if available, service status, and test result.
</acceptance_criteria>
```

---

## Stage 10 development prompt — two-AP E2E scenario

```text
<context>
We are at Stage 10: root + extender E2E scenario.
Goal: prove cloud config -> USP/data model -> AP-side services -> metrics-store -> status readback.
</context>

<task>
Create an E2E test scenario that can run in fake mode and, if hardware variables are provided, hardware mode. Generate an evidence bundle with commands, logs, metrics-store records, and status readback.
</task>

<rules>
- Fake mode must be deterministic and CI-friendly.
- Hardware mode must be clearly separate.
- Do not hide failures; produce a failure report.
</rules>

<acceptance_criteria>
- Fake E2E passes in CI/local environment.
- Hardware E2E is either run successfully or blocked with explicit reason.
- Evidence bundle is generated in a stable directory.
</acceptance_criteria>
```

---

## Stage 11 development prompt — report/demo package

```text
<context>
We are at Stage 11: report/demo package.
The meeting asked for a Section 4.3-style demo architecture, scenario, screenshots/logs, and explanation.
</context>

<task>
Generate the report/demo package from actual evidence artifacts. Include architecture diagram source, demo script, exact commands, screenshots/log excerpts if available, and limitations.
</task>

<rules>
- Do not invent screenshots or results.
- If evidence is missing, mark it missing and create a TODO.
- Separate simulated evidence from hardware evidence.
</rules>

<acceptance_criteria>
- Another lab member can rerun the demo from the document.
- Every result claim links to an evidence artifact.
- Limitations and unknowns are explicit.
</acceptance_criteria>
```
