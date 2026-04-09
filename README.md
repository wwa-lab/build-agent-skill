# IBM i Agent Skill Family

A complete Claude Code skill family for IBM i (AS/400) enterprise development — from raw requirement intake through specification, code generation, review, unit test planning, and executable test scaffold generation.

## Document Chain

```
Raw Input → Requirement Normalizer → Functional Spec → Technical Design ──→ Program Spec → Code
                                  ↗                           │                 │            ↑
   Existing Source → Program Analyzer ──→ Impact Analyzer     └──→ File Spec → DDS Source    |
                                  (+ CR)  ↗                                   │              |
                                                                 UT Plan Generator → Test Scaffold (SQL/CL scripts)
                                          Spec Reviewer (reviews any spec layer)             |
                                          DDS Reviewer (reviews DDS source against File Spec)|
                                          Code Reviewer (reviews generated/written code) ────┘
                                          Workflow Orchestrator (routes work through the chain)
```

## Skills

### Generation & Analysis Skills

| Skill | What It Produces | Audience |
|-------|-----------------|----------|
| **ibm-i-requirement-normalizer** | Structured requirement package from messy input (emails, tickets, notes) | BA, project lead |
| **ibm-i-program-analyzer** | Program comprehension from existing RPGLE/CLLE source — logic, call flow, structure | Developers, tech leads |
| **ibm-i-impact-analyzer** | Impact analysis of existing source + CR — what exists, what changes, what's the risk | Developers, tech leads |
| **ibm-i-functional-spec** | Business-functional document: current/future behavior, business rules, acceptance criteria | Business stakeholders |
| **ibm-i-technical-design** | Design document: module allocation, processing stages, object interaction, impact analysis | Solution architects |
| **ibm-i-program-spec** | Implementation spec: step-by-step logic, data/interface contracts, BR traceability | Developers, testers |
| **ibm-i-file-spec** | File definition spec: PF, LF, PRTF, DSPF — DDS field layouts, keys, screen/print layouts | Developers, DBAs |
| **ibm-i-code-generator** | RPGLE or CLLE source code from a Program Spec (Skeleton or Full Implementation) | Developers |
| **ibm-i-dds-generator** | DDS source code from a File Spec JSON — PF, LF, PRTF, DSPF (V2.2) | Developers |
| **ibm-i-ut-plan-generator** | Unit Test Plan from specs, CRs, or raw input — concrete UT cases, IBM i-aware | Developers |
| **ibm-i-test-scaffold** | Executable SQL/CL test scripts from UT Plans — setup, data, compile, execute, verify, cleanup | Developers, testers |

### Review & Orchestration Skills

| Skill | What It Does | Gate |
|-------|-------------|------|
| **ibm-i-compile-precheck** | Pre-compile review of RPGLE/CLLE — opcode safety, KLIST completeness, alias consistency, bounds | Compile safety gate |
| **ibm-i-spec-reviewer** | Reviews any spec document — checks layer boundary, completeness, traceability, downstream readiness | Spec quality gate |
| **ibm-i-dds-reviewer** | Reviews DDS source against File Spec — checks correctness, syntax, completeness, type-specific rules | DDS quality gate |
| **ibm-i-code-reviewer** | Reviews RPGLE/CLLE source against Program Spec — checks correctness, enhancement safety, format policy | Code quality gate |
| **ibm-i-workflow-orchestrator** | Routes work to the correct skill in the correct order — identifies current stage and safest next step | Workflow routing |

## Key Design Principles

**Layer boundary discipline** — Each skill stays in its lane. A Functional Spec describes business behavior, not module allocation. A Technical Design describes design structure, not implementation steps. Layer collapse is the primary failure mode these skills are designed to prevent.

**BR-xx continuity** — Business rules use the same BR-xx numbering across the entire chain. The expression changes per layer (business constraint → module allocation → implementation condition) but the number carries forward.

**Enhancement-first** — All skills are optimized for IBM i BAU enhancement work, not only greenfield projects. Enhancement tagging (NEW / MODIFIED / EXISTING), delta-first code generation, and partial-input safe drafting reflect real enterprise environments.

**Anti-hallucination** — No skill invents object names, business rules, or system details. Unknowns are marked TBD. Inferences are labeled and tracked.

**Tiered output** — Functional Spec, Technical Design, Program Spec, and File Spec use L1 (Lite) / L2 (Standard) / L3 (Full) levels based on complexity. Tiers change scope, not quality.

## Installation

Copy the `.claude/` directory into your project. Each skill is self-contained in its `SKILL.md`:

```
.claude/
├── ibm-i-requirement-normalizer/SKILL.md    # V1.0 + examples
├── ibm-i-functional-spec/SKILL.md           # V1.0 + references, examples
├── ibm-i-technical-design/SKILL.md          # V1.0 + references, examples
├── ibm-i-program-spec/SKILL.md              # V2.5 + section-guide, tier-guide, samples
├── ibm-i-file-spec/SKILL.md                 # V2.1.2 + references (5), examples (5)
├── ibm-i-program-analyzer/SKILL.md          # V1.0 — source logic comprehension and call flow
├── ibm-i-impact-analyzer/SKILL.md           # V1.2 — pre-spec change impact analysis
├── ibm-i-dds-generator/SKILL.md             # V2.2 + examples (6), tests (31 cases)
├── ibm-i-code-generator/SKILL.md            # V1.0 + references (3), examples (6), tests (8 cases)
├── ibm-i-ut-plan-generator/SKILL.md         # V1.2 — unit test plan from specs, CRs, raw input
├── ibm-i-test-scaffold/SKILL.md             # V1.1 — executable SQL/CL test scripts from UT Plans
├── ibm-i-test-scaffold/examples/            # sample outputs for batch, interactive, CL, service program
├── ibm-i-test-scaffold/tests/               # runner.sh + 6 structural test cases
├── ibm-i-compile-precheck/SKILL.md          # V1.0 — pre-compile safety review + checklists
├── ibm-i-dds-reviewer/SKILL.md              # V1.2 — DDS source review gate
├── ibm-i-spec-reviewer/SKILL.md             # V1.1 + examples
├── ibm-i-code-reviewer/SKILL.md             # V1.0 + references (3), examples (5)
└── ibm-i-workflow-orchestrator/SKILL.md     # V1.1 workflow routing
```

## Usage

Skills trigger automatically based on context. You can also invoke them by name:

```
# Normalize a messy requirement
"Normalize this change request for IBM i: [paste raw input]"

# Understand an existing program before changing it
"Analyze this RPGLE member and explain what it does"

# Assess enhancement impact on existing source
"Review this source and CR, and tell me what needs to change"

# Generate a functional spec
"Write a functional spec for this IBM i enhancement: [requirement]"

# Generate a technical design
"Create a technical design for this IBM i change: [requirement]"

# Generate a program spec
"Write a program spec for this RPGLE program: [requirement]"

# Define a file object
"Spec out a new physical file for customer master data"
"Design a display file with subfile for order inquiry"
"Add email and phone fields to the CUSTMAST PF"

# Generate DDS from a file spec
"Generate DDS source from this File Spec JSON"

# Generate code from a spec
"Implement this Program Spec in RPGLE"

# Generate a unit test plan
"Write a UT plan for this Program Spec"
"What should I test for this change request?"

# Generate executable test scaffolding
"Generate SQL/CL test scripts from this UT Plan"
"Create mock data, compile commands, and PASS/FAIL verification for these test cases"

# Review a spec
"Review this Technical Design for layer boundary and completeness"

# Review code
"Review this RPGLE against the Program Spec"
```

## Recommended Workflow

### New Development Path

1. **Normalize** raw input if messy or incomplete
2. **Functional Spec** → business review → scope approval
3. **Technical Design** → design review → design approval
4. **Program Spec** → build review → implementation readiness
5. **UT Plan** → developer-level test cases
6. **Test Scaffold** → executable SQL/CL for setup, compile, execute, verify, cleanup
7. **Code Generation** → compile precheck → code review → build handoff
8. **File Spec** → file definition review (parallel to Program Spec when DDS objects are involved)

### Enhancement Path

1. **Program Analyzer** → understand existing source when there is no CR yet
2. **Impact Analyzer** → scope the change against existing source + CR
3. **Program Spec** → implementation-ready logic
4. **UT Plan** → targeted developer-level test cases
5. **Test Scaffold** → executable test scripts for TDD or self-test
6. **Code Generation** → compile precheck → code review

Use **Spec Reviewer** between spec stages. Use **DDS Reviewer** after DDS generation or manual DDS changes. Use **Code Reviewer** after code generation or manual coding. Use **UT Plan Generator** before coding for test-first work or before SIT handoff, and use **Test Scaffold** when you want runnable SQL/CL scripts from those test cases.

## License

See [LICENSE](LICENSE).
