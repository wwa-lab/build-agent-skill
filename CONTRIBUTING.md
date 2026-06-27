# Contributing to Atlas Engineering Delivery Hub - Build

Thank you for helping improve the M4 Build tool. This repo is a skill-family package, so a good contribution usually improves build repeatability, traceability, examples, validation, or documentation for IBM i delivery work.

## Who Should Contribute

Good contributors include:

- IBM i developers working with RPGLE, CLLE, DDS, DB2 for i, or legacy enhancement delivery.
- Business analysts and solution architects who can improve spec structure and layer boundaries.
- Testers who can improve UT plans, SQL/CL scaffolds, and validation examples.
- AI workflow maintainers who can improve Claude Code skill packaging, prompts, and regression checks.
- Documentation contributors who can make the M4 Build story easier to understand and reuse.

## Good First Contribution Areas

- Add sanitized examples for common IBM i change types.
- Improve `docs/samples/atlas-build-tool-mini-output/` with clearer before/after evidence.
- Add or refine test cases under existing `tests/cases/` directories.
- Improve reviewer examples for code, DDS, compile precheck, or spec review findings.
- Clarify README, Chinese README, or submission docs where wording is ambiguous.
- Add Mermaid diagram improvements under `docs/assets/`.

## Skill And Tool Extension Areas

When changing skill behavior, edit the relevant `.claude/<skill-name>/SKILL.md` and keep the skill's lane narrow:

- Generation skills should generate their intended artifact only.
- Review skills should review and report findings, not rewrite artifacts.
- Orchestration should route, plan, and gate; it should not replace generation or review skills.
- Code generation must stay spec-first and should not invent missing IBM i object names, file layouts, or business rules.
- DDS generation must treat File Spec JSON as the controlling contract.

For new build patterns or templates:

1. Start from a sanitized, non-customer example.
2. State the input artifact and controlling source of truth.
3. Show expected output shape, not confidential implementation detail.
4. Include traceability markers such as BR-xx, Step IDs, fileSpecRef, or fieldRef where relevant.
5. Add a review or validation checklist so users know when the pattern is safe to reuse.

## Documentation Rules

- Keep the default repo entry in English and maintain `README.zh-CN.md` as the natural Chinese companion.
- Do not duplicate long technical material in multiple places. Link to `docs/full-reference-readme.md` or skill-local references when possible.
- Use "planned", "placeholder", or "TBD" for capabilities that are not already present.
- Do not describe this repo as the whole Atlas Engineering Delivery Hub. It is the M4 Build tool.
- Mention Atlas Phoenix Lens only as an upstream M3 Discovery capability when it helps explain lifecycle context.

## Testing And Validation Expectations

Before opening a PR, run the light checks that fit the change:

```bash
git diff --check
```

For Markdown-heavy changes, check relative links. A simple local script is acceptable as long as it ignores external URLs and generated binary assets.

For skill behavior changes, use the nearest existing harness:

```bash
.claude/ibm-i-dds-generator/tests/runner.sh --list
.claude/ibm-i-code-generator/tests/runner.sh --dry-run
.claude/ibm-i-test-scaffold/tests/runner.sh --list
```

Running full harnesses may call the `claude` CLI and consume model calls. Include what you actually ran in the PR.

## Data Safety And Secrets

- Never commit customer data, production source members, credentials, screenshots with sensitive system names, API keys, passwords, tokens, or internal hostnames.
- Use sanitized object names such as `CUSTMNT`, `CUSTMAST`, `ORDHDR`, `TESTLIB`, or `SAMPLELIB`.
- Keep generated examples small and clearly marked as sample material.
- If a secret or confidential artifact is accidentally committed, stop, remove it, notify the maintainer, and rotate the exposed credential if applicable.

## Pull Request Checklist

- The change fits the M4 Build scope.
- Skill changes preserve layer boundaries and anti-hallucination rules.
- Examples are sanitized and contain no customer data.
- Documentation links are valid.
- Tests or dry runs are listed in the PR.
- New patterns include enough context for human review.
- No generated binary churn is included unless it is intentional and explained.

## Commit Message Style

Use conventional commits:

```text
docs: improve atlas build sample package
test: add dds generator regression case
feat: add task.md planning example
fix: correct file spec validation wording
chore: refresh diagram assets
```

Keep commits focused. If one contribution changes both skill behavior and docs, explain the relationship in the PR summary.
