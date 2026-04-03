# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Purpose

This repository contains a family of 7 Claude Code Skills for IBM i (AS/400) enterprise development. The skills form a complete document-and-implementation chain — from raw requirement intake through specification, code generation, and review.

The repository contains no application code. All content is skill definitions (SKILL.md), reference documentation, and example outputs.

---

## Document Chain Architecture

The skills form a pipeline where each layer produces a distinct artifact type:

```
Raw Input → Requirement Normalizer → Functional Spec → Technical Design → Program Spec → Code
                                                                                         ↑
                                          Spec Reviewer (reviews any spec layer)          |
                                          Code Reviewer (reviews generated/written code) ──┘
```

| Skill | Purpose | Key Abstraction |
|-------|---------|-----------------|
| `ibm-i-requirement-normalizer` | Normalizes messy input into structured requirement package | Candidate items (CF-nn, CBR-nn, CE-nn) — not final spec entries |
| `ibm-i-functional-spec` | Business-functional document: current/future behavior, acceptance criteria | Business-visible behavior — no technical structure |
| `ibm-i-technical-design` | Design document: module allocation, processing stages, impact analysis | Design-level — no implementation steps |
| `ibm-i-program-spec` | Implementation spec: step-by-step logic, data/interface contracts, BR traceability | Implementation-level — developer handoff |
| `ibm-i-code-generator` | Generates RPGLE/CLLE source from Program Spec | Spec-driven code — Skeleton or Full Implementation modes |
| `ibm-i-spec-reviewer` | Reviews any spec document for quality, layer boundary, completeness | Quality gate — does not generate or rewrite |
| `ibm-i-code-reviewer` | Reviews code against Program Spec for correctness and enhancement safety | Quality gate — does not generate or rewrite |

### Layer Boundary Principle

Each skill must stay in its lane. The most common failure mode is layer collapse — a Functional Spec drifting into Technical Design content, or a Technical Design becoming a lighter Program Spec. Every skill includes explicit boundary rules and anti-pattern checks to prevent this.

### BR-xx Continuity

Business rules use the same BR-xx numbering across the entire chain. The expression changes per layer (business constraint → module allocation → implementation condition) but the number stays the same. The normalizer uses temporary CBR-nn numbering; BR-xx is finalized in the Functional Spec.

---

## File Structure

```
.claude/
├── ibm-i-requirement-normalizer/
│   └── SKILL.md
├── ibm-i-functional-spec/
│   └── SKILL.md
├── ibm-i-technical-design/
│   └── SKILL.md
├── ibm-i-program-spec/
│   ├── SKILL.md                    # Main skill definition
│   ├── section-guide.md            # Per-section content guidance
│   ├── tier-guide.md               # Level selection rules and decision examples
│   ├── v25-upgrade-analysis.md     # V2.5 release notes
│   ├── sample-rpgle-spec.md        # L3 Full example (RPGLE)
│   ├── sample-clle-spec.md         # L3 Full example (CLLE)
│   └── sample-lite-spec.md         # L1 Lite example
├── ibm-i-code-generator/
│   └── SKILL.md
├── ibm-i-spec-reviewer/
│   └── SKILL.md
└── ibm-i-code-reviewer/
    └── SKILL.md
```

Each skill's behavior is defined entirely in its SKILL.md. To change a skill's behavior, modify its SKILL.md.

The `ibm-i-program-spec` skill has the most supporting files because it was the first skill built (V2.5) and includes detailed section guides and examples. The other skills are self-contained in their SKILL.md.

---

## Shared Design Patterns Across All Skills

### Tiered Output (Spec Skills)

Functional Spec, Technical Design, and Program Spec all use L1 (Lite) / L2 (Standard) / L3 (Full) tiering based on complexity. The tiers change scope and depth, not quality. Each skill has its own Section Inclusion Table defining REQUIRED / CONDITIONAL / OPTIONAL / OMIT per level.

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

Both reviewers (spec-reviewer and code-reviewer) share these principles:

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

Generation skills (normalizer, functional-spec, technical-design, program-spec, code-generator) produce artifacts. Review skills (spec-reviewer, code-reviewer) assess artifacts. A generation skill must never silently become a reviewer, and a reviewer must never silently rewrite the artifact it is reviewing.
