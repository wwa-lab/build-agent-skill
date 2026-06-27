# 开放协作提交材料：Atlas Engineering Delivery Hub - Build

## 项目

**Atlas Engineering Delivery Hub - Build**

类别：**Tool**

仓库：`wwa-lab/build-agent-skill`

## 摘要

Atlas Engineering Delivery Hub - Build 是 Atlas Engineering Delivery Hub / Seven Mountains SDLC 中的 M4 Build 阶段工具。它帮助团队把结构化交付证据转换为可控的实现工作。

当前实现是一组面向 IBM i 企业开发的 Claude Code Skills。仓库包含 16 个专门技能，可以把需求、设计证据、Program Spec、File Spec 等输入，推进为 RPGLE/CLLE 源码草稿、DDS 源码、评审报告、单元测试计划、SQL/CL 测试脚手架，以及可审批的 `task.md` 构建计划。

## 解决的问题

IBM i 企业交付中，Discovery/Design 到 Build 的交接经常出现断点：

- 需求已经写清楚，但开发仍然要重新推断实现细节。
- 旧 RPGLE/CLLE 代码变更前，需要先理解现有逻辑和影响范围。
- 文件定义、DDS 源码和程序逻辑容易因为假设不同而漂移。
- Spec、代码、DDS、测试证据之间缺少统一质量门禁。
- 下游测试阶段很难拿到完整、可追踪的 Build 证据包。

本工具通过一个 Skill Family，把证据、追踪、生成、评审和测试计划放到同一条 M4 Build 链路中。

## 为什么可复用

这个仓库沉淀的是通用构建模式，而不是某个项目的专用实现：

- 所有 Skills 都以文本方式放在 `.claude/` 下，方便团队审阅和调整。
- 示例使用脱敏和通用对象名。
- Program Chain 和 File Chain 分离，但可通过稳定 ID 互相引用。
- `task.md` 模型可以表达目标产物、门禁、执行日志、开放问题和最终 manifest。
- 安全边界清晰：未知内容标记为 TBD，生成必须基于正确规格，关键位置保留人工审批。

## 与开放协作主题的关系

这个项目适合开放协作，因为许多团队都会遇到相似的 Build 阶段问题：

- 可以持续贡献常见 IBM i 变更模式和示例。
- 可以在不暴露客户数据的情况下改进评审规则。
- 可以逐步补充测试用例和回归检查。
- 可以共建新的 Build pattern、Mini Requirement、`task.md` 模板。
- 中英文文档让项目更容易被评审、采用和共同完善。

## 已交付内容

- 16 个 IBM i Claude Code Skills。
- Program Chain：需求/设计证据 -> Program Spec -> RPGLE/CLLE -> 编译预检查 -> 代码评审 -> UT Plan -> SQL/CL 脚手架。
- File Chain：Technical Design/File Spec -> File Spec JSON -> DDS Source -> DDS Review。
- 支持 Routing Mode、Plan Mode、Execute Mode 的 Workflow Orchestrator。
- `task.md` 模板和执行协议。
- 覆盖规格、代码生成、DDS 生成、测试脚手架、评审输出和编排计划的示例。
- DDS、代码生成、测试脚手架三个半自动测试 harness。
- 面向评审的中英文 README、提交材料、贡献指南、架构图和脱敏示例包。

## 可共建方向

- 更多脱敏 IBM i 变更模式。
- 更多 DDS、RPGLE、CLLE 和测试脚手架回归用例。
- 多程序、多文件、程序+文件混合变更的 Orchestrator 示例。
- Mermaid 图表渲染资产和 Markdown 链接 CI 检查。
- 常见企业交付失败模式的评审规则示例。
- M3 Discovery 证据与 M4 Build 包衔接的使用指南。

## Demo 讲述建议

1. 从一个小型 IBM i 增强需求开始，例如客户维护流程增加状态校验。
2. 展示 Build 工具如何把上游发现/设计证据作为源头。
3. 用生命周期图说明本仓库位于 M4 Build。
4. 用内部工作流图说明规格、源码/DDS、评审门禁、UT Plan、测试脚手架的关系。
5. 打开脱敏示例包，展示输入、`task.md`、生成片段和验证产物。
6. 最后强调交接边界：M4 Build 输出的是可控证据包，供 M5 Testing 使用，不是未经评审的生产发布。

## 边界

- 本仓库不是整个 Atlas Engineering Delivery Hub。
- 它不替代 M3 Discovery。
- 它不连接 IBM i 系统，也不部署代码。
- 它不声称生成代码无需人工评审即可生产使用。
- 它不处理密钥或客户数据。

## 评审入口

- [README.md](../README.md)：M4 Build 定位和快速开始。
- [README.zh-CN.md](../README.zh-CN.md)：中文说明。
- [CONTRIBUTING.md](../CONTRIBUTING.md)：安全贡献规则。
- [docs/assets/](assets/)：架构图。
- [docs/samples/atlas-build-tool-mini-output/](samples/atlas-build-tool-mini-output/)：脱敏示例。
- [docs/full-reference-readme.md](full-reference-readme.md)：保留的完整技术参考。
