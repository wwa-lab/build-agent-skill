# IBM i Agent Skill Family

A complete Claude Code skill family for IBM i (AS/400) enterprise development — from raw requirement intake through specification, code generation, and review.

## Document Chain

```
Raw Input → Requirement Normalizer → Functional Spec → Technical Design → Program Spec → Code
                                                                                         ↑
                                          Spec Reviewer (reviews any spec layer)          |
                                          Code Reviewer (reviews generated/written code) ──┘
```

## Skills

### Generation Skills

| Skill | What It Produces | Audience |
|-------|-----------------|----------|
| **ibm-i-requirement-normalizer** | Structured requirement package from messy input (emails, tickets, notes) | BA, project lead |
| **ibm-i-functional-spec** | Business-functional document: current/future behavior, business rules, acceptance criteria | Business stakeholders |
| **ibm-i-technical-design** | Design document: module allocation, processing stages, object interaction, impact analysis | Solution architects |
| **ibm-i-program-spec** | Implementation spec: step-by-step logic, data/interface contracts, BR traceability | Developers, testers |
| **ibm-i-code-generator** | RPGLE or CLLE source code from a Program Spec (Skeleton or Full Implementation) | Developers |

### Review Skills

| Skill | What It Reviews | Gate |
|-------|----------------|------|
| **ibm-i-spec-reviewer** | Any spec document — checks layer boundary, completeness, traceability, downstream readiness | Spec quality gate |
| **ibm-i-code-reviewer** | RPGLE/CLLE source against Program Spec — checks correctness, enhancement safety, format policy | Code quality gate |

## Key Design Principles

**Layer boundary discipline** — Each skill stays in its lane. A Functional Spec describes business behavior, not module allocation. A Technical Design describes design structure, not implementation steps. Layer collapse is the primary failure mode these skills are designed to prevent.

**BR-xx continuity** — Business rules use the same BR-xx numbering across the entire chain. The expression changes per layer (business constraint → module allocation → implementation condition) but the number carries forward.

**Enhancement-first** — All skills are optimized for IBM i BAU enhancement work, not only greenfield projects. Enhancement tagging (NEW / MODIFIED / EXISTING), delta-first code generation, and partial-input safe drafting reflect real enterprise environments.

**Anti-hallucination** — No skill invents object names, business rules, or system details. Unknowns are marked TBD. Inferences are labeled and tracked.

**Tiered output** — Functional Spec, Technical Design, and Program Spec use L1 (Lite) / L2 (Standard) / L3 (Full) levels based on complexity. Tiers change scope, not quality.

## Installation

Copy the `.claude/` directory into your project. Each skill is self-contained in its `SKILL.md`:

```
.claude/
├── ibm-i-requirement-normalizer/SKILL.md
├── ibm-i-functional-spec/SKILL.md
├── ibm-i-technical-design/SKILL.md
├── ibm-i-program-spec/SKILL.md          # + section-guide, tier-guide, samples
├── ibm-i-code-generator/SKILL.md
├── ibm-i-spec-reviewer/SKILL.md
└── ibm-i-code-reviewer/SKILL.md
```

## Usage

Skills trigger automatically based on context. You can also invoke them by name:

```
# Normalize a messy requirement
"Normalize this change request for IBM i: [paste raw input]"

# Generate a functional spec
"Write a functional spec for this IBM i enhancement: [requirement]"

# Generate a technical design
"Create a technical design for this IBM i change: [requirement]"

# Generate a program spec
"Write a program spec for this RPGLE program: [requirement]"

# Generate code from a spec
"Implement this Program Spec in RPGLE"

# Review a spec
"Review this Technical Design for layer boundary and completeness"

# Review code
"Review this RPGLE against the Program Spec"
```

## Recommended Workflow

1. **Normalize** raw input if messy or incomplete
2. **Functional Spec** → business review → scope approval
3. **Technical Design** → design review → design approval
4. **Program Spec** → build review → implementation readiness
5. **Code Generation** → code review → build handoff

Use **Spec Reviewer** between any spec stages. Use **Code Reviewer** after code generation or manual coding.

## License

See [LICENSE](LICENSE).
