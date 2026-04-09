# AGENTS.md

This file provides guidance to Codex and other AI agents when working with this repository.

---

## Repository Purpose

This repository contains a family of 15 Claude Code Skills for IBM i (AS/400) enterprise
development. The skills form two complete delivery pipelines — a **Program Chain** for
RPGLE/CLLE code and a **File Chain** for DDS source — plus program analysis, spec review,
DDS review, code review, unit test planning, and workflow orchestration.

The repository contains no application code. All content is skill definitions (SKILL.md),
reference documentation, example outputs, and test harnesses.

---

## Skill Chain Architecture

```
Raw Input → Requirement Normalizer → Functional Spec → Technical Design
                                  ↗                           │
   Existing Source → Program Analyzer ──→ Impact Analyzer     ┌────────────────┤
                                  (+ CR)  ↗                   │                │
                                                       Program Chain:    File Chain:
                                                       Program Spec      File Spec
                                                           ↓                 ↓
                                                       Code Generator    DDS Generator
                                                           ↓                 ↓
                                                       Code Reviewer     DDS Reviewer

        UT Plan Generator ─── produces unit test plans from any spec/CR/raw input
        Spec Reviewer ──── reviews any spec artifact
        Workflow Orchestrator ──── routes work through the chain
```

---

## All 15 Skills

### Generation & Analysis Skills (10)

| Skill | Path | Version | What It Produces |
|-------|------|---------|-----------------|
| `ibm-i-requirement-normalizer` | `.claude/ibm-i-requirement-normalizer/` | V1.0 | Structured requirement package from messy input |
| `ibm-i-program-analyzer` | `.claude/ibm-i-program-analyzer/` | V1.0 | Program logic analysis with call flow diagrams |
| `ibm-i-impact-analyzer` | `.claude/ibm-i-impact-analyzer/` | V1.2 | Impact analysis of existing source + CR |
| `ibm-i-functional-spec` | `.claude/ibm-i-functional-spec/` | V1.0 | Business-functional document |
| `ibm-i-technical-design` | `.claude/ibm-i-technical-design/` | V1.0 | Design document with module allocation |
| `ibm-i-program-spec` | `.claude/ibm-i-program-spec/` | V2.5 | Implementation spec with step-by-step logic |
| `ibm-i-file-spec` | `.claude/ibm-i-file-spec/` | V2.1.2 | File definition spec (PF/LF/PRTF/DSPF) with dual-layer output |
| `ibm-i-code-generator` | `.claude/ibm-i-code-generator/` | V1.0 | RPGLE or CLLE source from Program Spec |
| `ibm-i-dds-generator` | `.claude/ibm-i-dds-generator/` | V2.2 | DDS source from File Spec JSON (PF/LF/PRTF/DSPF) |
| `ibm-i-ut-plan-generator` | `.claude/ibm-i-ut-plan-generator/` | V1.2 | Unit test plan from specs, CRs, or raw input |

### Review Skills (4)

| Skill | Path | Version | What It Reviews |
|-------|------|---------|----------------|
| `ibm-i-compile-precheck` | `.claude/ibm-i-compile-precheck/` | V1.0 | RPGLE/CLLE source — compile safety, opcode patterns, KLIST, aliases |
| `ibm-i-spec-reviewer` | `.claude/ibm-i-spec-reviewer/` | V1.1 | Any spec document — layer boundary, completeness, File Spec rules |
| `ibm-i-dds-reviewer` | `.claude/ibm-i-dds-reviewer/` | V1.2 | DDS source against File Spec — syntax, completeness, type-specific rules |
| `ibm-i-code-reviewer` | `.claude/ibm-i-code-reviewer/` | V1.0 | RPGLE/CLLE against Program Spec — correctness, enhancement safety |

### Orchestration (1)

| Skill | Path | Version | Purpose |
|-------|------|---------|---------|
| `ibm-i-workflow-orchestrator` | `.claude/ibm-i-workflow-orchestrator/` | V1.1 | Routes work to correct skill in correct order |

---

## File Structure

All skills live under `.claude/` (not `.Codex/`). Each skill's behavior is defined in its `SKILL.md`.

```
.claude/
├── ibm-i-requirement-normalizer/    # V1.0 + examples
├── ibm-i-functional-spec/           # V1.0 + references, examples
├── ibm-i-technical-design/          # V1.0 + references, examples
├── ibm-i-program-spec/              # V2.5 + section-guide, tier-guide, samples
├── ibm-i-file-spec/                 # V2.1.2 + references (5), examples (5)
├── ibm-i-program-analyzer/          # V1.0 — source logic comprehension and call flow
├── ibm-i-impact-analyzer/           # V1.2 — pre-spec change impact analysis
├── ibm-i-dds-generator/             # V2.2 + examples (6), tests (31 cases)
├── ibm-i-code-generator/            # V1.0 + references (3), examples (6), tests (8 cases)
├── ibm-i-ut-plan-generator/         # V1.2 — unit test plan from specs, CRs, raw input
├── ibm-i-compile-precheck/          # V1.0 — pre-compile safety review + checklists
├── ibm-i-dds-reviewer/              # V1.2
├── ibm-i-spec-reviewer/             # V1.1 + examples
├── ibm-i-code-reviewer/             # V1.0 + references (3), examples (5)
└── ibm-i-workflow-orchestrator/     # V1.1
```

### Skills with Test Harnesses

| Skill | Test Location | Test Count | Runner |
|-------|-------------|-----------|--------|
| `ibm-i-dds-generator` | `.claude/ibm-i-dds-generator/tests/` | 31 cases | `runner.sh` — structural checks on generated DDS |
| `ibm-i-code-generator` | `.claude/ibm-i-code-generator/tests/` | 8 cases (3 layers) | `runner.sh` — L1 structural, L2 pipeline, L3 enhancement |

---

## Key Design Principles

- **Layer boundary discipline** — each skill stays in its lane; layer collapse is the primary failure mode
- **BR-xx continuity** — business rules carry the same BR-xx number across the entire chain
- **Enhancement-first** — optimized for IBM i BAU change work, not only greenfield
- **Anti-hallucination** — no skill invents object names, business rules, or system details
- **Tiered output** — L1 (Lite) / L2 (Standard) / L3 (Full) based on complexity
- **Dual-layer File Spec** — human-readable Markdown + machine-readable JSON
- **Cross-spec interoperability** — File Spec IDs (`fileSpecRef`, `fieldRef`) link to Program Spec

---

## Cross-Spec Reference Model

Program Spec and File Spec are peer artifacts. When File Specs exist:
- Program Spec's File Usage can include `File Spec Ref` column linking to the File Spec ID
- Program Spec's Data Contract can include `File Spec Ref` column with `<specId>:<fieldId>` references
- See `.claude/ibm-i-file-spec/references/interop-model.md` for the complete reference scheme

---

## Quick Reference: Which Skill to Use

| If you have... | And want... | Use... |
|----------------|-------------|--------|
| Messy request | Structured starting point | `ibm-i-requirement-normalizer` |
| Existing source (no CR) | Understand / analyze program | `ibm-i-program-analyzer` |
| Existing source + CR | Impact analysis | `ibm-i-impact-analyzer` |
| Requirement | Business scope | `ibm-i-functional-spec` |
| Functional Spec | Technical approach | `ibm-i-technical-design` |
| Technical Design | Implementation logic | `ibm-i-program-spec` |
| Technical Design | File definitions | `ibm-i-file-spec` |
| File Spec JSON | DDS source code | `ibm-i-dds-generator` |
| Program Spec | RPGLE/CLLE source | `ibm-i-code-generator` |
| Any spec or CR | Unit test plan | `ibm-i-ut-plan-generator` |
| Generated RPGLE/CLLE | Compile safety check | `ibm-i-compile-precheck` |
| Any spec | Quality check | `ibm-i-spec-reviewer` |
| DDS source | Validation | `ibm-i-dds-reviewer` |
| RPGLE/CLLE source | Validation | `ibm-i-code-reviewer` |
| Any stage | What to do next | `ibm-i-workflow-orchestrator` |
