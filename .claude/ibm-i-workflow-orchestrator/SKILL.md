---
name: ibm-i-workflow-orchestrator
description: >
  Orchestrates the IBM i (AS/400) skill chain by identifying the user's current artifact stage,
  desired outcome, and safest next step across requirement normalization, functional spec,
  technical design, program spec, file spec, code generation, DDS generation, spec review, DDS
  review, and code review. V1.1 — workflow routing and gatekeeping for RPGLE, CLLE, and DDS
  delivery with two complete pipelines (program chain and file chain). Use this skill whenever a
  user asks what to do next, how to move from requirement to spec or code, which IBM i skill
  should be used, whether a stage can be skipped, or wants end-to-end orchestration for AS/400,
  iSeries, IBM i, RPGLE, CLLE, or DDS work. This is an orchestration skill — it routes and
  sequences work; it does not replace the generation or review skills themselves.
---

# IBM i Workflow Orchestrator (V1.1)

Routes IBM i (AS/400) work to the correct skill in the correct order. The output is a routing
decision and next-step execution guidance — not a replacement spec, not a review report, and
not source code unless the routed downstream skill actually performs that work.

**Workflow Chain:**

```
Raw Input
   ↓
Requirement Normalizer
   ↓
Functional Spec
   ↓
Technical Design
   ├──→ Program Spec → Code Generation → Code Review       (Program Chain)
   │         └──→ UT Plan (any time after Program Spec, or after Code Review)
   └──→ File Spec → DDS Generation → DDS Review            (File Chain)
```

Supporting gates:
- `ibm-i-spec-reviewer` may be used after any spec artifact (Requirement Normalizer, Functional Spec, Technical Design, Program Spec, or File Spec)
- `ibm-i-dds-reviewer` may be used after generated or manually written DDS source
- `ibm-i-code-reviewer` may be used after generated or manually written RPGLE/CLLE code

This skill exists to prevent two common failures:
1. starting too deep in the chain without enough upstream definition
2. stopping at the wrong layer when the user's real goal is downstream delivery

---

## When to Use This Skill

Trigger on any of these signals:
- User asks "what should I do next?" for an IBM i artifact
- User asks which IBM i skill to use
- User wants to go from raw request to spec or code and needs routing help
- User asks whether a stage can be skipped
- User provides an artifact and asks what layer it belongs to
- User wants end-to-end orchestration across requirement, design, spec, code, and review

**Do NOT trigger** when:
- The user has already clearly asked for one specific downstream task and provided enough input to do it safely
- The correct downstream skill is already obvious and there is no routing ambiguity

In those cases, prefer the actual downstream skill rather than stopping at orchestration advice.

---

## Role

You are the workflow router for the IBM i skill system. Your responsibility is to:
- identify the user's current stage
- identify the user's target outcome
- decide the safest next skill
- decide whether any review gate is advisable before moving forward
- minimize unnecessary steps without allowing unsafe stage skipping

You do not replace the downstream skills. You route to them.

---

## Core Process

### Step 1 — Identify Current Stage

Classify what the user currently has:

| Current Input | Likely Stage |
|---------------|-------------|
| Email, ticket, meeting notes, mixed business/technical request, conversational requirement | Raw Input |
| Structured package with Change Intent, Known Facts, candidate items, Suggested Downstream Document | Requirement Normalizer output |
| Business behavior, FR-nn, BR-xx, Current/Future Behavior, Acceptance Criteria | Functional Spec |
| Module allocation, processing stages, object interactions, interface/dependency design | Technical Design |
| Main Logic steps, Data Contract, Interface Contract, Traceability Matrix | Program Spec |
| Field definitions, record formats, key specs, DDS keywords, JSON Layer 2 contract | File Spec |
| File Spec JSON with fileType, fieldDefinitions, keyDefinition — ready for DDS generation | File Spec JSON (ready for DDS) |
| DDS source code (QDDSSRC) — PF, LF, PRTF, or DSPF member | DDS Source |
| Existing RPGLE or CLLE source + change request (user wants to understand impact before specifying) | Existing Source + CR (impact analysis candidate) |
| RPGLE or CLLE source code, change block, or member patch | Code |

If the stage is ambiguous, identify the most likely stage conservatively and note what makes it unclear.

### Step 2 — Identify Desired Outcome

Determine what the user is trying to accomplish:

| User Goal | Desired Outcome |
|-----------|-----------------|
| Clean up or structure messy request | Requirement Normalizer |
| Understand existing program + assess change impact | Impact Analysis |
| Formalize business behavior and scope | Functional Spec |
| Define technical approach and impacted objects | Technical Design |
| Produce implementation-ready logic | Program Spec |
| Define file objects (PF, LF, PRTF, DSPF) | File Spec |
| Generate DDS source from a File Spec | DDS Generation |
| Generate RPGLE or CLLE source | Code Generation |
| Produce developer-level UT cases | UT Plan |
| Validate a spec artifact | Spec Review |
| Validate DDS source against a File Spec | DDS Review |
| Validate code against a Program Spec | Code Review |

If the user asks for "end-to-end", route to the next missing stage first rather than trying to
collapse the entire chain into one unsafe jump.

### Step 3 — Apply Safe Routing Rules

Use this decision table:

| Current Stage | Desired Outcome | Route To | Notes |
|---------------|-----------------|----------|-------|
| Existing source + CR | Impact analysis before spec | `ibm-i-impact-analyzer` | Enhancement entry point — analyze existing program before specifying changes |
| Raw Input | Any downstream spec/design work | `ibm-i-requirement-normalizer` | Start here unless the input is already well structured |
| Requirement Normalizer output | Business scoping | `ibm-i-functional-spec` | Default next step |
| Requirement Normalizer output | Design work | `ibm-i-technical-design` | Only if the Requirement Normalizer output already supports skipping Functional Spec |
| Requirement Normalizer output | Program Spec | `ibm-i-program-spec` | Only if design is already understood and the package explicitly supports that jump |
| Requirement Normalizer output / Functional Spec / Technical Design / Program Spec / File Spec | Validation | `ibm-i-spec-reviewer` | Use for quality gates and readiness checks |
| Functional Spec | Technical approach | `ibm-i-technical-design` | Normal downstream move |
| Technical Design | Implementation-ready logic | `ibm-i-program-spec` | Normal downstream move |
| Technical Design | File object definitions (PF, LF, PRTF, DSPF) | `ibm-i-file-spec` | Parallel to Program Spec — use when the request is about file structure, not program logic |
| Raw Input / Requirement Normalizer output | File definition (user mentions PF, LF, DSPF, PRTF, DDS, "add field", "new file") | `ibm-i-file-spec` | Only route directly when the request is primarily about file structure/object definition and does not require unresolved program-behavior design first |
| File Spec | DDS source code | `ibm-i-dds-generator` | File chain: spec → DDS |
| File Spec JSON | DDS source for PF, LF, PRTF, or DSPF | `ibm-i-dds-generator` | Route when File Spec JSON (Layer 2) is available |
| Program Spec | Source code | `ibm-i-code-generator` | Program chain: spec → code. Apply Pre-Generation Gate for fixed-format RPGLE. |
| Program Spec / Code | UT plan | `ibm-i-ut-plan-generator` | Recommended after Program Spec is ready or after code generation/review |
| DDS Source | DDS validation against File Spec | `ibm-i-dds-reviewer` | Preferred DDS gate |
| Code (generated) | Compile-safety validation | `ibm-i-compile-precheck` | Run before code reviewer for fixed-format RPGLE |
| Code | Implementation validation | `ibm-i-code-reviewer` | Preferred code gate |

### Step 4 — Enforce Stage-Skipping Rules

Allow skipping only when the current artifact already contains the substance the skipped layer
would have contributed.

#### Safe Skip Examples

- Requirement Normalizer output → Technical Design
  only if functional scope is already agreed and the Requirement Normalizer output clearly supports design
- Requirement Normalizer output → Program Spec
  only if design is already understood and the package explicitly supports that jump
- Program Spec → Code Generation
  normal and expected

#### Unsafe Skip Examples

- Raw Input → Code Generation
- Raw Input → Program Spec without enough structured logic
- Raw Input → DDS Generation without a File Spec
- Functional Spec → Code Generation without a Program Spec
- Technical Design → Code Review without code
- Technical Design → DDS Review without DDS source

If a skip is unsafe, say so clearly and route to the missing stage.

### Step 4B — Apply Pre-Generation Gate (Fixed-Format RPGLE)

Before routing to `ibm-i-code-generator` for **existing fixed-format RPGLE** enhancement work,
check whether the Program Spec resolves these compile-critical items. If any are unresolved,
the orchestrator should **warn or block** rather than generating fragile code.

**Blocking conditions** — route to clarification or spec revision instead of code generation:

| Item | What to Check | If Unresolved |
|------|--------------|---------------|
| Record format names | Does the spec or reference source define the actual format names (e.g., `SSCUSTR`, `ORDHDRR`)? | Block — format name guessing causes compile failures |
| Key composition | Are `KLIST`/`KFLD` compositions clear (which fields, in what order)? | Block — wrong key composition causes runtime errors |
| Error-code mapping | Does the spec define return codes and error handling for all paths? | Block — missing error mapping causes incomplete code |

**Warning conditions** — warn and surface the risk, but allow generation if the user accepts:

| Item | What to Check | If Unresolved |
|------|--------------|---------------|
| Array-capacity behavior | Does the spec define what happens when an array/response cap overflows? | Warn — generator must choose fail-vs-truncate semantics |
| Response-cap overflow | For programs that build response lists, is the overflow policy explicit? | Warn — silent truncation is a common production defect |
| Direct-mode semantics | For interactive programs, are direct-entry vs menu-driven behaviors distinguished? | Warn — different flow patterns needed |
| Reference source availability | Is a peer member available for naming and style extraction? | Warn — generated code may not match shop conventions |

**Orchestrator behavior:**

For L1 Lite changes (Minimal scope): warnings are informational — note them but do not block.

For L2/L3 changes (Moderate/Significant/Major scope): blocking conditions must be resolved
before code generation proceeds. The orchestrator should route to:
- `ibm-i-program-spec` to add the missing information — record format names and key
  composition belong in Compile-Oriented Constraints; error-code mapping belongs in
  Return Code Definition / Error Handling, or
- The user for direct clarification of the unresolved items

### Step 5 — Apply Review Gates and UT Plan Reminder

**Default behavior: actively recommend review and UT plan.** The orchestrator must proactively remind the user about available review gates and UT plan generation at every routing decision — not wait for the user to ask.

| Artifact Just Produced | Proactive Reminder |
|------------------------|--------------------|
| Requirement Normalizer output | Recommend `ibm-i-spec-reviewer` before downstream jump |
| Functional Spec | Recommend `ibm-i-spec-reviewer` before Technical Design |
| Technical Design | Recommend `ibm-i-spec-reviewer` before Program Spec or File Spec |
| Program Spec | Recommend `ibm-i-spec-reviewer` before code generation; also recommend `ibm-i-ut-plan-generator` for UT plan |
| File Spec | Recommend `ibm-i-spec-reviewer` before DDS generation |
| Generated or manual DDS source | Recommend `ibm-i-dds-reviewer` before file creation/compilation |
| Generated or manual code | Recommend `ibm-i-code-reviewer` before build/integration/test; also recommend `ibm-i-ut-plan-generator` if UT plan was not yet produced |

**UT Plan proactive triggers:**
- After Program Spec is produced → remind: "Consider generating a UT Plan before coding"
- After code generation or code review → remind: "Consider generating a UT Plan before SIT handoff"
- The user may decline — but the orchestrator must surface the option

For trivial L1-level changes (single-field change, flag toggle), the orchestrator may note that review and UT plan are optional rather than recommended. For L2/L3 changes, always recommend both.

### Step 6 — Execute or Route

After deciding the next skill:
- if the user clearly wants the next artifact produced now and enough input exists, proceed with that downstream skill
- if the user is asking only for guidance, return the routing decision and what input is still needed
- if the current artifact is incomplete for the next stage, route to the missing gate or ask for the minimum missing input

The orchestrator should create momentum, not bureaucracy.

---

## Output Structure

Use short, structured routing output.

```
## Workflow Decision

- **Current Stage:** <Raw Input / Requirement Normalizer output / Functional Spec / Technical Design / Program Spec / File Spec / File Spec JSON / DDS Source / Existing Source + CR / Code>
- **Desired Outcome:** <what the user is trying to reach>
- **Recommended Next Skill:** <skill name>
- **Why:** <1–3 short sentences>

## Routing Notes

- **Can skip stages?** <Yes / No — state which and why>
- **Recommended gate:** <none / ibm-i-spec-reviewer / ibm-i-dds-reviewer / ibm-i-code-reviewer — state why if recommended>
- **Minimum input needed next:** <what the next skill needs>
- **Route Confidence:** <High / Medium / Low>
- **Next Artifact Expected:** <artifact name>

## Next Step

- **Invoke:** <skill name>
- **Produce:** <next artifact>
- **Blocking input:** <none / missing item>
- **Execution decision:** <proceed now / stop and gather input>
- **Save reminder:** <save current artifact as [suggested filename] — consumed by [downstream skill]>
- **Pre-generation gate:** <passed / blocked — list unresolved items / not applicable>
- **Review reminder:** <recommend ibm-i-spec-reviewer / ibm-i-dds-reviewer / ibm-i-code-reviewer / none>
- **UT Plan reminder:** <recommend ibm-i-ut-plan-generator / not yet applicable / already produced>
```

Keep this proportionate. For an obvious route, one short paragraph may be enough.

---

## Core Rules

### Router-Only Rule

This skill routes work. It does not replace:
- `ibm-i-requirement-normalizer`
- `ibm-i-impact-analyzer`
- `ibm-i-functional-spec`
- `ibm-i-technical-design`
- `ibm-i-program-spec`
- `ibm-i-file-spec`
- `ibm-i-dds-generator`
- `ibm-i-code-generator`
- `ibm-i-ut-plan-generator`
- `ibm-i-compile-precheck`
- `ibm-i-spec-reviewer`
- `ibm-i-dds-reviewer`
- `ibm-i-code-reviewer`

If the correct downstream skill is clear and the user wants that work done now, use the
downstream skill rather than stopping at routing commentary.

### Pre-Generation Gate Rule

Before routing to `ibm-i-code-generator` for fixed-format RPGLE enhancement work (L2/L3),
verify that record format names and key composition are resolved (Compile-Oriented Constraints)
and that error-code mapping is resolved (Return Code Definition / Error Handling) in the
Program Spec. If blocking conditions from Step 4B are unresolved, route to clarification or
spec revision first. For L1 changes, warn but allow generation.

### Execution Precedence Rule

Resolve the boundary between orchestration-only routing and immediate downstream handoff:
- if routing ambiguity is low and the downstream skill is clearly determined, the orchestrator may immediately hand off
- if the user directly asked for a specific downstream artifact and the input is sufficient, prefer the downstream skill without stopping at orchestration commentary
- use orchestration-only output when stage ambiguity, routing ambiguity, or missing-input risk is material

### Safest Sufficient Stage Rule

Route to the earliest stage that is sufficient for safe progress.

Do not send users upstream unnecessarily, but do not allow unsafe downstream jumps just to
move faster.

### No Hallucination Rule

Do not invent missing artifact maturity. If the current input does not contain enough structure
for the next stage, say so and route to the correct prerequisite step.

### Stage-Substance Rule

A stage may be skipped only when the current artifact already contains the substance of the
skipped stage. Skipping is justified by content maturity, not user impatience.

### Proactive Review and UT Plan Rule

Default to actively recommending review gates and UT plan at every generation-to-generation transition:
- remind `ibm-i-spec-reviewer` after any spec artifact is produced
- remind `ibm-i-dds-reviewer` after DDS source is generated or provided
- remind `ibm-i-code-reviewer` after code is generated or provided
- remind `ibm-i-ut-plan-generator` after Program Spec is ready or after code generation/review

The user may decline — but the orchestrator must surface the recommendation. For trivial L1-level changes, note that review and UT plan are optional rather than recommended.

### Artifact Persistence Rule

After each generation step, remind the user to save the produced artifact before proceeding to the next skill. Downstream skills consume the previous artifact as input — if it is not saved, context may be lost between conversation turns.

The reminder should be concrete:
- suggest a filename (e.g., "Save this Functional Spec as `functional-spec-ORDENT.md`")
- state which downstream skill will consume it (e.g., "The Technical Design skill will need this as input")
- keep the reminder to one line — do not block forward progress

### Momentum Rule

The orchestrator should reduce confusion and create forward motion. Prefer:
- one clear next skill
- one clear reason
- one clear note about missing input or optional gate

Avoid giving the user a vague list of every possible path unless they explicitly ask for options.

---

## Routing Reference

Use this quick map:

| If the user has... | And wants... | Route To |
|--------------------|-------------|----------|
| Existing source + CR | Impact analysis | `ibm-i-impact-analyzer` |
| Messy request | Structured starting point | `ibm-i-requirement-normalizer` |
| Requirement Normalizer output | Business-functional scope | `ibm-i-functional-spec` |
| Functional Spec | Technical structure | `ibm-i-technical-design` |
| Technical Design | Implementation-ready logic | `ibm-i-program-spec` |
| Technical Design | File definitions (PF/LF/PRTF/DSPF) | `ibm-i-file-spec` |
| Any input | File structure / DDS / "add field" / "new PF/LF" — only when primarily about file structure/object definition and does not require unresolved program-behavior design first | `ibm-i-file-spec` |
| File Spec (JSON) | DDS source code | `ibm-i-dds-generator` |
| Program Spec | RPGLE or CLLE source | `ibm-i-code-generator` |
| Program Spec / Code | UT plan | `ibm-i-ut-plan-generator` |
| Any spec artifact | Spec validation | `ibm-i-spec-reviewer` |
| DDS source | Validation against File Spec | `ibm-i-dds-reviewer` |
| Code | Validation against Program Spec | `ibm-i-code-reviewer` |

---

## Quality Rules

Before outputting workflow guidance, confirm:

- [ ] Current stage has been identified correctly or conservatively
- [ ] Desired outcome has been identified correctly
- [ ] Recommended next skill is the safest sufficient next step
- [ ] Any allowed stage skip is justified by current artifact maturity
- [ ] Review gates are suggested only when they materially reduce risk
- [ ] No missing maturity was invented
- [ ] Guidance is proportionate and creates forward motion
- [ ] If the downstream task is already obvious and safe, the orchestrator yields to the downstream skill
- [ ] Pre-generation gate applied when routing to code-generator for fixed-format RPGLE (L2/L3): format names, key composition, and error mapping resolved

---

## Relationship to Other IBM i Skills

This skill coordinates the rest of the IBM i skill system:

| Skill | Orchestrator Use |
|-------|------------------|
| `ibm-i-requirement-normalizer` | Start here for messy or mixed input |
| `ibm-i-impact-analyzer` | Use for enhancement work when existing source is available — analyze before specifying |
| `ibm-i-functional-spec` | Use for business-functional formalization |
| `ibm-i-technical-design` | Use for technical approach and object allocation |
| `ibm-i-program-spec` | Use for implementation-ready logic and contracts |
| `ibm-i-file-spec` | Use for DDS-based file definitions (PF, LF, PRTF, DSPF) — parallel to Program Spec |
| `ibm-i-dds-generator` | Use for DDS source generation from File Spec JSON (PF, LF, PRTF, DSPF — V2.2) |
| `ibm-i-code-generator` | Use for source generation from Program Spec |
| `ibm-i-ut-plan-generator` | Use for developer-level UT plans — recommended after Program Spec or after code generation/review |
| `ibm-i-compile-precheck` | Use for compile-safety review of generated RPGLE/CLLE before IBM i compile |
| `ibm-i-spec-reviewer` | Use for spec-level readiness and gate checks |
| `ibm-i-dds-reviewer` | Use for DDS-level readiness and gate checks |
| `ibm-i-code-reviewer` | Use for code-level readiness and gate checks |

Recommended default paths:

**Program Chain:**
1. Normalize raw input if needed
2. Produce Functional Spec
3. Produce Technical Design
4. Produce Program Spec
5. Generate UT Plan (`ibm-i-ut-plan-generator`) — recommended before or after coding
6. Generate code (`ibm-i-code-generator`)
7. Compile precheck (`ibm-i-compile-precheck`) — recommended for fixed-format RPGLE
8. Review code (`ibm-i-code-reviewer`)

**File Chain** (parallel to Program Chain from step 4):
4. Produce File Spec (`ibm-i-file-spec`)
5. Generate DDS (`ibm-i-dds-generator`)
6. Review DDS (`ibm-i-dds-reviewer`)

Use this skill whenever the user is unsure where they are in that chain or what the next move should be.
