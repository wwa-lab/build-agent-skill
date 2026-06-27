# Open Collaboration Submission: Atlas Engineering Delivery Hub - Build

## Project

**Atlas Engineering Delivery Hub - Build**

Category: **Tool**

Repository: `wwa-lab/build-agent-skill`

## Summary

Atlas Engineering Delivery Hub - Build is the M4 Build-stage tool within the Atlas Engineering Delivery Hub / Seven Mountains SDLC. It helps teams convert structured delivery evidence into controlled implementation work.

The current implementation is a Claude Code skill family for IBM i enterprise delivery. It packages 16 specialized skills that turn requirements, design evidence, Program Specs, and File Specs into implementation-ready outputs: RPGLE/CLLE source drafts, DDS source, review reports, unit test plans, SQL/CL test scaffolds, and orchestrated `task.md` build plans.

## Problem It Solves

Enterprise IBM i work often fails at the handoff between discovery/design and build:

- Requirements are captured, but developers still need to reconstruct implementation detail.
- Legacy RPGLE/CLLE changes need careful source analysis before modification.
- File definitions, DDS source, and program logic drift when they are generated from different assumptions.
- Review gates are inconsistent across specs, code, DDS, and test evidence.
- Build evidence is hard to package for downstream testing.

This tool makes M4 Build repeatable by keeping evidence, traceability, generation, review, and test planning in one skill family.

## Why It Is Reusable Across Teams

The repo is reusable because it packages patterns rather than project-specific implementation:

- Skills are stored as text under `.claude/`, so teams can inspect and adapt them.
- Examples are sanitized and generic.
- File and program workflows are separated but interoperable.
- The `task.md` model gives teams a portable batch plan with targets, gates, logs, open questions, and final manifest.
- The safety posture is explicit: unknowns become TBDs, generation requires the right spec, and human review gates are non-bypassable.

## Fit With Open Collaboration

This repository is well-suited to open collaboration because many teams face the same build-stage problems:

- Common IBM i object types and delivery artifacts can be contributed as examples.
- Review rules can be refined from real delivery experience without exposing customer data.
- Test harness cases can grow incrementally.
- Contributors can add new build patterns, mini requirements, and `task.md` templates.
- Bilingual documentation makes the project easier to evaluate, adopt, and co-build.

## What Is Already Delivered

- 16 IBM i Claude Code skills.
- Program Chain: requirement/design evidence -> Program Spec -> RPGLE/CLLE -> compile precheck -> code review -> UT plan -> SQL/CL scaffold.
- File Chain: Technical Design/File Spec -> File Spec JSON -> DDS source -> DDS review.
- Workflow Orchestrator with Routing Mode, Plan Mode, and Execute Mode.
- `task.md` template and execution protocol.
- Examples across specs, code generation, DDS generation, test scaffolds, review outputs, and orchestrator plans.
- Semi-automated harnesses for DDS generation, code generation, and test scaffold generation.
- Reviewer-facing docs, bilingual README, contribution guide, diagrams, and sanitized mini sample package.

## What Contributors Can Co-Build

- More sanitized IBM i change patterns.
- More DDS, RPGLE, CLLE, and test scaffold regression cases.
- Better orchestrator examples for multi-program or program+file changes.
- Rendered diagram assets and CI checks for Markdown links.
- Additional reviewer rule examples for common enterprise failure modes.
- Guidance for teams that want to pair M3 Discovery evidence with M4 Build packages.

## Demo Story

1. Start with a small IBM i enhancement requirement: add a status validation to a customer maintenance flow.
2. Show how the Build tool treats upstream discovery/design evidence as the source of truth.
3. Use the lifecycle diagram to place the repo in M4 Build.
4. Walk through the internal workflow diagram: specs, code/DDS generation, review gates, UT plan, test scaffold.
5. Open the mini sample package and show representative input, `task.md`, generated excerpts, and validation artifacts.
6. Close with the handoff: M4 Build produces a controlled evidence package for M5 Testing, not an unreviewed production deployment.

## Boundaries

- This repo is not the whole Atlas Engineering Delivery Hub.
- It does not replace M3 Discovery.
- It does not connect to IBM i systems or deploy code.
- It does not claim generated code is production-ready without human review.
- It does not handle secrets or customer data.

## Review Checklist

- [README.md](../README.md) explains the M4 Build positioning.
- [README.zh-CN.md](../README.zh-CN.md) provides a Chinese companion.
- [CONTRIBUTING.md](../CONTRIBUTING.md) defines safe contribution rules.
- [docs/assets/](assets/) contains architecture visuals.
- [docs/samples/atlas-build-tool-mini-output/](samples/atlas-build-tool-mini-output/) contains a sanitized sample.
- [docs/full-reference-readme.md](full-reference-readme.md) preserves the deeper technical material.
