# 06 — E2E Test and Final Verification Prompts

## E2E implementation prompt

```text
<context>
We need a reproducible E2E test for the core research claim:
Cloud-side Wi-Fi AI/ML configuration can travel through a USP/TR-181-style management plane to AP-side ML data services, and the resulting Gateway/Collector/Streamer behavior can be observed through metrics-store ingestion and status readback.
</context>

<scenario>
1. Start metrics-store test receiver.
2. Start fake or real USP Agent/data-model adapter.
3. Apply config:
   - Gateway.Enable = true
   - Collector.Enable = true
   - Collector.CollectionFrequencyMs = {frequency_ms}
   - Streamer.Enable = true
   - Streamer.EndpointURI = {metrics_store_endpoint}
4. Confirm status readback.
5. Generate or collect one sample metric.
6. Confirm the metric arrives at metrics store.
7. Read service status and last sample/upload timestamps.
8. Stop services cleanly.
9. Produce evidence bundle.
</scenario>

<task>
Create or update the E2E test runner for this scenario. It must support fake mode. Hardware mode is optional and must be gated by explicit variables/flags.
</task>

<rules>
- Do not invent successful hardware results.
- Fake mode must be deterministic.
- Evidence bundle must include config input, command output, received metric records, status readback, and test verdict.
</rules>

<acceptance_criteria>
- Fake E2E passes locally.
- Failure mode produces a readable failure report.
- Evidence bundle path is printed.
</acceptance_criteria>
```

## E2E verification prompt

```text
<task>
Run and audit the E2E test. Do not modify code unless the test runner itself is broken and I approve a fix.
</task>

<checklist>
- Was the metrics-store receiver started?
- Was configuration applied through the intended management boundary?
- Did Gateway/Collector/Streamer status change as expected?
- Did at least one metric arrive with correct source/timestamp fields?
- Is the test deterministic in fake mode?
- Are hardware and fake results separated?
- Is every claim backed by evidence in the bundle?
</checklist>

<output_format>
1. E2E verdict
2. Commands run
3. Evidence bundle path
4. Passed checks
5. Failed or skipped checks
6. Interpretation for research report
7. Next falsification test
</output_format>
```

## Final whole-project adversarial verification prompt

```text
<role>
Act as a strict but fair systems research reviewer. Your goal is to find overclaims, missing evidence, invalid assumptions, and non-reproducible results.
</role>

<context>
The project claims to prototype cloud-edge ML data management and ML-enabled Multi-AP management using USP/TR-181-style data models, AP-side Gateway/Collector/Streamer services, a metrics store, and lifecycle management for an anomaly resolver agent.
</context>

<task>
Perform a final adversarial review of the repository and evidence bundle. Do not fix code. Produce a review report.
</task>

<review_dimensions>
- Requirements traceability
- Standards grounding
- TDD compliance
- Small CL compliance
- Boy Scout cleanup discipline
- E2E reproducibility
- Simulation vs hardware gap
- Security/safety risks
- ML/anomaly resolver overclaims
- Data model maintainability
- Demo/report honesty
</review_dimensions>

<output_format>
# Final Review Verdict
PASS / PASS WITH MAJOR LIMITATIONS / FAIL

# Strongest Supported Claims
- ...

# Unsupported or Overstated Claims
- ...

# Evidence Table
| Claim | Evidence | Confidence | What would falsify it |

# Blocking Issues
- ...

# Non-blocking Issues
- ...

# Required Next Experiments
- ...

# Reviewer Recommendation
- ...
</output_format>
```

## Final release/readiness prompt

```text
<task>
Prepare a release-readiness summary for the current research milestone.
</task>

<required_sections>
- What works
- What is simulated only
- What is hardware-verified
- What is not implemented
- What failed and why
- Reproduction steps
- Evidence artifact index
- Known risks
- Next Small CLs
</required_sections>

<rules>
- Do not hide limitations.
- Do not present simulation as hardware validation.
- Use exact command outputs and artifact paths.
</rules>
```
