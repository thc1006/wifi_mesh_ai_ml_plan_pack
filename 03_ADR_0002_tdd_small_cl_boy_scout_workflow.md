# 03 — ADR-0002: TDD + Boy Scout + Small CL Workflow

## Status

Accepted for this research plan

## Context

The project will use an AI coding assistant. That increases speed but also increases risk of hidden assumptions, overbroad changes, fabricated APIs, and test-shaped implementations. The workflow must produce evidence suitable for academic research and code review.

## Decision

All implementation work must follow this cycle:

```text
1. Inspect existing files and summarize facts.
2. Propose one Small CL only.
3. Write or update tests first.
4. Run tests and capture failing output.
5. Implement minimal general-purpose code.
6. Run tests and capture passing output.
7. Refactor only if tests remain green.
8. Apply Boy Scout cleanup only in touched files.
9. Produce CL summary and evidence bundle.
```

## Small CL definition

A CL is acceptable when it:

- Solves exactly one requirement or one preparatory task.
- Includes tests or documented reason tests are not applicable.
- Keeps the system working.
- Is small enough for another developer to review in one sitting.
- Does not mix unrelated refactor, formatting, and behavior changes.

## Boy Scout Rule definition for this project

A CL may include small cleanup if:

- The cleanup is in files already touched by the CL.
- The cleanup is directly relevant to the current work.
- The cleanup is separately described in the CL summary.
- The cleanup does not change unrelated behavior.

## TDD Rule definition for this project

For behavior changes, the assistant must show:

- Test list before implementation.
- Failing test output before implementation.
- Minimal implementation.
- Passing test output.
- Refactor justification, if any.

## Anti-patterns

- “Implement all stages” in one prompt.
- Passing tests without showing the red step.
- Hard-coding test fixture values.
- Large cleanups mixed with a feature.
- Claims of standards compliance without standard-backed evidence.
- Hardware claims based only on simulation.

## Validation

Every PR must fill `.github/PULL_REQUEST_TEMPLATE.md`. The reviewer must reject PRs with missing evidence.
