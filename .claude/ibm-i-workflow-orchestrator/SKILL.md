---
name: ibm-i-workflow-orchestrator
description: >
  Orchestrates the IBM i (AS/400) skill chain by identifying the user's current artifact stage,
  desired outcome, and safest next step across requirement normalization, functional spec,
  technical design, program spec, code generation, spec review, and code review. V1.0 —
  workflow routing and gatekeeping for RPGLE and CLLE delivery. Use this skill whenever a user
  asks what to do next, how to move from requirement to spec or code, which IBM i skill should
  be used, whether a stage can be skipped, or wants end-to-end orchestration for AS/400, iSeries,
  IBM i, RPGLE, or CLLE work. This is an orchestration skill — it routes and sequences work; it
  does not replace the generation or review skills themselves.
---

# IBM i Workflow Orchestrator (V1.0)

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
   ↓
Program Spec
   ↓
Code Generation
   ↓
Code Review
```

Supporting gates:
- `ibm-i-spec-reviewer` may be used after Requirement Normalizer, Functional Spec, Technical Design, or Program Spec
- `ibm-i-code-reviewer` may be used after generated or manually written code

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
| RPGLE or CLLE source code, change block, or member patch | Code |

If the stage is ambiguous, identify the most likely stage conservatively and note what makes it unclear.

### Step 2 — Identify Desired Outcome

Determine what the user is trying to accomplish:

| User Goal | Desired Outcome |
|-----------|-----------------|
| Clean up or structure messy request | Requirement Normalizer |
| Formalize business behavior and scope | Functional Spec |
| Define technical approach and impacted objects | Technical Design |
| Produce implementation-ready logic | Program Spec |
| Generate RPGLE or CLLE source | Code Generation |
| Validate a spec artifact | Spec Review |
| Validate code against a Program Spec | Code Review |

If the user asks for "end-to-end", route to the next missing stage first rather than trying to
collapse the entire chain into one unsafe jump.

### Step 3 — Apply Safe Routing Rules

Use this decision table:

| Current Stage | Desired Outcome | Route To | Notes |
|---------------|-----------------|----------|-------|
| Raw Input | Any downstream spec/design work | `ibm-i-requirement-normalizer` | Start here unless the input is already well structured |
| Requirement Normalizer output | Business scoping | `ibm-i-functional-spec` | Default next step |
| Requirement Normalizer output | Design work | `ibm-i-technical-design` | Only if the normalized package already supports skipping Functional Spec |
| Requirement Normalizer output | Program Spec | `ibm-i-program-spec` | Only if design is already understood and the package explicitly supports that jump |
| Requirement Normalizer / Functional Spec / Technical Design / Program Spec | Validation | `ibm-i-spec-reviewer` | Use for quality gates and readiness checks |
| Functional Spec | Technical approach | `ibm-i-technical-design` | Normal downstream move |
| Technical Design | Implementation-ready logic | `ibm-i-program-spec` | Normal downstream move |
| Program Spec | Source code | `ibm-i-code-generator` | Preferred handoff to code |
| Code | Implementation validation | `ibm-i-code-reviewer` | Preferred code gate |

### Step 4 — Enforce Stage-Skipping Rules

Allow skipping only when the current artifact already contains the substance the skipped layer
would have contributed.

#### Safe Skip Examples

- Requirement Normalizer → Technical Design
  only if functional scope is already agreed and the normalized package clearly supports design
- Requirement Normalizer → Program Spec
  only if design is already understood and the package explicitly supports that jump
- Program Spec → Code Generation
  normal and expected

#### Unsafe Skip Examples

- Raw Input → Code Generation
- Raw Input → Program Spec without enough structured logic
- Functional Spec → Code Generation without a Program Spec
- Technical Design → Code Review without code

If a skip is unsafe, say so clearly and route to the missing stage.

### Step 5 — Apply Review Gates

Use review skills when they materially reduce risk:

| Artifact | Optional / Recommended Gate |
|----------|-----------------------------|
| Requirement Normalizer output | `ibm-i-spec-reviewer` when routing confidence is low or downstream jump is non-standard |
| Functional Spec | `ibm-i-spec-reviewer` before Technical Design when business scope is still uncertain |
| Technical Design | `ibm-i-spec-reviewer` before Program Spec when object allocation or impact is risky |
| Program Spec | `ibm-i-spec-reviewer` before code generation when implementation risk is high |
| Generated or manual code | `ibm-i-code-reviewer` before build/integration/test |

Do not force review gates mechanically for trivial requests. Use them when they prevent real downstream risk.

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

- **Current Stage:** <Raw Input / Requirement Normalizer / Functional Spec / Technical Design / Program Spec / Code>
- **Desired Outcome:** <what the user is trying to reach>
- **Recommended Next Skill:** <skill name>
- **Why:** <1–3 short sentences>

## Routing Notes

- **Can skip stages?** <Yes / No — state which and why>
- **Recommended gate:** <none / ibm-i-spec-reviewer / ibm-i-code-reviewer — state why if recommended>
- **Minimum input needed next:** <what the next skill needs>

## Action

<Either:>
- Proceed now with `<skill name>`

<Or:>
- Gather / confirm: <minimum missing item(s)>
```

Keep this proportionate. For an obvious route, one short paragraph may be enough.

---

## Core Rules

### Router-Only Rule

This skill routes work. It does not replace:
- `ibm-i-requirement-normalizer`
- `ibm-i-functional-spec`
- `ibm-i-technical-design`
- `ibm-i-program-spec`
- `ibm-i-code-generator`
- `ibm-i-spec-reviewer`
- `ibm-i-code-reviewer`

If the correct downstream skill is clear and the user wants that work done now, use the
downstream skill rather than stopping at routing commentary.

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

### Review Gate Rule

Recommend review gates when they materially reduce risk:
- route to `ibm-i-spec-reviewer` for spec-level uncertainty or non-standard downstream jumps
- route to `ibm-i-code-reviewer` before build/integration/test for generated or manual code

Do not force reviewers into every trivial path.

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
| Messy request | Structured starting point | `ibm-i-requirement-normalizer` |
| Normalized package | Business-functional scope | `ibm-i-functional-spec` |
| Functional Spec | Technical structure | `ibm-i-technical-design` |
| Technical Design | Implementation-ready logic | `ibm-i-program-spec` |
| Program Spec | RPGLE or CLLE source | `ibm-i-code-generator` |
| Any spec artifact | Validation | `ibm-i-spec-reviewer` |
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

---

## Relationship to Other IBM i Skills

This skill coordinates the rest of the IBM i skill system:

| Skill | Orchestrator Use |
|-------|------------------|
| `ibm-i-requirement-normalizer` | Start here for messy or mixed input |
| `ibm-i-functional-spec` | Use for business-functional formalization |
| `ibm-i-technical-design` | Use for technical approach and object allocation |
| `ibm-i-program-spec` | Use for implementation-ready logic and contracts |
| `ibm-i-code-generator` | Use for source generation from Program Spec |
| `ibm-i-spec-reviewer` | Use for document-level readiness and gate checks |
| `ibm-i-code-reviewer` | Use for code-level readiness and gate checks |

Recommended default path:
1. Normalize raw input if needed
2. Produce Functional Spec
3. Produce Technical Design
4. Produce Program Spec
5. Generate code
6. Review code

Use this skill whenever the user is unsure where they are in that chain or what the next move should be.
