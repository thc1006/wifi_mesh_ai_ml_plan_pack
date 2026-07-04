# CLAUDE.md — Project Instructions

You are assisting with an academic research prototype for cloud-edge Wi-Fi AI/ML management over USP/TR-181 and Multi-AP/EasyMesh-like AP environments.

## Non-negotiable rules

1. Do not implement broad changes. Propose one Small CL at a time.
2. Use TDD for behavior changes: test list → failing test → minimal implementation → passing test → refactor.
3. Keep management plane and data plane separate.
4. Treat TR-181/TR-369/USP/EasyMesh/prplMesh/prplLCM details as standards-sensitive. Do not invent API behavior.
5. If a standard, repo, hardware feature, or vendor convention is uncertain, mark it as unknown and create a verification task.
6. Do not claim hardware support unless verified on real hardware or from official vendor documentation.
7. Apply Boy Scout cleanup only in touched files and describe it separately.
8. Avoid hard-coded solutions that only satisfy visible tests.
9. Every completed stage must produce evidence: commands run, test output, diff summary, limitations, and next risk.

## Project architecture assumptions

- Cloud Wi-Fi AI/ML sends management intent.
- USP Controller/Test Controller sends operations to USP Agent/Data Model adapter.
- TR-181 vendor-extension-style custom objects represent Gateway, Collector, Streamer, MultiAPIntent, and AnomalyResolver state.
- Gateway, Collector, and Streamer must be separate model entities.
- Custom ML data transport should use TCP or another explicit data-plane transport, not IEEE 1905.1, unless a later ADR proves otherwise.
- Start with fake/simulation adapters, then root AP, then extender AP.

## Required response format for implementation tasks

Use this format:

```text
Facts found:
- ...

Proposed Small CL:
- ...

Tests to add or update first:
- ...

Commands I will run:
- ...

Risk / unknowns:
- ...
```

After implementation, report:

```text
Evidence:
- Red test output: ...
- Green test output: ...
- Commands run: ...
- Files changed: ...
- Boy Scout cleanup: ...
- Remaining risks: ...
```

## Definition of done

A task is done only when:

- Acceptance criteria are satisfied.
- Tests pass locally.
- Evidence is recorded.
- No unrelated large change is included.
- The assistant can explain how the change advances the SDD/ADR plan.
