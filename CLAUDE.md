# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Purpose

This repository contains a family of 14 Claude Code Skills for IBM i (AS/400) enterprise development. The skills form a complete document-and-implementation chain — from raw requirement intake through specification, code generation, review, and unit test planning — plus a file definition skill for DDS-based file objects and a workflow orchestrator for routing work through the chain.

The repository contains no application code. All content is skill definitions (SKILL.md), reference documentation, and example outputs.

---

## Document Chain Architecture

The skills form a pipeline where each layer produces a distinct artifact type:

```
Raw Input → Requirement Normalizer → Functional Spec → Technical Design ──→ Program Spec → Code
                                  ↗                           │                 │            ↑
   Existing Source + CR → Impact Analyzer                     └──→ File Spec → DDS Source    |
                                                                              │              |
                                                                 UT Plan Generator (any spec or CR input)
                                          Spec Reviewer (reviews any spec layer)             |
                                          DDS Reviewer (reviews generated/written DDS source)|
                                          Code Reviewer (reviews generated/written code) ────┘
                                          Workflow Orchestrator (routes work through the chain)
```

| Skill | Purpose | Key Abstraction |
|-------|---------|-----------------|
| `ibm-i-requirement-normalizer` | Normalizes messy input into structured requirement package | Candidate items (CF-nn, CBR-nn, CE-nn) — not final spec entries |
| `ibm-i-impact-analyzer` | Analyzes existing source + CR to produce impact analysis | Source-level structural analysis — entry point for enhancement work |
| `ibm-i-functional-spec` | Business-functional document: current/future behavior, acceptance criteria | Business-visible behavior — no technical structure |
| `ibm-i-technical-design` | Design document: module allocation, processing stages, impact analysis | Design-level — no implementation steps |
| `ibm-i-program-spec` | Implementation spec: step-by-step logic, data/interface contracts, BR traceability | Implementation-level — developer handoff |
| `ibm-i-file-spec` | File definition spec: PF, LF, PRTF, DSPF field layouts, keys, screen/print layouts | DDS-level — file object definition |
| `ibm-i-code-generator` | Generates RPGLE/CLLE source from Program Spec | Spec-driven code — Skeleton or Full Implementation modes |
| `ibm-i-dds-generator` | Generates DDS source from File Spec JSON | Spec-driven DDS — PF, LF, PRTF, DSPF (V2.2) |
| `ibm-i-ut-plan-generator` | Generates Unit Test Plans from specs, CRs, or raw input | Developer-level UT cases — does not execute tests |
| `ibm-i-compile-precheck` | Pre-compile review of RPGLE/CLLE for compile-safety issues | Quality gate — after code generation, before compile |
| `ibm-i-spec-reviewer` | Reviews any spec document for quality, layer boundary, completeness | Quality gate — does not generate or rewrite |
| `ibm-i-dds-reviewer` | Reviews DDS source against File Spec for correctness, syntax, completeness | Quality gate — does not generate or rewrite |
| `ibm-i-code-reviewer` | Reviews code against Program Spec for correctness and enhancement safety | Quality gate — does not generate or rewrite |
| `ibm-i-workflow-orchestrator` | Routes work to the correct skill in the correct order | Orchestration — does not replace generation or review skills |

### Layer Boundary Principle

Each skill must stay in its lane. The most common failure mode is layer collapse — a Functional Spec drifting into Technical Design content, or a Technical Design becoming a lighter Program Spec. Every skill includes explicit boundary rules and anti-pattern checks to prevent this.

### BR-xx Continuity

Business rules use the same BR-xx numbering across the entire chain. The expression changes per layer (business constraint → module allocation → implementation condition) but the number stays the same. The normalizer uses temporary CBR-nn numbering; BR-xx is finalized in the Functional Spec.

---

## File Structure

```
.claude/
├── ibm-i-requirement-normalizer/
│   ├── SKILL.md
│   └── examples/sample-normalization.md
├── ibm-i-functional-spec/
│   ├── SKILL.md
│   ├── references/section-guide.md
│   ├── references/tier-guide.md
│   └── examples/                   # sample-lite, sample-new-function, sample-enhancement
├── ibm-i-technical-design/
│   ├── SKILL.md
│   ├── references/section-guide.md
│   ├── references/tier-guide.md
│   └── examples/                   # sample-lite-design, sample-rpgle-design, sample-enhancement-design
├── ibm-i-program-spec/
│   ├── SKILL.md                    # V2.5
│   ├── section-guide.md
│   ├── tier-guide.md
│   ├── v25-upgrade-analysis.md
│   ├── sample-rpgle-spec.md
│   ├── sample-clle-spec.md
│   └── sample-lite-spec.md
├── ibm-i-file-spec/
│   ├── SKILL.md                    # V2.1.2
│   ├── references/                 # section-guide, tier-guide, json-schema, interop-model, validation-rules
│   └── examples/                   # sample-pf-spec, sample-lf-spec, sample-dspf-spec, sample-prtf-spec, sample-pf-enhancement-spec
├── ibm-i-impact-analyzer/
│   └── SKILL.md                    # V1.2 — pre-spec change impact analysis
├── ibm-i-dds-generator/
│   ├── SKILL.md                    # V2.2 — PF, LF, PRTF, DSPF
│   ├── examples/                   # sample-pf, sample-lf-simple, sample-lf-join, sample-dspf, sample-prtf, sample-pf-version
│   └── tests/                      # runner.sh + 31 test cases (tc-01 to tc-31)
├── ibm-i-code-generator/
│   ├── SKILL.md                    # V1.0
│   ├── references/                 # rpgle-format-policy, change-output-modes, clle-enhancement-patterns
│   ├── examples/                   # sample-rpgle-new-free, sample-rpgle-setll-reade-loop, sample-rpgle-embedded-sql, + 3 more
│   └── tests/                      # runner.sh + 8 test cases (tc-cg-01 to tc-cg-08, 3 layers)
├── ibm-i-ut-plan-generator/
│   └── SKILL.md                    # V1.2 — unit test plans from specs, CRs, or raw input
├── ibm-i-compile-precheck/
│   ├── SKILL.md                    # V1.0 — pre-compile safety review
│   └── references/                 # fixed-format-checklists (CL1-CL6)
├── ibm-i-dds-reviewer/
│   └── SKILL.md                    # V1.2 — reviews DDS source against File Spec
├── ibm-i-spec-reviewer/
│   ├── SKILL.md                    # V1.1
│   └── examples/sample-spec-review.md
├── ibm-i-code-reviewer/
│   ├── SKILL.md                    # V1.0
│   ├── references/                 # review-checkpoints, rpgle-review-policy, enhancement-review-patterns
│   └── examples/                   # sample-review-br-coverage-gap, + 4 more
└── ibm-i-workflow-orchestrator/
    └── SKILL.md                    # V1.1 — routes work through the skill chain
```

Each skill's behavior is defined entirely in its SKILL.md. To change a skill's behavior, modify its SKILL.md.

Most skills now include supporting `references/` (guides and policies) and `examples/` (sample outputs). The DDS generator and code generator also include `tests/` directories with semi-automated test runners.

---

## Shared Design Patterns Across All Skills

### Tiered Output (Spec Skills)

Functional Spec, Technical Design, Program Spec, and File Spec all use L1 (Lite) / L2 (Standard) / L3 (Full) tiering based on complexity. The tiers change scope and depth, not quality. Each skill has its own Section Inclusion Table defining REQUIRED / CONDITIONAL / OPTIONAL / OMIT per level.

### Safe Draft Rule

All skills prefer generating a structured draft with TBD markers and (Inferred) labels over refusing to produce output. Clarifying questions are asked only when the input is too vague to establish even a safe scope.

### Enhancement Tagging

All spec skills use consistent tagging for change work:
- **(NEW)** — introduced by this change
- **(MODIFIED)** — existing, changed
- **(EXISTING — context only)** — unchanged, included only when needed for clarity

Signal over noise: EXISTING entries are included only when they are direct dependencies of changed items.

### Anti-Hallucination Discipline

No skill may invent object names, business rules, user roles, or system details. Unknowns are marked `TBD (To Be Confirmed)`. Inferences are labeled `(Inferred)` and tracked in Open Questions.

---

## Code Generator Specifics

The code generator has unique characteristics not shared by the spec skills:

- **Spec-First Rule**: generates code only from a Program Spec, not from raw requirements or upstream documents
- **Skeleton vs Full Implementation**: automatically downgrades to Skeleton when the spec has unresolved TBDs
- **Delta-First Enhancement**: defaults to minimal change blocks for enhancements, not full-member regeneration
- **RPGLE Format Policy**: new programs → free format; existing → fixed format; mixed → preserve original style
- **Readiness Levels**: compile-shaped scaffold / compile-ready draft / production-safe implementation

---

## Reviewer Specifics

All three reviewers (spec-reviewer, dds-reviewer, and code-reviewer) share these principles:

- **Review-only**: never generate, rewrite, or replace the reviewed artifact
- **Evidence-based findings**: every finding references a specific location with severity and category
- **Fix path judgment**: Patch / Revise upstream / Regenerate / Needs clarification
- **Readiness decision**: Ready / Ready with minor fixes / Needs revision / Not ready
- **Unsupported vs False**: absence from the spec is a risk signal, not proof of error

---

## Working With This Repository

- **Modifying a skill's behavior** → edit its `.claude/<skill-name>/SKILL.md`
- **Adding a new skill** → create `.claude/<skill-name>/SKILL.md` following the family patterns
- **Checking layer boundaries** → use `ibm-i-spec-reviewer` on any spec document
- **Checking code quality** → use `ibm-i-code-reviewer` on generated or written source
- **Understanding the Program Spec's section structure** → read `.claude/ibm-i-program-spec/section-guide.md`
- **Understanding tier selection** → read `.claude/ibm-i-program-spec/tier-guide.md`

---

## Skill Generation and Review Are Separate Concerns

Generation skills (normalizer, impact-analyzer, functional-spec, technical-design, program-spec, file-spec, code-generator, dds-generator, ut-plan-generator) produce artifacts. Review skills (spec-reviewer, dds-reviewer, compile-precheck, code-reviewer) assess artifacts. The workflow orchestrator routes work through the chain but does not replace any generation or review skill. A generation skill must never silently become a reviewer, and a reviewer must never silently rewrite the artifact it is reviewing.
