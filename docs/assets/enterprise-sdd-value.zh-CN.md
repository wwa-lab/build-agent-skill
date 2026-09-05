# Enterprise SDD README value visual

- Asset: [enterprise-sdd-value.zh-CN.png](enterprise-sdd-value.zh-CN.png)
- Generation: built-in imagegen tool, 2026-09-06.
- Purpose: Chinese value overview at the top of both README files; red, white and black visual style.
- Evidence source: [value narrative](../atlas-engineering-delivery-hub-build-pitch.md) and [synthetic POC](../cases/rpgle-flow-poc/README.md).
- Brand name deliberately omitted while naming is under discussion. Existing brand and registered team name remain unchanged.
- Evidence boundary: static artifacts and checks only; no compilation, business execution or measured productivity claim.

## Generation prompt

```text
Use case: infographic-diagram.
Create a finished, premium Chinese README hero infographic for an enterprise Spec-Driven Development project, to communicate the value to nontechnical colleagues and competition judges in five seconds. Wide landscape 16:9. Crisp editorial flat vector-like graphics rendered as a raster image, generous whitespace, white background, near-black ink and strong corporate red (#DB0011) as the only accent. Confident, minimal, restrained enterprise design. No gradients, no neon, no robots, no photography, no logos.
Main headline upper left, very large bold Chinese sans-serif: “让 AI 交付，有据可查。”
Small eyebrow above: “企业级 SDD 实践”
Subheadline: “把业务意图，连接到可追溯的实现与评审证据”

Below headline a very clear left-to-right visual transformation across three panels with thin red arrows connecting them. The middle panel is subtly wider and highlighted by a red top edge. Use simple document, connected-contract and evidence-stack iconography, one intentional line connecting requirements to implementation, avoiding intricate architecture.
Left panel label “现状问题”, large title “知识分散，反复确认”, two short lines “需求、代码与团队经验分离” and “规则有没有落实，难以核对”.
Middle panel label “SDD 方法”, large title “让规范连接每一步”, three compact visible steps “需求澄清 → 分层规格 → 生成与评审”, supporting line “按改动选路径 · 将领域经验写入 Skills”, second supporting line “规则编号贯穿 · 人工确认与检查反馈”.
Right panel label “交付价值”, large title “依据可查，经验可复用”, two short lines “沿业务规则追踪规格与源码” and “留下可复核、可交接的交付材料”.

At bottom a sharply separated near-black evidence strip with white type and small red accents, not exaggerated promotional metrics. Left small label “首个实践锚点：IBM i”; middle “16 个领域 Skills”; right “合成 POC：32 条规则 · 94 个步骤静态定位”.
Under evidence strip a single small but readable gray line on white: “当前证据为静态检查；编译、业务运行与效率收益待验证。”

Use ONLY the quoted Chinese text and SDD / Skills / IBM i / POC present above. Keep Chinese perfectly accurate and legible. Every panel needs sufficient padding. This is an executive value story, not a software UI screenshot. No invented productivity percentages or claims of completed business validation. The project name is intentionally omitted while naming is being discussed.
```
