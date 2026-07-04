# Risk Register

| ID | Risk | Likelihood | Impact | Detection | Mitigation | Status |
|---|---|---:|---:|---|---|---|
| R-001 | Custom TR-181 model mismatches vendor convention | High | High | Vendor review | Keep mapping layer and ADR | Open |
| R-002 | Simulation diverges from AP hardware | High | High | Root AP smoke test | Separate fake/hardware evidence | Open |
| R-003 | AI assistant fabricates APIs | Medium | High | Standards-sensitive review | Inspect first, source required | Open |
| R-004 | IEEE 1905.1 misused for ML data | Medium | Medium | Architecture review | Data-plane transport interface | Open |
| R-005 | LCM unavailable on target AP | Medium | High | Hardware preflight | Fake adapter first; real adapter later | Open |
| R-006 | E2E demo not reproducible | Medium | High | Re-run by another member | Evidence bundle and exact commands | Open |
