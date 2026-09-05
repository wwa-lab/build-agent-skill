# CodeCup 2026 Submission: Atlas Engineering Delivery Hub – Build

This submission summarizes the maintained [Situation → Solution → Result narrative](atlas-engineering-delivery-hub-build-pitch.md). The narrative and case library hold the evidence and measurement approach.

## Project And Value

**Atlas Engineering Delivery Hub – Build** helps IBM i developers, reviewers and delivery teams turn business requirements and existing-code knowledge into implementation work with clear evidence and familiar maintenance conventions.

It is a reusable domain-skill toolkit within Atlas's M4 Build stage. The intended improvements are lower human effort, less rework and better handoffs. Current evidence consists of implemented workflow rules and a synthetic POC with static checks.

## 1. Situation — Why Build This?

An IBM i change requires knowledge of existing logic, file layouts, calling contracts and team conventions. The [pilot retrospective](../article-pilot-retrospective.md) records direct usability feedback: a full specification chain burdened small changes; generated fixed-format code lacked familiar structure, comments and naming; existing-code analysis and test preparation were absent from the workflow.

Developers and reviewers therefore still needed to supply context and organize generated artifacts. The pilot provides qualitative feedback, without a measured effort baseline or an estimate of team-wide cost.

## 2. Solution — How Is The Approach Distinctive?

| Design | Problem addressed | Intended value |
| --- | --- | --- |
| A qualified Mini Requirement → Program Spec fast path | Reprocessing known information through every document layer | Fit the preparation work to the change while retaining necessary specifications |
| Existing source, layered specs, file/parameter contracts and maintenance style guide generation | Missing system and team context | Make domain knowledge reusable and reviewable |
| BR traceability, cross-spec references, review rules and static checks accompany artifacts | Reconstructing the relationship between requirements and implementation | Provide concrete implementation evidence and correction records |

The distinctive proposition is this combination for IBM i delivery. SDD and Skills are methods the project adopts; it does not claim to have invented them or demonstrated superiority over competing products.

The 16 skills cover analysis, specifications, program/DDS generation, reviews, developer-test preparation and orchestration protocols. They support the solution; the presentation begins with the problem and mechanisms.

## 3. Result — What Is Established, And What Benefit Is Expected?

The [synthetic order-fulfillment and settlement POC](cases/rpgle-flow-poc/README.md) contains requirements, design, 8 program specs, 24 file specs and 35 source files. Its 32 business rules link to specification steps and source locations; 94 steps have static locations. Review records document corrections involving message overwrites, duplicate declarations, rollback results and duplicate-request queries.

These results show use of the workflow in a case with cross-program and cross-file dependencies, producing readable, traceable implementation material that can be checked locally. Static results do not establish business correctness, and not every recorded issue is attributable to an automated tool.

| Evidence level | Current status |
| --- | --- |
| Workflow rules, domain references, specifications and source case | Available |
| Static traceability, contract checks and recorded corrections | Available, with stated limitations |
| Human-effort, rework and handoff improvements | Await real-case comparisons |
| IBM i compilation, business execution and model evaluation | Not recorded for this POC |

Next, an anonymized real enhancement with clear acceptance criteria will record total human effort, rework categories, correction rounds and independent review quality, followed by compilation and regression evidence. The [measurement approach](atlas-engineering-delivery-hub-build-pitch.md#希望带来的收益与下一步测量) includes baseline conditions and post-generation human correction work.

## Presentation Flow

1. Situation: open with the small-change friction from the pilot.
2. Solution: connect each of the three mechanisms to a problem.
3. Demo: follow BR-06 through the synthetic POC's specification, request ledger, source and correction record.
4. Result: show available evidence, then the intended benefits and real-case measurement plan.

## Collaboration And Scope

Contributors can add anonymized real cases, domain-rule corrections and reusable checks. Use the [case template](cases/case-template.md) to record the situation, actual solution and results. The project currently has no IBM i connector, deployment runtime or complete business-execution validation.

- [Maintained value narrative and short pitch](atlas-engineering-delivery-hub-build-pitch.md)
- [Case library and evidence](cases/README.md)
- [Full skill reference](full-reference-readme.md)
- [Contribution guide](../CONTRIBUTING.md)
