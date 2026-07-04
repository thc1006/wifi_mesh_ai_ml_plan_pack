# wifi_mesh_ai_ml_plan_pack

> WNC / Wi-Fi Mesh AI/ML Project Prompt + SDD/ADR Pack

This package is intended for starting the project from zero with an AI coding assistant while keeping the work academically rigorous and reviewable.

## What this pack contains

1. `00_source_grounding_and_assumptions.md` — source grounding, claims, unknowns, and cross-check notes.
2. `01_SDD_development_research_plan.md` — full software design/development research plan.
3. `02_ADR_0001_reference_architecture.md` — architecture decision record for the initial cloud-edge design.
4. `03_ADR_0002_tdd_small_cl_boy_scout_workflow.md` — development workflow ADR.
5. `04_prompt_playbook_copy_paste.md` — copy-paste prompts for each development stage.
6. `05_stage_validation_prompts.md` — copy-paste verification prompts for each stage.
7. `06_e2e_test_and_final_verification.md` — E2E and final validation prompts.
8. `07_adversarial_review.md` — failure modes, counterarguments, and reviewer checklist.
9. `08_acceptance_matrix.csv` — acceptance matrix for academic traceability.
10. `CLAUDE.md` — project-level AI coding instructions.
11. `.claude/skills/tdd-small-cl/SKILL.md` — optional reusable skill for TDD + small CLs.
12. `.claude/agents/*.md` — optional subagent prompts for research review and build validation.
13. `.github/PULL_REQUEST_TEMPLATE.md` — Small CL PR template.
14. `docs/*` — experiment log, risk register, glossary.

## How to use

Start by copying `CLAUDE.md` into the root of the real repository. Then use the prompts in `04_prompt_playbook_copy_paste.md` in order. Do not ask the AI assistant to implement everything at once. The workflow is designed around TDD, Boy Scout Rule, and Small CLs.

The most important rule: every stage must produce evidence, not just code. Evidence means failing test output, passing test output, command logs, generated artifacts, and a small diff summary.
