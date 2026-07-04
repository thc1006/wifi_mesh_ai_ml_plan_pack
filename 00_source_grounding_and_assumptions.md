# 00 — Source Grounding and Assumptions

## Meeting-derived scope

The meeting defines four second-year workstreams:

1. Cloud-edge ML data management.
2. ML-enabled Multi-AP management.
3. Anomaly resolver agent containerization and lifecycle management.
4. Integration testing.

The technical details discussed in the meeting include:

- A custom TR-181 data model for ML data collection service setting and monitoring.
- Configuration exposed to Wi-Fi AI/ML, including lifecycle control, network connection, collection frequency, service-module metrics, and data collection status.
- Custom TR-181 data models for Multi-AP Wi-Fi and Mesh management, including channel, TX power, credential, and topology.
- Current implementation has a gateway data model but lacks independent streamer and collector data models.
- The meeting suggests separating Gateway, Streamer, and Collector into separate data models.
- For custom data passing between Multi-AP Controller and Multi-AP Agent, the meeting leans toward TCP rather than IEEE 1905.1 because IEEE 1905.1 should be reserved for Mesh / Multi-AP control semantics.
- Demo target: cloud-side configuration through USP, then Gateway/Collector/Streamer behavior, with data reaching a cloud metrics store.

## Standards and implementation grounding

As of the 2026-07-02 web check:

- TR-369 USP Issue 1 Amendment 5 is dated January 2026.
- TR-181 Device Data Model Issue 2 Amendment 21 is dated June 2026.
- Broadband Forum positions USP as a protocol for managing, monitoring, updating, and controlling connected devices and home networks.
- OB-USP-Agent is the Broadband Forum reference implementation for a USP Agent.
- OB-USP-Agent Test Controller can send USP controller messages from files and is useful for repeatable validation.
- prplMesh is an open-source carrier-grade implementation of Wi-Fi Alliance EasyMesh with Agent and Controller functionality.
- prpl LCM is positioned as app/service lifecycle management for prplOS/RDK-B-like CPE environments.

## Prompting / AI coding grounding

The prompt design in this pack follows these source-backed practices:

- State task, constraints, success criteria, and output format explicitly.
- Use structured XML-like sections for context, task, constraints, acceptance criteria, and verification.
- Ask the coding assistant to investigate first, then plan, then modify.
- Use tests as the feedback loop.
- Ask the assistant to self-check against criteria before claiming completion.
- Keep project memory concise and specific in `CLAUDE.md`.

## Engineering rules integrated

### TDD Rule

For every behavior-changing stage:

1. List the tests that should exist.
2. Write a failing test first and capture the failure.
3. Implement the smallest general-purpose code that makes the test pass.
4. Refactor while preserving passing tests.

A test passing because the implementation hard-codes the test case is not acceptable.

### Boy Scout Rule

Each CL may include small cleanup in touched files only, but the cleanup must be directly relevant and must not mix unrelated refactoring with feature work.

### Small CLs

Each CL must do one thing, include its tests, preserve a working system, and provide enough context for review.

## Explicit unknowns

- The exact in-house meaning of `MWA` remains unverified from public sources and transcript alone.
- The exact in-house meaning of `PWHM` remains unverified from public sources and transcript alone.
- The actual repository structure, target AP hardware, prplOS version, and existing code are not available in this pack.
- Exact production TR-181 vendor-extension naming must be confirmed against team/company conventions before finalization.

## References

# References

This pack was prepared from the corrected meeting transcript and current public documentation checked on 2026-07-02.

## Project / meeting grounding
- Corrected meeting transcript: propos_corrected_transcript(1).md, provided by the user.
- Scope extracted from meeting: second-year work = cloud-edge ML data management, ML-enabled Multi-AP management, anomaly resolver containerization/lifecycle management, and integration testing.
- Important design note from meeting: current gateway data model exists; streamer and collector need independent data models; custom data between Multi-AP Controller and Agent should likely use TCP rather than IEEE 1905.1 because IEEE 1905.1 is for mesh control.

## Official / primary technical sources
- Broadband Forum TR-369 USP PDF, Issue 1 Amendment 5, January 2026: https://www.broadband-forum.org/pdfs/tr-369-1-5-0.pdf
- Broadband Forum Device Data Model TR-181 page, Issue 2 Amendment 21, June 2026: https://device-data-model.broadband-forum.org/
- User Services Platform resources: https://usp.technology/resources/
- OB-USP-Agent reference implementation: https://github.com/BroadbandForum/obuspa
- OB-USP-Agent Test Controller: https://github.com/BroadbandForum/obuspa-test-controller
- prplMesh: https://prplfoundation.org/prplmesh/
- prpl LCM announcement: https://prplfoundation.org/prpl-foundation-enables-open-source-app-store-concept-for-residential-cpe-based-on-rdk-b-and-prplos/
- Linux Containers project: https://linuxcontainers.org/

## AI coding / prompting sources
- Claude Code common workflows: https://code.claude.com/docs/en/common-workflows
- Claude Code memory / CLAUDE.md: https://code.claude.com/docs/en/memory
- Claude Code power user tips: https://support.claude.com/en/articles/14554000-claude-code-power-user-tips
- Anthropic prompt engineering best practices: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- Anthropic success criteria and evaluations: https://platform.claude.com/docs/en/test-and-evaluate/develop-tests

## Software engineering practice sources
- Martin Fowler, TestDrivenDevelopment: https://martinfowler.com/bliki/TestDrivenDevelopment.html
- Google Engineering Practices, Small CLs: https://google.github.io/eng-practices/review/developer/small-cls.html
- Google Engineering Practices, Code Review Standard: https://google.github.io/eng-practices/review/reviewer/standard.html
- Robert C. Martin, Boy Scout Rule excerpt: https://www.oreilly.com/library/view/97-things-every/9780596809515/ch08.html
