# 05 — Stage Validation Prompts

Use one of these after each stage. These are designed to make the AI assistant prove the work rather than merely claim success.

---

## Generic stage verification prompt

```text
<task>
Verify the stage that was just completed. Do not modify files unless needed to run verification scripts. Inspect the diff and evidence.
</task>

<checklist>
- Does this change satisfy exactly one Small CL objective?
- Were tests written or updated before implementation?
- Is there red test evidence when the change is behavior-changing?
- Are all relevant tests passing now?
- Are claims tied to files, commands, or logs?
- Is any Boy Scout cleanup limited to touched files and documented?
- Are there invented APIs, unverified standard claims, or hidden assumptions?
- Are unknowns captured as TODO/risk rather than silently ignored?
</checklist>

<output_format>
1. Verdict: PASS / PASS WITH RISKS / FAIL
2. Evidence reviewed
3. Requirement IDs satisfied
4. Missing evidence
5. Suspected overreach or mixed concerns
6. Required fixes before next stage
</output_format>
```

---

## TDD compliance verification prompt

```text
<task>
Audit the last change for TDD compliance.
</task>

<criteria>
- Test list existed before implementation.
- At least one new/changed behavior test failed for the expected reason before implementation.
- Implementation is general-purpose and not hard-coded to the test data.
- Tests passed after implementation.
- Refactor, if any, preserved passing tests.
</criteria>

<output_format>
- TDD verdict:
- Red evidence:
- Green evidence:
- Generality check:
- Refactor check:
- What would falsify this verdict:
</output_format>
```

---

## Small CL verification prompt

```text
<task>
Audit the last diff for Small CL compliance.
</task>

<criteria>
- One purpose only.
- Minimal blast radius.
- Includes tests/evidence.
- No unrelated formatting churn.
- No mixed feature + broad refactor.
- System remains working.
</criteria>

<output_format>
- Small CL verdict:
- Main purpose:
- Files changed and why:
- Lines/areas that look unrelated:
- Recommendation: merge / split / revert / request changes
</output_format>
```

---

## Standards-sensitive verification prompt

```text
<context>
This project involves TR-181, TR-369 USP, EasyMesh/prplMesh, and LCM/LXC-like behavior. We must avoid fabricated standards claims.
</context>

<task>
Review the last change for standards-sensitive claims. Check whether every standards-related claim is either:
1. backed by official documentation already in the repo,
2. backed by code behavior in the repo,
3. explicitly marked as an assumption or TODO.
</task>

<output_format>
- Standards claim verdict:
- Claims backed by evidence:
- Claims needing source:
- Claims that should be downgraded to assumption:
- Required edits:
</output_format>
```

---

## Hardware-readiness verification prompt

```text
<task>
Review whether the current stage is safe to run on AP hardware.
</task>

<criteria>
- Default mode is read-only.
- Write/config operations require explicit opt-in.
- There is a rollback or restoration note.
- The script captures logs and environment facts.
- Hardware failures are reported without masking.
</criteria>

<output_format>
- Hardware safety verdict:
- Safe read-only commands:
- Risky write commands:
- Required preflight checks:
- Rollback instructions:
- Evidence artifact path:
</output_format>
```
