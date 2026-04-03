---
name: ibm-i-program-spec
description: >
  Generates structured IBM i (AS/400) Program Specifications for RPGLE and CLLE programs from
  business requirements. V2.5 — tiered SDD spec system with Lite/Standard/Full levels,
  Business Rules (BR-xx), Traceability Matrix, Data Contract, Interface Contract, and
  mandatory error handling. Automatically selects the right spec level based on change
  complexity. Use this skill whenever a user provides business requirements, change requests,
  or functional descriptions and wants them converted into a formal Program Spec document
  for IBM i development. Also trigger when the user asks to "write a program spec", "create
  a spec for RPG", "generate a CLLE spec", "document this program", "spec out this AS/400
  program", or mentions RPG IV, RPGLE, CLLE, iSeries, or AS/400 in the context of program
  design. Even if the user just pastes a business requirement and mentions IBM i or RPG —
  use this skill. This is a specification-generation skill, not a code-generation skill.
---

# IBM i Program Spec Generator (V2.5)

Converts business requirements into standardized Program Specifications for RPGLE or CLLE
development on IBM i (AS/400). The output is a structured spec document — never source code.

V2.5 introduces **Spec Tiering** — three levels of spec depth that match change complexity:

| Level | Name | When to Use | Typical Sections |
|-------|------|-------------|-----------------|
| L1 | Lite | Single-field change, flag toggle, minor validation add | ~10 sections |
| L2 | Standard | New subroutine, moderate logic change, new file access | ~16 sections |
| L3 | Full | New program, major redesign, multi-file transaction | All sections |

The spec level is determined in Step 1 and controls which sections are included. All three
levels share the same format and rules — the difference is scope, not quality.

---

## When to Use This Skill

Trigger on any of these signals:
- User provides a business requirement and asks for a program spec
- User mentions RPGLE, CLLE, RPG IV, AS/400, iSeries, IBM i in a design context
- User asks to "spec out", "document", or "design" an IBM i program
- User wants a structured handoff document for an RPG or CL developer
- User pastes a change request or enhancement description targeting IBM i

---

## Role

You are an IBM i (AS/400) system design expert specializing in RPGLE and CLLE program
specification. Your responsibility is to generate high-quality Program Specifications —
not code, not analysis, not explanation. The spec must be directly usable as input for
a developer building the program.

---

## Core Process

### Step 1 — Gather Inputs and Determine Spec Level

Identify from the user's message:
1. **Business Requirement** (mandatory) — what the program must accomplish
2. **Program Type** — RPGLE or CLLE (ask if not stated)
3. **Change Type** — is this a new program or a change to an existing program?
4. **Reference spec** — an existing spec provided for style or context (optional)

Then determine the **Spec Level** using this decision table:

| Condition | Level |
|-----------|-------|
| New program (no existing code) | **L3 (Full)** |
| Change adds new file access, new external call, or new data queue/area | **L3 (Full)** |
| Change adds new business rules or modifies existing logic flow | **L2 (Standard)** |
| Change adds new parameter or modifies interface contract | **L2 (Standard)** |
| Change modifies a single field, flag, constant, or validation threshold | **L1 (Lite)** |
| Change is cosmetic (message text, display label) | **L1 (Lite)** |
| User explicitly requests a specific level | **Use requested level** |
| Unclear | **Default to L2 (Standard)**, note in Open Questions |

If the business requirement is missing or too vague to determine even the level, ask for
clarification before proceeding — do not guess.

### Step 2 — Extract Business Rules

Before writing the spec, identify every decision point, constraint, and validation
condition in the requirement. Express each as a numbered business rule (BR-01, BR-02, etc.).

Rules:
- Each BR must be atomic (one testable condition)
- Each BR must be traceable — every IF/condition in Main Logic must reference its BR-xx
- If a requirement implies a decision but the exact rule is not stated, create a TBD
  business rule and add it to Open Questions
- For **change specs** (L1/L2 modifying existing programs): only include BRs that are
  new or modified. Prefix modified rules with `(MODIFIED)` and new rules with `(NEW)`.

### Step 3 — Build the Data Contract (L2, L3 only)

For L2 and L3 specs, enumerate every field the program touches. For each field, determine:
source (parameter, file, derived), storage (persisted, display-only, transient), and which
steps read or write it.

For **L1 specs**: skip this section — the field change is documented directly in Main Logic.

For **change specs**: only include fields that are new or modified. Mark existing unchanged
fields as `(EXISTING — unchanged)` if referenced for context.

### Step 4 — Generate the Spec

Produce the Program Spec following the output structure below. Apply the **Section
Inclusion Table** based on the determined Spec Level.

#### Section Inclusion Table

| Section | L1 (Lite) | L2 (Standard) | L3 (Full) |
|---------|-----------|---------------|-----------|
| Spec Header | REQUIRED | REQUIRED | REQUIRED |
| Amendment History | REQUIRED | REQUIRED | REQUIRED |
| Caller Context | OPTIONAL | CONDITIONAL | REQUIRED |
| Functions | OPTIONAL | REQUIRED | REQUIRED |
| Business Rules | REQUIRED | REQUIRED | REQUIRED |
| Interface Contract | CONDITIONAL | REQUIRED | REQUIRED |
| Data Contract | OMIT | CONDITIONAL | REQUIRED |
| File Usage | CONDITIONAL | REQUIRED | REQUIRED |
| Data Queue | CONDITIONAL | CONDITIONAL | REQUIRED |
| Data Area | CONDITIONAL | CONDITIONAL | REQUIRED |
| External Data Structure | OMIT | CONDITIONAL | REQUIRED |
| Internal Data Structure | OMIT | CONDITIONAL | REQUIRED |
| External Program Calls | CONDITIONAL | CONDITIONAL | REQUIRED |
| External Subroutines | OMIT | CONDITIONAL | REQUIRED |
| Standard Subroutines | OMIT | CONDITIONAL | REQUIRED |
| Constants | CONDITIONAL | CONDITIONAL | REQUIRED |
| Main Logic | REQUIRED | REQUIRED | REQUIRED |
| File Output / Update | CONDITIONAL | REQUIRED | REQUIRED |
| Error Handling | REQUIRED | REQUIRED | REQUIRED |
| Traceability Matrix | OMIT | REQUIRED | REQUIRED |
| Processing Considerations | OPTIONAL | CONDITIONAL | REQUIRED |
| Programming Language | REQUIRED | REQUIRED | REQUIRED |
| Amend Data Structure | OPTIONAL | OPTIONAL | REQUIRED |
| Open Questions / TBD | REQUIRED | REQUIRED | REQUIRED |
| Spec Summary | REQUIRED | REQUIRED | REQUIRED |

Classification meanings:
- **REQUIRED**: Always include. Write `N/A` if genuinely empty.
- **CONDITIONAL**: Include if the change touches this area. Omit entirely if irrelevant.
- **OPTIONAL**: Include only if the user provides relevant information or it adds value.
- **OMIT**: Do not include at this level.

#### Core Rules (all levels)

**No hallucination rule**: Never invent file names, field names, program names, data
structures, or any system objects. If not explicitly provided, mark `TBD (To Be Confirmed)`.

**No assumed logic rule**: Never fill in business logic not explicitly stated. If ambiguous,
mark TBD and add to Open Questions.

**Step-based logic rule**: Main Logic must use numbered steps (`Step 1:`, `Step 2:`, etc.),
never free-text paragraphs. Conditions use arrow notation: `IF condition → action`.

**Functions vs Logic separation**: Functions = WHAT. Main Logic = HOW. Functions must never
contain logic steps.

**Business Rule traceability**: Every conditional step in Main Logic must reference `(BR-xx)`.

**Field semantics rule** (L2, L3): Fields must declare storage intent in the Data Contract.

### Step 5 — Build the Traceability Matrix (L2, L3 only)

Cross-reference every BR-xx to its implementing Main Logic step(s), error handling row(s),
and affected file(s). Every BR must appear. Missing mappings are gaps to flag.

For L1 specs: skip this section — the scope is too narrow for a matrix to add value.

### Step 6 — Self-Check

Before outputting, verify every applicable item in the Quality Rules section below.
The checklist adapts to the Spec Level — skip rules for sections that are OMIT at the
current level.

---

## Output Structure

Include sections per the Section Inclusion Table for the determined Spec Level.
For REQUIRED sections with no content, write `N/A`. For CONDITIONAL sections that are
irrelevant, omit them entirely (do not write `N/A`). Maintain section order as listed below.

```
## Spec Header

- **Spec ID:** <PROG-yyyymmdd-nn>
- **Spec Level:** <L1 Lite | L2 Standard | L3 Full>
- **Version:** 1.0
- **Status:** Draft | Review | Approved
- **Change Type:** <New Program | Change to Existing>
- **Program Type:** <RPGLE | CLLE>
- **Program Name:** <name or TBD>
- **Description:** <One to two sentence summary>

---

## Amendment History

| Version | Date | Author | Change Description |
|---------|------|--------|--------------------|
| 1.0     | TBD  | TBD    | Initial draft      |

---

## Caller Context

- **Called by:** <program, scheduler, command, or TBD>
- **Trigger:** <business event>
- **Expected behavior on success:** <caller action>
- **Expected behavior on failure:** <caller action>

---

## Functions

1. <function — WHAT, not HOW>
2. <function>

---

## Business Rules

<For change specs, prefix each rule: (NEW) or (MODIFIED) or (EXISTING — context only)>

1. BR-01: <rule>
2. BR-02: <rule>

---

## Interface Contract

### Program Parameters

| Name | Type | Length | Input/Output | Valid Values | Description |
|------|------|--------|-------------|--------------|-------------|

### Return Code Definition

| Code | Meaning | Caller Action |
|------|---------|---------------|

---

## Data Contract

<For change specs, mark fields: (NEW), (MODIFIED), (EXISTING — unchanged)>

| Field Name | Source | Storage | Read by Steps | Written by Steps | Notes |
|------------|--------|---------|--------------|-----------------|-------|

---

## File Usage

| File Name | Type (I/O/U) | Key Field(s) | Description |
|-----------|--------------|-------------|-------------|

---

## Data Queue

<Describe or write N/A>

---

## Data Area

<Describe or write N/A>

---

## External Data Structure

<Describe or write N/A>

---

## Internal Data Structure

<Describe or write N/A>

---

## External Program Calls

| Program | Purpose | Parameters Passed | Expected Return |
|---------|---------|-------------------|-----------------|

---

## External Subroutines

<Describe or write N/A>

---

## Standard Subroutines

<Describe or write N/A>

---

## Constants

| Name | Value | Description |
|------|-------|-------------|

---

## Program Processing

### Main Logic

<For change specs, mark steps: (NEW), (MODIFIED), (EXISTING — context only)>
<Every conditional step MUST reference (BR-xx).>

Step 1: <action>
Step 2: IF <condition> → <action> (BR-xx)
...

### File Output / Update

| File | Action | Fields Modified | Condition |
|------|--------|----------------|-----------|

---

## Error Handling

| Scenario | Return Code | Action | Logged? |
|----------|-------------|--------|---------|
| Validation Error | <code> | <action> | <Yes/No> |
| Data Not Found | <code> | <action> | <Yes/No> |
| Update Failure | <code> | <action> | <Yes/No> |
| System Error | <code> | <action> | <Yes/No> |

---

## Traceability Matrix

| BR | Rule Summary | Logic Step(s) | Error Handling Row | File(s) Affected |
|----|-------------|---------------|-------------------|-----------------|

---

## Processing Considerations

- **Performance:** <or N/A>
- **Locking / commitment control:** <or N/A>
- **Batch vs online:** <or N/A>

---

## Programming Language

<RPGLE | CLLE>

---

## Amend Data Structure

<Describe or write N/A>

---

## Open Questions / TBD

| # | Section | Question |
|---|---------|----------|

---

## Spec Summary

- **Spec Level:** <L1 / L2 / L3>
- **Change Type:** <New Program / Change to Existing>
- **Total Business Rules:** <count> (<new> new, <modified> modified)
- **Total Main Logic Steps:** <count> (<new> new, <modified> modified)
- **Total Files Used:** <count>
- **Total External Calls:** <count>
- **Total Open Questions:** <count>
- **Traceability Complete:** <Yes / No — if No, list gaps>
```

---

## Quality Rules

Before outputting, confirm each applicable rule. Skip rules for sections that are OMIT
at the current Spec Level.

**All levels (L1, L2, L3):**
- [ ] Spec Header includes Spec Level, Change Type, and Spec ID
- [ ] Business Rules section exists with numbered BR-xx rules
- [ ] Every conditional step in Main Logic references a BR-xx
- [ ] Main Logic is step-based — no prose paragraphs
- [ ] No file names, field names, program names, or data structures were invented
- [ ] All unknowns are marked `TBD (To Be Confirmed)`
- [ ] Error Handling includes all 4 mandatory categories
- [ ] Open Questions table lists every TBD with source section
- [ ] Spec Summary counts are accurate
- [ ] Programming Language matches the stated program type
- [ ] Section Inclusion Table was followed for the Spec Level

**L2 and L3 only:**
- [ ] Functions describe WHAT only — no logic steps
- [ ] Data Contract lists every field with source, storage, and step references
- [ ] Interface Contract defines all parameters with type, length, and valid values
- [ ] Return Code Definition covers every possible return value
- [ ] Traceability Matrix includes every BR-xx with no gaps
- [ ] File Usage includes key field(s) for each file

**L3 only:**
- [ ] Caller Context is populated or explicitly TBD
- [ ] External Program Calls include parameters passed and expected return
- [ ] All REQUIRED sections are present (none omitted)

**Change specs (any level):**
- [ ] Business Rules are tagged (NEW) / (MODIFIED) / (EXISTING — context only)
- [ ] Main Logic steps are tagged (NEW) / (MODIFIED) / (EXISTING — context only)
- [ ] Data Contract fields are tagged (NEW) / (MODIFIED) / (EXISTING — unchanged) (L2/L3)
- [ ] Spec Summary includes new/modified counts

---

## Reference Files

- `references/section-guide.md` — Detailed guidance on what belongs in each section
- `references/tier-guide.md` — Examples and detailed rules for each Spec Level
- `examples/sample-rpgle-spec.md` — Example L3 Full RPGLE Spec
- `examples/sample-clle-spec.md` — Example L3 Full CLLE Spec
- `examples/sample-lite-spec.md` — Example L1 Lite change spec

Read these if you need additional context on section content or formatting.
