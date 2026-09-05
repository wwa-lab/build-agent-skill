# CodeCup 2026 Submission: Atlas Engineering Delivery Hub – Build

This submission summarizes the maintained [Situation → Solution → Result narrative](atlas-engineering-delivery-hub-build-pitch.md). The narrative and case library hold the evidence and measurement approach.

## Project And Value

**Atlas Engineering Delivery Hub – Build** explores enterprise SDD (Spec-Driven Development) in practice: turning business requirements, system knowledge and engineering constraints into a traceable, reviewable and verifiable delivery process.

IBM i is the first in-depth practice context. The current toolkit supports that domain; other technology environments await adaptation and validation. It is a reusable domain-skill toolkit within Atlas's M4 Build stage. The intended improvements are lower human effort, less rework and better handoffs. Current evidence consists of implemented workflow rules and a synthetic POC with static checks.

## 1. Situation — Why Build This?

Enterprise AI delivery needs explicit business context, system constraints and rule traceability so developers and reviewers can assess generated work.

In the first IBM i context, a change requires knowledge of existing logic, file layouts, calling contracts and team conventions. The [pilot retrospective](../article-pilot-retrospective.md) records direct usability feedback: a full specification chain burdened small changes; generated fixed-format code lacked familiar structure, comments and naming; existing-code analysis and test preparation were absent from the workflow.

Developers and reviewers therefore still needed to supply context and organize generated artifacts. The pilot provides qualitative feedback, without a measured effort baseline or an estimate of team-wide cost.

## 2. Solution — How Is The Approach Distinctive?

| Design | Problem addressed | Intended value |
| --- | --- | --- |
| A qualified Mini Requirement → Program Spec fast path | Reprocessing known information through every document layer | Fit the preparation work to the change while retaining necessary specifications |
| Existing source, layered specs, file/parameter contracts and maintenance style guide generation | Missing system and team context | Make domain knowledge reusable and reviewable |
| BR traceability, cross-spec references, review rules and static checks accompany artifacts | Reconstructing the relationship between requirements and implementation | Provide concrete implementation evidence and correction records |

The distinctive proposition is to carry enterprise constraints through SDD and test the method through domain cases. The table describes its first IBM i implementation. SDD and Skills are methods the project adopts; it does not claim to have invented them or demonstrated superiority over competing products.

The current 16 IBM i skills cover analysis, specifications, program/DDS generation, reviews, developer-test preparation and orchestration protocols. They support the solution; the presentation begins with the problem and mechanisms.

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

1. Situation: open with enterprise delivery questions about context, constraints and review evidence, then ground them in the IBM i pilot.
2. Solution: connect each of the three mechanisms to a problem.
3. Demo: follow BR-06 through the synthetic POC's specification, request ledger, source and correction record.
4. Result: show available evidence, then the intended benefits and real-case measurement plan.

## Collaboration And Scope

Contributors can add anonymized real cases, domain-rule corrections and adaptations for new technology contexts. The [domain expansion approach](atlas-engineering-delivery-hub-build-pitch.md#领域扩展路线) requires domain contracts, generation/review rules and actual validation tools. Use the [case template](cases/case-template.md) to record the situation, actual solution and results. The project currently has no IBM i connector, deployment runtime or complete business-execution validation.

- [Maintained value narrative and short pitch](atlas-engineering-delivery-hub-build-pitch.md)
- [Case library and evidence](cases/README.md)
- [Full skill reference](full-reference-readme.md)
- [Contribution guide](../CONTRIBUTING.md)
