# Atlas Engineering Delivery Hub - Build Pitch

## 30-Second Pitch

Atlas Engineering Delivery Hub - Build is the M4 Build-stage tool in the Atlas Engineering Delivery Hub. It helps IBM i teams move from approved discovery and design evidence into controlled implementation work: Program Specs, File Specs, RPGLE/CLLE and DDS generation, review gates, unit test plans, and SQL/CL test scaffolds.

It is not trying to be the whole SDLC. It focuses on one hard handoff: turning evidence into build-ready artifacts without losing traceability or inventing missing facts.

## The Reviewer Hook

Many AI coding demos start with "generate code from a prompt." This tool starts later and more safely: "generate and review build artifacts from approved evidence."

That distinction matters for legacy enterprise work. In IBM i environments, the risk is not only whether AI can write RPGLE or DDS. The risk is whether the generated artifact is tied to the right requirement, uses the right file contract, preserves existing source constraints, and leaves a reviewable trail for testing.

## Demo Flow

1. **Lifecycle placement:** show `M1 -> M2 -> M3 -> [M4 Build] -> M5 -> M6 -> M7`.
2. **Upstream evidence:** explain that M3 Discovery tools can produce requirements and design evidence.
3. **Build routing:** open the workflow orchestrator and show how it routes raw input, specs, source, file contracts, and review needs.
4. **Program Chain:** show Program Spec -> Code Generator -> Compile Precheck -> Code Review -> UT Plan -> Test Scaffold.
5. **File Chain:** show File Spec JSON -> DDS Generator -> DDS Review.
6. **Batch plan:** show `task.md` as the approved work package with targets, gates, log, open questions, and manifest.
7. **Handoff:** show the mini sample package as the M4 evidence bundle for M5 Testing.

## What To Emphasize

- The repo is an M4 Build tool, not a whole framework.
- The tool is evidence-first and review-first.
- It is reusable because it packages build patterns, not confidential implementation.
- It fits open collaboration because teams can add sanitized patterns, examples, tests, and review rules.
- It keeps humans in control at approval and Critical-finding gates.

## Suggested Slide Outline

1. Project title and M4 Build positioning.
2. Problem: discovery-to-build handoff breaks traceability.
3. Solution: IBM i skill family for controlled implementation work.
4. Lifecycle diagram with M4 highlighted.
5. Internal workflow diagram.
6. Mini sample: input -> task.md -> generated artifacts -> validation.
7. Collaboration areas and roadmap.

## Short Chinese Pitch

Atlas Engineering Delivery Hub - Build 是 Atlas Engineering Delivery Hub / Seven Mountains SDLC 中的 M4 Build 工具。它不替代整个研发流程，而是专注解决一个关键断点：如何把已经批准的需求和设计证据，转化为可追踪、可评审、可测试的实现工作。

当前仓库提供 16 个面向 IBM i 的 Claude Code Skills，覆盖 Program Chain、File Chain、评审门禁、单元测试计划、SQL/CL 测试脚手架和 `task.md` 批处理计划。它的价值不是盲目生成代码，而是在生成前后都保留证据、边界和人工审查。
