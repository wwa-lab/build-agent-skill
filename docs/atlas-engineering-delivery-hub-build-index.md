# Atlas Engineering Delivery Hub - Build Index

This page is the fast navigation map for reviewers evaluating this repository as the **Atlas Engineering Delivery Hub - Build** Tool entry.

## One-Sentence Positioning

Atlas Engineering Delivery Hub - Build is the M4 Build-stage tool within the Atlas Engineering Delivery Hub. It helps IBM i teams convert structured delivery evidence into controlled implementation work.

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
| Contribution guide | [CONTRIBUTING.md](../CONTRIBUTING.md) | How contributors can safely extend the tool. |
| Architecture visuals | [docs/assets/](assets/) | Mermaid diagrams for lifecycle, workflow, and handoff. |
| Mini sample | [docs/samples/atlas-build-tool-mini-output/](samples/atlas-build-tool-mini-output/) | Sanitized representative input/output package. |
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

## Current Maturity Notes

- This is a skill package and documentation repository, not a standalone SaaS or CLI product.
- The test harnesses call the `claude` CLI and require local authentication for full execution.
- Generated RPGLE/CLLE and DDS artifacts still require human IBM i review before compile, integration, or production use.
- There is no IBM i credential handling or deployment automation in this repo.

## Demo Path For Reviewers

1. Read the top of [README.md](../README.md) for M4 positioning.
2. Open [docs/assets/atlas-build-lifecycle.mmd](assets/atlas-build-lifecycle.mmd) to see lifecycle placement.
3. Open [docs/assets/atlas-build-internal-workflow.mmd](assets/atlas-build-internal-workflow.mmd) to understand the tool chain.
4. Review [docs/samples/atlas-build-tool-mini-output/](samples/atlas-build-tool-mini-output/) for a safe mini example.
5. Inspect [.claude/ibm-i-workflow-orchestrator/SKILL.md](../.claude/ibm-i-workflow-orchestrator/SKILL.md) and the task.md references for the batching model.
