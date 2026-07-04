# TDD Small CL Skill

Use this skill when implementing any code change in this repository.

## Process

1. Inspect files first.
2. State one Small CL objective.
3. Write tests first.
4. Run tests and capture expected failure.
5. Implement minimal general-purpose code.
6. Run tests and capture pass.
7. Refactor only with tests green.
8. Apply Boy Scout cleanup only in touched files.
9. Report evidence.

## Required output

```text
Small CL objective:
Tests added first:
Red output:
Implementation summary:
Green output:
Boy Scout cleanup:
Files changed:
Risks:
Next CL:
```

## Reject conditions

- Broad multi-stage implementation.
- No tests for behavior change.
- No evidence output.
- Unrelated refactor.
- Standards claims without source/code evidence.
