# IBM i Agent Skill Family

A complete Claude Code skill family for IBM i (AS/400) enterprise development — from raw requirement intake through specification, code generation, review, unit test planning, and executable test scaffold generation. Supports both **single-step routing** (default, one decision at a time) and **task.md batched execution** (Plan → Approve → Execute, optionally driven straight from a Technical Design).

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
                                          Workflow Orchestrator — Routing Mode (single step)
                                                                  Plan Mode (emit task.md from spec or TD)
                                                                  Execute Mode (run an approved task.md end-to-end)
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
| **ibm-i-program-spec** | Implementation spec: step-by-step logic, data/interface contracts, BR traceability. **V2.6+ supports TD-aware mode** for orchestrator-driven module-scoped generation | Developers, testers |
| **ibm-i-file-spec** | File definition spec: PF, LF, PRTF, DSPF — DDS field layouts, keys, screen/print layouts. **V2.2+ supports TD-aware mode** for orchestrator-driven file-scoped generation | Developers, DBAs |
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
| **ibm-i-workflow-orchestrator** | Routes work to the correct skill (Routing Mode), generates a task.md batch plan (Plan Mode), or runs an approved task.md end-to-end (Execute Mode). **V1.2+** | Workflow routing + batch execution |

## Key Design Principles

**Layer boundary discipline** — Each skill stays in its lane. A Functional Spec describes business behavior, not module allocation. A Technical Design describes design structure, not implementation steps. Layer collapse is the primary failure mode these skills are designed to prevent.

**BR-xx continuity** — Business rules use the same BR-xx numbering across the entire chain. The expression changes per layer (business constraint → module allocation → implementation condition) but the number carries forward.

**Enhancement-first** — All skills are optimized for IBM i BAU enhancement work, not only greenfield projects. Enhancement tagging (NEW / MODIFIED / EXISTING), delta-first code generation, and partial-input safe drafting reflect real enterprise environments.

**Anti-hallucination** — No skill invents object names, business rules, or system details. Unknowns are marked TBD. Inferences are labeled and tracked.

**Tiered output** — Functional Spec, Technical Design, Program Spec, and File Spec use L1 (Lite) / L2 (Standard) / L3 (Full) levels based on complexity. Tiers change scope, not quality.

**Plan / Execute separation (V1.2+)** — Batched runs are split into a human-approved plan (`task.md`, generated by Plan Mode) and an automated execution phase (Execute Mode). The orchestrator never silently re-plans during execution, and Plan Mode never silently executes. Human checkpoints (`draft → approved`, Spec Approval Gate, reviewer Critical halts) are explicit and non-bypassable.

## task.md — Batch Execution Mechanism (V1.2+)

In addition to one-step-at-a-time routing, the orchestrator can drive a multi-target batch run from a single approved spec or Technical Design. The batch is described by a `task.md` file with eight sections:

| Section | Purpose |
|---------|---------|
| §1 Metadata | task_id, mode, status (`draft / approved / running / awaiting-spec-approval / blocked / done / failed`), approval signatures |
| §2 Inputs | Paths to the input artifacts (program_spec / file_spec / technical_design / existing_source / cr_document) |
| §3 Targets | The deliverables to produce — one row per artifact, with skill name, output path, dependencies |
| §4 Execution Policy | How to handle Critical / High findings, TBDs, skill failures, parallelism |
| §5 Gate Definitions | Severity-based gates (compile precheck, code review, DDS review) and the structural Spec Approval Gate (TD-driven mode) |
| §6 Execution Log | Real-time `[ ] / [~] / [x] / [!] / [s]` markers updated by Execute Mode |
| §7 Open Questions | TBDs carried from input specs, plus auto-merged TBDs from generated Layer A specs (TD-driven mode). Each entry is `blocking: yes / no / pending-human-judgment` |
| §8 Manifest | Final deliverables list with output paths, hashes, reviewer verdicts |

**Plan Mode** (orchestrator) reads an approved spec / TD and emits a `task.md` draft. **Execute Mode** reads an approved `task.md` and runs the batch. Reference docs live under `.claude/ibm-i-workflow-orchestrator/references/`:

- `task-md-template.md` — canonical structure, mode dictionary, status dictionary, placeholder rules, parallel safety rules
- `task-md-execution-protocol.md` — preconditions, topological scheduler, gate evaluation, TBD handling, idempotent resume

Five worked samples are in `.claude/ibm-i-workflow-orchestrator/examples/`.

### Plan Mode Entry Shapes

| Entry shape | Inputs | §3 Targets emitted |
|-------------|--------|---------------------|
| **Program-spec entry** | Program Spec (+ optional File Spec, existing source, CR) | Code-gen + reviewers + UT plan + test scaffold |
| **File-only entry** | File Spec only | DDS-gen + DDS-review |
| **Combined entry** | Both Program Spec and File Spec | Both chains |
| **TD-driven entry** | Technical Design only | Two-layer §3: Layer A generates N program-specs (V2.6+) and M file-specs (V2.2+) from the TD; Layer B generates code, DDS, UT, tests downstream. Spec Approval Gate enforced between layers. |

## Installation

Copy the `.claude/` directory into your project. Each skill is self-contained in its `SKILL.md`:

```
.claude/
├── ibm-i-requirement-normalizer/SKILL.md    # V1.0 + examples
├── ibm-i-functional-spec/SKILL.md           # V1.0 + references, examples
├── ibm-i-technical-design/SKILL.md          # V1.0 + references, examples
├── ibm-i-program-spec/SKILL.md              # V2.6 — adds Step 0 TD-aware mode
├── ibm-i-file-spec/SKILL.md                 # V2.2 — adds Step 0 TD-aware mode
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
└── ibm-i-workflow-orchestrator/             # V1.2 — routing + Plan Mode + Execute Mode
    ├── SKILL.md
    ├── references/
    │   ├── mini-requirement-template.md
    │   ├── task-md-template.md              # task.md canonical structure
    │   └── task-md-execution-protocol.md    # how Execute Mode runs a task.md
    └── examples/
        ├── fixtures/
        │   └── fixture-td-customer-contact.md       # sample TD used by td-driven-with-file
        ├── sample-task-md-new-program.md            # Program Spec → full chain
        ├── sample-task-md-program-enhancement.md    # Program Spec + existing source → full chain
        ├── sample-task-md-file-enhancement.md       # File Spec only → DDS chain
        ├── sample-task-md-td-driven.md              # TD → 2 program-specs Layer A → Layer B
        └── sample-task-md-td-driven-with-file.md    # TD → 1 program-spec + 1 file-spec Layer A → Layer B
```

## Usage

Skills trigger automatically based on context. You can also invoke them by name:

```
# --- Routing Mode (default — one step at a time) ---

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

# Review a spec / DDS / code
"Review this Technical Design for layer boundary and completeness"
"Review this RPGLE against the Program Spec"

# --- Plan Mode (emit a task.md batch plan) ---

# From an approved Program Spec
"I have a Program Spec at <path>. Plan a batch run for the rest of the chain."

# From an approved Technical Design (auto-derive specs from the TD)
"I have a Technical Design at <path>. Plan a TD-driven batch run."

# --- Execute Mode (run an approved task.md) ---

"Execute this task.md at <path>."
"Run the approved batch in <task.md>."
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

### TD-Driven Batch Path (V1.2+)

For changes large enough to span multiple programs and/or files, drive the chain straight from one approved Technical Design:

1. **Technical Design** → design review → design approval *(human-driven)*
2. **Plan Mode** *(orchestrator)* → reads the TD, cross-references Module Allocation Table and Objects Affected, emits a `task.md` draft with two-layer §3 (Layer A: spec generation, Layer B: downstream artifacts)
3. **Approve task.md** *(human-driven)* → resolve `<...>` placeholders, classify any `pending-human-judgment` §7 entries, set `status: approved`
4. **Execute Mode — Layer A** *(orchestrator)* → invokes program-spec V2.6+ and file-spec V2.2+ in TD-aware mode for each derived target; auto-merges spec-level TBDs into §7 with `pending-human-judgment`
5. **Spec Approval Gate** *(human-driven)* → review every generated spec (optionally call Spec Reviewer), classify all `pending-human-judgment` entries, write `specs_approved_by`, set `status: running`
6. **Execute Mode — Layer B** *(orchestrator)* → code generation, compile precheck, code review, DDS generation, DDS review, UT plan, test scaffold; halts only on reviewer Critical findings
7. **Final Manifest** → `task.md` §8 lists every produced artifact with reviewer verdict and hash

Use **Spec Reviewer** between spec stages. Use **DDS Reviewer** after DDS generation or manual DDS changes. Use **Code Reviewer** after code generation or manual coding. Use **UT Plan Generator** before coding for test-first work or before SIT handoff, and use **Test Scaffold** when you want runnable SQL/CL scripts from those test cases.

## License

See [LICENSE](LICENSE).
