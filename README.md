# Atlas Engineering Delivery Hub - Build

[简体中文](README.zh-CN.md) | [Competition Submission](docs/open-collaboration-submission.md) | [Full Skill Reference](docs/full-reference-readme.md)

![Atlas Engineering Delivery Hub mobile visual](docs/assets/atlas-engineering-delivery-hub-mobile-static.png)

**Atlas Engineering Delivery Hub - Build is the M4 Build-stage tool within the Atlas Engineering Delivery Hub. It helps teams convert structured delivery evidence into controlled implementation work.**

This repository packages a family of Claude Code skills for IBM i (AS/400) enterprise delivery. It is not the whole Atlas Engineering Delivery Hub framework. It is the M4 Build tool: the part of the lifecycle that turns approved requirements, design evidence, program specifications, and file contracts into implementation-ready artifacts, review gates, and developer test scaffolds.

```
M1 Planning -> M2 Estimation -> M3 Discovery -> [M4 Build] -> M5 Testing -> M6 Deployment -> M7 Maintenance
```

In the larger Atlas story, Atlas Phoenix Lens / Legacy Spec Factory is an upstream M3 Discovery capability. This repo is the downstream M4 Build capability for controlled build execution, especially for IBM i teams working with RPGLE, CLLE, DDS, DB2 for i, and legacy enhancement work.

## Current Delivery Scope

The repo currently delivers a **16-skill IBM i build pipeline** under `.claude/`. The skills cover two connected delivery chains:

- **Program Chain:** requirements/design evidence -> Program Spec -> RPGLE/CLLE source -> compile precheck -> code review -> unit test plan -> SQL/CL test scaffold.
- **File Chain:** technical design/file requirements -> File Spec with Markdown + JSON -> DDS source for PF/LF/PRTF/DSPF -> DDS review.
- **Analysis and Routing:** existing source analysis, impact analysis, spec review, and workflow orchestration including task.md planning and approved batch execution.

The repository contains skill definitions, references, examples, diagrams, and semi-automated test harnesses. It does **not** contain an application server, packaged binary, IBM i connector, or automatic deployment runtime.

## Main Capabilities

| Area | Skills | Delivered behavior |
|------|--------|--------------------|
| Intake and analysis | `ibm-i-requirement-normalizer`, `ibm-i-program-analyzer`, `ibm-i-impact-analyzer` | Normalize messy input, understand existing RPGLE/CLLE source, and assess source-level change impact. |
| Specification | `ibm-i-functional-spec`, `ibm-i-technical-design`, `ibm-i-program-spec`, `ibm-i-file-spec` | Produce tiered functional, technical, program, and DDS file specs with BR-xx traceability and TBD handling. |
| Build artifact generation | `ibm-i-code-generator`, `ibm-i-dds-generator` | Generate RPGLE/CLLE source from Program Specs and DDS source from File Spec JSON contracts. |
| Validation gates | `ibm-i-compile-precheck`, `ibm-i-spec-reviewer`, `ibm-i-dds-reviewer`, `ibm-i-code-reviewer` | Review compile safety, spec quality, DDS alignment, and code/spec alignment. |
| Developer testing | `ibm-i-ut-plan-generator`, `ibm-i-test-scaffold` | Produce unit test plans and executable SQL/CL scaffold scripts for setup, compile, execute, verify, and cleanup. |
| Orchestration | `ibm-i-workflow-orchestrator` | Route one-step workflows or generate/execute approved `task.md` batch plans. |

## Inputs And Outputs

Typical inputs:

- Raw change requests, tickets, emails, meeting notes, or mini requirements.
- Existing RPGLE/CLLE source members and change requests.
- Functional Specs, Technical Designs, Program Specs, File Specs, or File Spec JSON.
- Unit Test Plans or test scenarios for IBM i delivery.

Typical outputs:

- Normalized requirement packages.
- Functional Specs, Technical Designs, Program Specs, and File Specs.
- RPGLE or CLLE source drafts and DDS source members.
- Compile precheck, spec review, code review, and DDS review reports.
- Unit test plans and SQL/CL test scaffold packages.
- `task.md` batch plans with target artifacts, gates, execution log, open questions, and final manifest.

## How It Works

The Build tool uses evidence-preserving skill boundaries. Each skill has a narrow job and explicit rules in its own `SKILL.md`.

1. **Classify the current artifact stage.** The workflow orchestrator decides whether the team is holding raw input, discovery/design evidence, existing source, a spec, generated code, or a test plan.
2. **Generate only from the right source of truth.** Code generation requires a Program Spec. DDS generation requires File Spec JSON. Review skills assess artifacts and do not rewrite them.
3. **Preserve traceability.** Business rules keep BR-xx identifiers across layers. File Specs expose stable IDs so Program Specs can link to file and field references.
4. **Mark uncertainty instead of inventing detail.** Unknown object names, missing file layouts, and unresolved design facts become `TBD` or labeled inferences.
5. **Insert human gates.** Plan approval, spec approval, reviewer Critical findings, and unresolved blocking TBDs are explicit stop points.

See the architecture visuals:

- [Lifecycle positioning](docs/assets/atlas-build-lifecycle.mmd)
- [Internal workflow](docs/assets/atlas-build-internal-workflow.mmd)
- [Upstream/downstream relationship](docs/assets/atlas-build-upstream-downstream.mmd)

## Quick Start

1. Clone the repository.
2. Copy `.claude/` into an IBM i delivery repository that uses Claude Code skills, or keep this repo open as the skill reference workspace.
3. Start with the artifact you already have:

```text
Raw request -> ibm-i-requirement-normalizer
Existing source only -> ibm-i-program-analyzer
Existing source + CR -> ibm-i-impact-analyzer
Technical Design -> ibm-i-program-spec and/or ibm-i-file-spec
Program Spec -> ibm-i-code-generator, ibm-i-ut-plan-generator
File Spec JSON -> ibm-i-dds-generator
Generated source -> compile/code/DDS review skills
Unsure what next -> ibm-i-workflow-orchestrator
```

4. For batch delivery, ask the orchestrator to produce a `task.md` plan from an approved Program Spec or Technical Design. Review and approve the plan before execution mode.
5. Use the generated review reports, test scaffold, and final manifest as the build evidence package for downstream M5 Testing.

## Example Workflow

```text
M3 Discovery evidence
  -> Technical Design
  -> ibm-i-workflow-orchestrator Plan Mode
  -> approved task.md
  -> Program Spec and File Spec generation
  -> human Spec Approval Gate
  -> RPGLE/CLLE and DDS generation
  -> compile precheck, code review, DDS review
  -> UT Plan and SQL/CL test scaffold
  -> M5 Testing handoff
```

A small sanitized demo package is available at [docs/samples/atlas-build-tool-mini-output/](docs/samples/atlas-build-tool-mini-output/).

## Directory Overview

```text
.claude/                                  # 16 IBM i Claude Code skills
docs/assets/                              # Mermaid architecture/design visuals
docs/samples/atlas-build-tool-mini-output/ # Sanitized mini demo package
docs/full-reference-readme.md             # Preserved detailed technical README
docs/open-collaboration-submission.md     # English competition submission draft
docs/open-collaboration-submission.zh-CN.md # Chinese competition submission draft
docs/atlas-engineering-delivery-hub-build-index.md # Reviewer navigation index
docs/atlas-engineering-delivery-hub-build-pitch.md # Short pitch and demo script
OpenCode_IBMi_Skill_Family.pptx           # Existing presentation asset
```

## Key Documents

- [Reviewer index](docs/atlas-engineering-delivery-hub-build-index.md)
- [Open collaboration submission](docs/open-collaboration-submission.md)
- [中文提交材料](docs/open-collaboration-submission.zh-CN.md)
- [Pitch and demo story](docs/atlas-engineering-delivery-hub-build-pitch.md)
- [Contribution guide](CONTRIBUTING.md)
- [Original full skill reference](docs/full-reference-readme.md)
- [Workflow orchestrator skill](.claude/ibm-i-workflow-orchestrator/SKILL.md)
- [Task.md template](.claude/ibm-i-workflow-orchestrator/references/task-md-template.md)
- [Task.md execution protocol](.claude/ibm-i-workflow-orchestrator/references/task-md-execution-protocol.md)

## Validation And Test Harnesses

Three skills include semi-automated shell harnesses:

- `.claude/ibm-i-dds-generator/tests/runner.sh` with 31 DDS cases.
- `.claude/ibm-i-code-generator/tests/runner.sh` with 8 code-generation cases.
- `.claude/ibm-i-test-scaffold/tests/runner.sh` with 6 scaffold cases.

These harnesses invoke the `claude` CLI and then apply structural checks to generated output. They are useful for regression testing skill behavior, but they require an authenticated Claude CLI and can consume model calls.

## Evidence, Traceability, And Human Review

This tool is designed for controlled build work, not blind automation. It keeps upstream evidence visible, carries BR-xx continuity across layers, labels assumptions, and stops at human approval gates when the next action could change scope or create unsafe code. Generated source and DDS should always be reviewed by qualified IBM i developers before compile, integration, or production use.

## Roadmap

Near-term contribution areas:

- Add more sanitized end-to-end demo packages for common IBM i change patterns.
- Expand test harness coverage for orchestrator `task.md` planning and TD-driven batch flows.
- Add rendered SVG/PNG versions of Mermaid diagrams if a repo-safe renderer is standardized.
- Add more reviewer examples for compile-precheck and DDS/code review edge cases.
- Document migration guidance for using the skills in mixed Claude Code and Codex workflows.

Longer-term ideas:

- Package reusable templates for M4 Build evidence bundles.
- Add optional CI checks for Markdown links and test harness dry runs.
- Explore safe adapters for downstream M5 Testing handoff without adding deployment or credential handling to this repo.

## Contributing

Contributions are welcome from IBM i developers, business analysts, architects, testers, and AI workflow maintainers. Start with [CONTRIBUTING.md](CONTRIBUTING.md), and keep new build patterns evidence-based, sanitized, and reviewable.

## License

See [LICENSE](LICENSE).
