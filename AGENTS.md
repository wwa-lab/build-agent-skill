# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

---

## Repository Purpose

This repository contains a Codex Skill (`ibm-i-program-spec`) that generates structured Program Specifications for IBM i (AS/400) development in RPGLE and CLLE. The skill converts business requirements into formal specification documents that developers can use for implementation, testing, and validation.

The repository contains no application code—only specification templates, reference documentation, and example specs.

---

## Skill Invocation

The skill is triggered automatically when:
- A user mentions RPGLE, CLLE, RPG IV, AS/400, iSeries, or IBM i in a design/requirements context
- A user asks to "spec out", "document", or "design" an IBM i program
- A user provides a business requirement and asks for a program spec

Manual invocation: Use the skill name `ibm-i-program-spec` when needed.

---

## Core Architecture

### Spec Tiering System (V2.5)

The skill automatically selects the appropriate spec depth based on change complexity:

| Level | Name | Scope | Sections | Use Case |
|-------|------|-------|----------|----------|
| **L1** | Lite | Single-field change, flag toggle, minor validation | ~10 | Small, isolated changes to existing programs |
| **L2** | Standard | New subroutine, moderate logic change, parameter change | ~16 | Logic flow modifications, new functionality within existing program |
| **L3** | Full | New program, major redesign, multi-file transaction | All ~25 | New programs or fundamental rewrites |

**Selection rules** are in `.Codex/ibm-i-program-spec/tier-guide.md`. The skill determines the level in Step 1 based on the user's business requirement.

### Spec Generation Workflow (6 Steps)

The skill follows this process:

1. **Gather & Tier** — Extract business requirement, program type (RPGLE/CLLE), change type, and determine spec level
2. **Extract Business Rules** — Identify every decision point and constraint (BR-01, BR-02, etc.)
3. **Build Data Contract** — Enumerate fields, sources, storage, and step references (L2/L3 only)
4. **Generate Spec** — Produce output following section inclusion table for the determined level
5. **Build Traceability** — Cross-reference every BR-xx to logic steps, error handling, and files (L2/L3 only)
6. **Self-Check** — Verify against quality rules before output

---

## File Structure

```
.Codex/ibm-i-program-spec/
├── SKILL.md                    # Main skill definition (the skill itself)
├── section-guide.md            # Detailed guidance for each spec section
├── tier-guide.md               # Spec level selection rules and decision examples
├── v25-upgrade-analysis.md     # V2.5 release notes (breaking changes, tiering intro)
├── sample-rpgle-spec.md        # L3 Full spec example (RPGLE new program)
├── sample-clle-spec.md         # L3 Full spec example (CLLE new program)
└── sample-lite-spec.md         # L1 Lite spec example (change to existing program)
```

**Key reference for future work**:
- `SKILL.md` — contains the complete skill prompt and rules; modify this to change behavior
- `section-guide.md` — detailed content guidance for each section; refer to this when clarifying what belongs where
- `tier-guide.md` — decision examples and detailed level definitions; use this to validate spec level selection

---

## Core Rules & Constraints

### No Hallucination Rule
Never invent file names, field names, program names, data structures, or any system objects. If not explicitly provided, mark `TBD (To Be Confirmed)`.

### No Assumed Logic Rule
Never fill in business logic not explicitly stated in the requirement. If ambiguous, mark TBD and add to Open Questions.

### Step-Based Logic Rule
Main Logic must use numbered steps (`Step 1:`, `Step 2:`, etc.), never free-text paragraphs. Conditions use arrow notation: `IF condition → action`.

### Business Rule Traceability
Every conditional step in Main Logic must reference `(BR-xx)`. The Traceability Matrix (L2/L3) must include every BR with no gaps.

### Functions vs Logic Separation
Functions = WHAT (one sentence, imperative, describes purpose only)
Main Logic = HOW (numbered steps with conditions and actions)
Functions must never contain logic steps.

### Field Semantics (L2/L3)
Data Contract must declare storage intent for every field: Persisted, Display, or Transient.

---

## Spec Output Format

All specs follow the same structure (sections vary by level):

```
## Spec Header
- Spec ID: PROG-yyyymmdd-nn
- Spec Level: L1/L2/L3
- Change Type: New Program or Change to Existing
- Program Type: RPGLE or CLLE

## Amendment History
| Version | Date | Author | Change Description |

## Business Rules
BR-01: <condition or constraint>
BR-02: <condition or constraint>

## Main Logic
Step 1: <action>
Step 2: IF <condition> → <action> (BR-01)

## Error Handling
| Scenario | Return Code | Action | Logged? |

## Traceability Matrix (L2/L3 only)
| BR | Rule Summary | Logic Step(s) | Error Handling Row | File(s) Affected |
```

**For change specs**: Tag all new/modified items with (NEW), (MODIFIED), or (EXISTING — context only).

---

## Quality Checklist

Before outputting a spec, verify:

**All levels**:
- [ ] Spec Header includes Spec Level, Change Type, and Spec ID
- [ ] Business Rules section exists with numbered BR-xx rules
- [ ] Every conditional step in Main Logic references a BR-xx
- [ ] Main Logic is step-based — no prose paragraphs
- [ ] No invented names (file, field, program, data structure) — all unknowns are marked TBD
- [ ] All 4 mandatory error categories present (Validation Error, Data Not Found, Update Failure, System Error)
- [ ] Open Questions table lists every TBD with source section
- [ ] Spec Summary counts are accurate

**L2 and L3 only**:
- [ ] Functions describe WHAT only — no logic steps
- [ ] Data Contract lists every field with source, storage, and step references
- [ ] Interface Contract defines all parameters with type, length, and valid values
- [ ] Traceability Matrix includes every BR-xx with no gaps

**L3 only**:
- [ ] Caller Context is populated or explicitly TBD
- [ ] External Program Calls include parameters and expected returns
- [ ] All REQUIRED sections are present (none omitted)

**Change specs (any level)**:
- [ ] Business Rules are tagged (NEW) / (MODIFIED) / (EXISTING — context only)
- [ ] Main Logic steps are tagged (NEW) / (MODIFIED) / (EXISTING — context only)
- [ ] Data Contract fields are tagged (NEW) / (MODIFIED) / (EXISTING — unchanged) (L2/L3)

---

## Common Patterns in Specs

### Error Return Codes

All specs include these four mandatory error categories:

| Scenario | Return Code | Action | Logged? |
|----------|-------------|--------|---------|
| Validation Error | (typically 1) | Stop processing, return error message to caller | Yes |
| Data Not Found | (typically 2) | Return indicator or code, may retry or log | Yes |
| Update Failure | (typically 3) | Rollback, return error to caller | Yes |
| System Error | (typically 9) | Log critical error, return system error code | Yes |

Success is typically return code 0.

### File Operations

File usage sections name the file, type (Input/Output/Update), and key field(s):

| File Name | Type | Key Field(s) | Description |
|-----------|------|-------------|-------------|
| INVFILE | I | INVNO, CUSNO | Invoice master file |
| ORDERF | O | ORDNO | Order output file |

### Parameters

Interface Contract parameters specify type, length, valid values, and direction:

| Name | Type | Length | Input/Output | Valid Values | Description |
|------|------|--------|-------------|--------------|-------------|
| CUSTID | CHAR | 10 | Input | Valid customer ID or blank | Customer ID lookup parameter |
| RETCODE | CHAR | 1 | Output | 0=Success, 1=Error, 2=Not Found | Program return code |

---

## When to Reference Each File

- **Need to understand spec level selection?** → Read `tier-guide.md` (Decision Examples table)
- **Need to know what belongs in a specific section?** → Read `section-guide.md`
- **Want to see a complete spec example?** → Review `sample-rpgle-spec.md` or `sample-clle-spec.md`
- **Working with a change (L1) spec?** → Review `sample-lite-spec.md` for tagging patterns
- **Need to understand V2.5 breaking changes?** → Read `v25-upgrade-analysis.md`
- **Implementing the skill itself?** → Modify `SKILL.md`

---

## Development Notes

- This is a specification-generation skill, not code-generation. Output is documentation, not source.
- The skill should NEVER generate RPG code, CL code, or data structure definitions. If a user asks for code, clarify that this skill produces specs, and suggest a code-generation skill instead.
- Specs are inputs to a developer or a code-generation tool—not finished deliverables for implementation.
- All unknowns must be marked TBD with a corresponding entry in Open Questions. Never guess or invent.

---

## Testing Specs in Practice

Specs are correct when:
1. A developer can read the spec and implement the program without asking for clarification
2. A QA team can generate test cases from the Business Rules and Traceability Matrix
3. The spec accurately reflects the requirement without invented details
4. All TBDs have been resolved or explicitly documented as out-of-scope

---

