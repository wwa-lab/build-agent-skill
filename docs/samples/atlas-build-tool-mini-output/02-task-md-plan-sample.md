# task.md Sample: CUSTMNT Status Validation

## 1. Metadata

| Field | Value |
|-------|-------|
| task_id | `SAMPLE-M4-001` |
| mode | `program-spec-entry` |
| status | `approved` |
| approved_by | `Sample Reviewer` |

## 2. Inputs

| Input | Path |
|-------|------|
| mini_requirement | `01-mini-requirement.md` |
| existing_source | `TBD: source member path for CUSTMNT` |

## 3. Targets

| Target ID | Skill | Output | Depends On |
|-----------|-------|--------|------------|
| T1 | `ibm-i-program-spec` | `03-program-spec-excerpt.md` | mini requirement |
| T2 | `ibm-i-code-generator` | `04-generated-rpgle-excerpt.md` | T1 |
| T3 | `ibm-i-compile-precheck` | compile precheck report | T2 |
| T4 | `ibm-i-code-reviewer` | code review report | T1, T2 |
| T5 | `ibm-i-ut-plan-generator` | `05-ut-plan-excerpt.md` | T1 |
| T6 | `ibm-i-test-scaffold` | `06-test-scaffold-excerpt.md` | T5 |

## 4. Execution Policy

- Halt on Critical findings from compile precheck or code review.
- Preserve TBDs instead of inventing missing source paths.
- Do not execute SQL/CL scripts from this package automatically.

## 5. Gate Definitions

| Gate | Passing condition |
|------|-------------------|
| Spec Approval Gate | Program Spec reviewed and BR-01 traceability accepted. |
| Compile Precheck | No Critical compile-safety issues. |
| Code Review | No Critical spec-alignment issues. |

## 8. Manifest Excerpt

| Artifact | Status |
|----------|--------|
| Program Spec excerpt | generated |
| RPGLE excerpt | generated for review |
| UT Plan excerpt | generated |
| Test scaffold excerpt | generated |
