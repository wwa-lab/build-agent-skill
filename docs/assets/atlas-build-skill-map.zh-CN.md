# Atlas Build 技能协作图

- [PNG：README 展示版](atlas-build-skill-map.zh-CN.png)
- [SVG：可缩放、可编辑源文件](atlas-build-skill-map.zh-CN.svg)
- 视觉：红、白、黑；中文功能名称搭配英文 Skill 名称。
- 范围：当前[技能目录](../../agent-skills/)下的 16 个 Agent Skills。节点上的 `data-skill` 保存完整技能标识；其余灰色节点表示输入或交接产物，不计入技能数量。

## 阅读方式

1. 顶部的 Workflow Orchestrator 识别当前输入、选择路径，并支持计划与批准后执行。
2. 需求整理、功能规格和技术设计形成完整上游链。既有源码可先做程序分析；结合 CR 做影响分析后，按范围补充设计或进入程序规格。
3. 技术设计分别形成程序规格和文件规格。程序规格驱动 RPGLE/CLLE 生成、编译预检查及代码评审；文件规格 JSON 驱动 DDS 生成和评审。
4. UT Plan Generator 可从规格、CR 或原始输入形成测试计划，Test Scaffold 再生成 SQL/CL 脚本。此支线可与实现并行，不以代码评审通过为唯一入口。
5. Spec Reviewer 可评审需求整理产物及各层规格；图中用跨层栏表示。每一条箭头不意味着必须走完整链，也不表示自动批准。

Mini Requirement 短路径有准入条件：适用于上下文充分的小范围增强或修复；新程序或较大范围变更应回到完整链。BR-xx 和程序／文件规格之间的稳定引用承接可追溯性。

## 维护依据与边界

连接关系以本仓库 [Workflow Orchestrator](../../agent-skills/ibm-i-workflow-orchestrator/SKILL.md)、[Code Generator](../../agent-skills/ibm-i-code-generator/SKILL.md)、[Spec Reviewer](../../agent-skills/ibm-i-spec-reviewer/SKILL.md) 及各节点对应的 `SKILL.md` 为准。

布局参考了 [Skill-Library / IBM i Skill Family](https://github.com/wwa-lab/Skill-Library/tree/main/skills/ibm-i-skill-family) 的技能家族图，依据本仓库能力重新绘制。参考页中的 Requirement Intake 和 UT Plan XML 为待导入占位，本仓库没有这两个技能，因此本图未纳入，也未带入个人信息。

本图是能力与主要产物流转图，不是案例执行记录。编译预检查是静态检查；测试脚本仍需目标环境配置、数据和实际执行。当前合成 POC 未记录 UT Plan / Test Scaffold 执行，图示不表示完成运行验证或自动部署。

SVG 为无外部字体、脚本或资源依赖的手工排版源文件；PNG 由同一 SVG 导出。修改图中文字或连接线后需重新导出 PNG，并检查 16 个 `data-skill` 与当前技能目录是否一致。
