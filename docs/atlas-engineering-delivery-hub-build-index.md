# Atlas Engineering Delivery Hub - Build Index

This page is the fast navigation map for reviewers evaluating this repository as the **Atlas Engineering Delivery Hub - Build** Tool entry.

## One-Sentence Positioning

Enterprise SDD (Spec-Driven Development) in practice: turn business requirements, system knowledge and engineering constraints into a traceable, reviewable and verifiable delivery process. IBM i is the first in-depth practice context; the current 16 skills implement that domain, while transferability to other stacks remains to be validated.

## Start With Situation → Solution → Result

Read the [maintained value narrative](atlas-engineering-delivery-hub-build-pitch.md) first. It connects the [pilot's qualitative feedback](../article-pilot-retrospective.md) to three mechanisms—scope-aware routing, reusable domain rules and checking evidence—and separates established POC results from benefits awaiting real-case measurement.

See the [method and domain expansion approach](atlas-engineering-delivery-hub-build-pitch.md#领域扩展路线) for what must be adapted and verified before adding another technology context.

## Where It Fits

```
M1 Planning -> M2 Estimation -> M3 Discovery -> [M4 Build] -> M5 Testing -> M6 Deployment -> M7 Maintenance
```

- Umbrella: Atlas Engineering Delivery Hub.
- Lifecycle: Seven Mountains SDLC.
- Upstream example: Atlas Phoenix Lens / Legacy Spec Factory as M3 Discovery.
- This repo: M4 Build, focused on implementation artifacts and build readiness.

## What Is In The Repository

| Area | Location | What to look for |
|------|----------|------------------|
| Root entry | [README.md](../README.md) | Competition-facing project narrative and quick start. |
| Chinese entry | [README.zh-CN.md](../README.zh-CN.md) | Natural Chinese version of the project narrative. |
| Full reference | [docs/full-reference-readme.md](full-reference-readme.md) | Preserved technical README for the IBM i skill family. |
| Submission draft | [docs/open-collaboration-submission.md](open-collaboration-submission.md) | Reviewer-ready competition submission text. |
| Chinese submission | [docs/open-collaboration-submission.zh-CN.md](open-collaboration-submission.zh-CN.md) | Chinese submission companion. |
| Pitch | [docs/atlas-engineering-delivery-hub-build-pitch.md](atlas-engineering-delivery-hub-build-pitch.md) | Short pitch, demo story, and talking points. |
| Interactive deck | [Chinese HTML presentation](presentations/README.md) | 18 slides with scenario routing, BR-06 evidence and speaker notes. |
| Value evidence | [Pilot retrospective](../article-pilot-retrospective.md) | Qualitative feedback motivating workflow changes; no measured benefit baseline. |
| Contribution guide | [CONTRIBUTING.md](../CONTRIBUTING.md) | How contributors can safely extend the tool. |
| Architecture visuals | [docs/assets/](assets/) | Mermaid diagrams for lifecycle, workflow, and handoff. |
| Mini sample | [docs/samples/atlas-build-tool-mini-output/](samples/atlas-build-tool-mini-output/) | Sanitized representative input/output package. |
| Delivery cases | [docs/cases/README.md](cases/README.md) | Case registry and template for synthetic and anonymized real delivery cases. |
| Complete POC | [RPGLE Flow POC](cases/rpgle-flow-poc/README.md) | Pinned requirements, design, 8 program specs, 24 file specs, 35 source files, static evidence and frozen benchmark kit. |
| Skills | [.claude/](../.claude/) | The 16 Claude Code skills that implement the workflow surface. |

## Actual Capabilities Delivered

- 16 Claude Code skills for IBM i delivery.
- Program Chain for RPGLE/CLLE work.
- File Chain for DDS PF/LF/PRTF/DSPF work.
- Program and file generation from controlled specs.
- Review gates for specs, code, DDS, and compile safety.
- Unit test plan generation and SQL/CL test scaffold generation.
- Workflow orchestration with routing, Plan Mode, Execute Mode, and approved `task.md` packages.
- Semi-automated harnesses for DDS generation, code generation, and test scaffold checks.
- A complete synthetic POC snapshot with local structure/contract and evaluation-package checks.

## Current Maturity Notes

- This is a skill package and documentation repository, not a standalone SaaS or CLI product.
- The test harnesses call the `claude` CLI and require local authentication for full execution.
- Generated RPGLE/CLLE and DDS artifacts still require human IBM i review before compile, integration, or production use.
- There is no IBM i credential handling or deployment automation in this repo.
- The POC is Draft with static-check evidence only. IBM i compilation, business execution, model evaluation and productivity comparisons are not yet recorded; no real delivery cases have been added yet.

## Demo Path For Reviewers

1. Situation: start with enterprise AI delivery questions about context, constraints and review evidence; then read the IBM i [pilot feedback](../article-pilot-retrospective.md) as a concrete example.
2. Solution: use the [value narrative](atlas-engineering-delivery-hub-build-pitch.md) to connect each mechanism to a problem.
3. Demo: follow BR-06 through the [RPGLE Flow POC](cases/rpgle-flow-poc/README.md), including its source and correction records.
4. Result: inspect the static evidence and distinguish it from pending effort, rework, compilation and business-execution measurements.
5. Use the [case template](cases/case-template.md) to plan the next real-case comparison.
6. Consult the [lifecycle diagram](assets/atlas-build-lifecycle.mmd), [internal workflow](assets/atlas-build-internal-workflow.mmd) and [.claude/ibm-i-workflow-orchestrator/SKILL.md](../.claude/ibm-i-workflow-orchestrator/SKILL.md) as technical backup. The POC is not evidence that the entire orchestrated workflow was executed.
