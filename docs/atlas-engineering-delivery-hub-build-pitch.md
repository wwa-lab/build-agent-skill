# Atlas Build：Situation → Solution → Result

本页是仓库价值定位和路演主线的维护入口。README 与参赛提交材料是本页的摘要；案例库保存支撑结论的证据。更新主张时先核对证据，再同步摘要，不另建一份独立的价值定义。

## 核心价值主张

**让 IBM i 交付团队把业务需求和旧代码知识，转成有依据、符合维护约定、能够逐项评审的实现工作。**

我们希望减少开发者反复理解、补充上下文和修正生成结果的投入，并让个人维护经验成为团队可复用的规则。当前已交付工作流和合成 POC 的静态证据；效率、返工和业务质量收益仍需真实案例对比测量。

直接使用者是 IBM i 开发者与评审者。交付负责人关注人工投入、返工和交接质量；业务方最终关注需求是否正确落地及交付是否可预期。首先验证开发与评审环节的改善，再讨论对整体业务交付的影响。

## 1. Situation — 为什么要做？

### 一个已经出现过的问题

[既有 Pilot 复盘](../article-pilot-retrospective.md)记录：一次只涉及少量 subroutine 的增强，被引导先走完整功能规格和技术设计链；生成的 fixed-format 代码又缺少维护者习惯的分隔、注释和命名；流程前端缺少旧代码理解，后端缺少测试准备。

这份复盘提供的是一次 Pilot 的定性反馈，没有可复核的工时基线和完整交付结果。它支持我们为什么调整产品，不用于宣称已节省多少时间，也不等于案例库已有完整真实项目案例。

### 要解决的交付问题

| 现状问题 | 对交付的影响 | 本项目要改变的工作方式 |
| --- | --- | --- |
| 业务改动描述之外，还需要现有源码、文件布局、调用及异常上下文 | 开发与评审仍需补齐实现依据；依据不足时容易产生不同解释 | 把现有证据和缺口带入规格、生成与评审 |
| 小型维护需求也被要求经过所有文档层 | 已知内容重复整理，工作流本身增加负担 | 满足条件的小改动走短路径，范围不明或新程序走完整链 |
| 生成代码没有匹配既有源码和团队维护约定 | 维护者需要整理命名、注释、布局并再次检查 | 将 fixed-format 约束和参考源码风格显式传入生成与评审 |
| 规则、程序、文件和验证材料分散 | 评审者需要重新寻找规则落点及跨对象关系 | 保留规则编号、文件／字段引用、参数契约和检查证据 |

前述 Pilot 直接支撑流程过重、可读性和两端衔接问题。跨对象一致性问题还得到 POC 中共享声明、参数／文件契约及修正记录的支持。我们尚未测量这些问题在整个团队中的发生频率、平均成本或普遍程度。

### 为什么采用 AI + 领域工作流？

这个项目已经尝试用模型生成分层规格和源码草案。Pilot 表明，要让这些输出适合日常维护，仍需明确当前程序、改动范围和维护规则。因此，我们把开发经验组织为可重复使用的输入要求、生成规则与评审步骤，让模型工作围绕团队已有证据展开。

这是采用该方案的设计依据；相对于人工流程或直接提示模型的净收益，留给后续对照案例验证。

## 2. Solution — 如何聪明且有特点地解决？

### 特点一：按改动规模选择路径

目标程序、现有源码、文件来源、规则和预期行为已明确的小型增强，可以通过 Mini Requirement 进入 Program Spec，随后生成并评审。新程序、影响不清或需求有争议的工作仍需要完整规格链。短路径保留实现规格与质量检查。

依据：[Orchestrator 的 Fast-Path](../.claude/ibm-i-workflow-orchestrator/SKILL.md)、[Mini Requirement 模板](../.claude/ibm-i-workflow-orchestrator/references/mini-requirement-template.md)。

**价值逻辑：** 已知内容减少重复整理，必要上下文仍进入实现依据。短路径规则已经存在，其实际节省时间尚未对比测量。

### 特点二：将 IBM i 维护经验写进生成依据

旧代码理解与影响分析提供变更背景；Program Spec 描述处理步骤；File Spec 同时提供可阅读文档与 JSON 契约；参考源码提供 fixed-format 布局、命名、分隔和异常处理惯例。生成与评审各有明确职责。

依据：[源码风格规则](../.claude/ibm-i-code-generator/references/source-style-profile.md)、[Program / File Spec 引用模型](../.claude/ibm-i-file-spec/references/interop-model.md)、[POC 共享契约](cases/rpgle-flow-poc/snapshot/docs/specifications/shared-contract.md)。

**价值逻辑：** 将资深开发者每次需要口头补充的约定变成可审阅、可复用的工作材料。当前已形成这些规则与契约；它们对整理和返工成本的改善仍待测量。

### 特点三：把实现依据与检查证据一起交付

BR 编号贯穿规格和源码定位；程序与文件通过稳定引用衔接；生成之后使用评审规则和检查工具核对。未确认内容标记为 TBD，关键审批、阻塞和后续交接由编排协议表达。

依据：[task.md 执行协议](../.claude/ibm-i-workflow-orchestrator/references/task-md-execution-protocol.md)、[POC 追踪记录](cases/rpgle-flow-poc/snapshot/docs/source/static-checks.json)、[POC 检查与修正](cases/rpgle-flow-poc/snapshot/docs/source/validation-report.md)。

**价值逻辑：** 评审可以沿着规则、步骤、源码和发现检查实现依据。POC 提供了实际产物与静态检查；编排门禁目前有文本协议，不能据此声称全部停点均由独立运行系统强制执行，或 POC 已跑完自动编排。

### 这些特点如何构成差异？

项目的差异化主张是这三项机制在 IBM i 日常交付中的组合：**适合维护工作的入口、可复用的领域规则、随产物交付的检查证据。** SDD、Skills 和评审本身不是本项目发明的技术；当前没有竞品对照试验，不使用“行业首创”“唯一方案”或“已优于所有通用 Agent”等说法。

通用能力维护在 `.claude/`，案例保存实际应用与发现。POC 专用静态检查器目前仍含业务对象和约束，通用化及问题反馈是后续改进，不能作为已交付的通用检查平台宣传。

## 3. Result — 现在有什么结果和收益？

### 已有结果及证据

| 已有结果 | 可以支持的价值判断 | 证据与边界 |
| --- | --- | --- |
| Pilot 反馈推动了短路径、旧代码分析、风格规则与测试准备能力的调整 | 产品设计回应了实际使用者提出的问题 | [Pilot 复盘](../article-pilot-retrospective.md)与现有 Skills；缺少改进前后效果测量 |
| 合成 POC 形成需求、设计、8 份程序规格、24 份文件规格和 35 个源码文件 | 这套 Skills 已用于有跨程序、跨文件依赖的完整产物案例 | [案例入口](cases/rpgle-flow-poc/README.md)；产物为 Draft，不能据此证明生成流程可稳定重复 |
| BR-01～BR-32 可关联规格步骤与源码，94 个步骤有静态定位 | 评审材料提供了可逐项核对的实现线索 | [静态检查结果](cases/rpgle-flow-poc/snapshot/docs/source/static-checks.json)；定位齐全不代表规则在所有输入下正确 |
| 修正记录包含消息覆盖、重复声明、回滚后结果、去重查询等问题 | 产物经过了针对性审阅与修正，保留了问题处理依据 | [检查报告](cases/rpgle-flow-poc/snapshot/docs/source/validation-report.md)；部分来自审阅，不能把所有发现都归因于自动工具 |
| 本地源码检查与冻结评测包检查可执行，案例材料有固定来源及校验值 | 其他人可以复核当前静态证据和材料版本 | [复核方法](cases/rpgle-flow-poc/README.md#本仓库中的复核)；这不是编译、业务运行或模型得分 |

当前最有依据的结果表述是：**我们已把分散的交付约定组织成工作流，并在一个有规模的合成案例中形成了可追踪、可复核的规格与源码草案。**

16 个 Skills、万行源码和产物数量用来说明能力组成与样本规模。面向评委的价值重点是：能否找到实现依据、核对契约，以及查看问题如何被发现和处理。

### 希望带来的收益与下一步测量

| 预期收益（尚未实测） | 如何检验 | 当前状态 |
| --- | --- | --- |
| 减少需求到可评审实现的人工投入 | 汇总理解、准备上下文、规格、生成操作、修正和评审的总人时；另记端到端历时 | 无对照数据 |
| 减少因规则遗漏、契约及源码风格不一致造成的返工 | 按统一分类记录独立评审发现、严重度、修正轮次及修正人时；包括上游规格自身错误 | 无对照数据 |
| 降低交接时重新理解实现的成本 | 由未参与生成的评审者定位同一组规则和依赖，记录时间、正确率和澄清次数 | 无对照数据 |
| 提高交付质量与可预测性 | 在 IBM i 环境记录首次编译、修复后编译、业务验收及回归结果，并关联版本 | 尚无编译或业务运行证据 |

下一批真实案例优先选择一项范围明确的增强，比较相近复杂度、相同验收口径的日常工作与使用 Skills 的交付过程。记录开发者经验、既有知识、参与人员和工具配置；控制重复做同一题的学习效应，样本不足时如实披露。

如果要单独验证“领域工作流相对直接提示模型”的增量价值，应尽量使用相同模型、工具权限、可见业务材料和验收条件，两组只改变是否使用该工作流，保留所有人工补充与修正。将这种比较与“不同模型的四类 Flow 理解评测”分开，后者不直接衡量 Build 的交付收益。

用[案例模板](cases/case-template.md)保存这些数据。当前不填写推测百分比，也不将订单履约 POC 的虚构业务目标表述为已改善银行业务指标。

## 路演讲述顺序

建议先用五页完成价值叙事，技术架构和技能清单放入备份页：

1. **Situation：** 用 Pilot 中“小改动被迫先走完整文档链”的场景开场，说明上下文、维护习惯和交接问题给开发与评审带来的负担。
2. **Solution：** 展示三项设计：按范围选路径、将领域约定写入依据、随实现交付检查证据。解释每项设计如何回应一个问题。
3. **Demonstration：** 打开合成 POC 的 BR-06：相同内容的重复请求返回已知结果。沿 ORDMAIN Program Spec 的 Step 5 `MDEDUP`、REQPF 文件契约、源码及静态追踪记录核对；再看去重查询修正记录，说明没有订单主记录时也要保留原请求结果的语义。
4. **Result：** 展示已形成的产物、可核对的规则路径、检查与修正。明确静态证据范围，随后说明下一步如何测量人工投入和质量收益。
5. **Next validation：** 邀请实际交付团队提供一个可分享、可脱敏、验收明确的案例，验证业务价值，并将发现反馈到技能。

Demo 证据入口：[BR-06 需求](cases/rpgle-flow-poc/snapshot/docs/requirements/functional-requirements.md)、[ORDMAIN 规格](cases/rpgle-flow-poc/snapshot/docs/specifications/programs/ORDMAIN.md)、[REQPF 契约](cases/rpgle-flow-poc/snapshot/docs/specifications/files/REQPF.md)、[ORDMAIN 源码](cases/rpgle-flow-poc/snapshot/src/QRPGLESRC/ORDMAIN.rpgle)、[静态追踪](cases/rpgle-flow-poc/snapshot/docs/source/static-checks.json)、[修正记录](cases/rpgle-flow-poc/snapshot/docs/source/validation-report.md)。冻结源码中的 `MDEDUP` 起始物理行是 559，仅作为阅读定位；这段演示不声称已经运行重复请求测试。

### 与 CodeCup 主题的对应

根据用户提供的活动材料，路演可以这样对应主题；以下是项目的表达方式，不是新增评审规则：

- **Think customer, deliver value：** 从开发者和评审者的真实使用反馈出发，以人工投入、返工和交接质量检验收益。
- **Simplify to amplify：** 让满足条件的小改动走短路径，并复用已有源码与维护规则。
- **Challenge, align and commit：** 根据 Pilot 反馈调整设计，显式记录不确定项与审批依据，用实际案例继续检验。
- **AI Enable SDLC / Harness Engineering：** 模型生成、领域约束、检查工具与人工判断共同参与交付；分别说明已有协议、实际执行证据和待完成能力。

## 可直接讲述的短版

### 中文

在 IBM i 日常维护里，一项业务改动需要开发者理解旧代码、核对文件与调用，还要让实现符合团队的维护习惯。我们的 Pilot 发现，完整文档链会拖重小改动，生成代码也需要补足这些上下文。因此，我们让明确的小改动走短路径，把领域约定写入规格和生成规则，并将业务规则、源码与检查证据关联起来。目前，一个合成 POC 已形成 8 份程序规格、24 份文件规格和 35 个源码文件，具备静态追踪与修正记录。下一步将用真实案例测量人工投入、返工和验证质量的改善。

### English

IBM i maintenance requires developers to understand existing code, check file and calling contracts, and preserve the team's maintenance conventions. Our pilot exposed friction from a full document chain for small changes and generated code that needed more context. Atlas Build addresses this with a qualified fast path, reusable domain rules, and traceable specifications, source and review evidence. A synthetic POC now contains 8 program specs, 24 file specs and 35 source files with static checks and recorded corrections. Real delivery cases will measure the effect on human effort, rework and validation quality.

## 产品范围与技术备份

Atlas Engineering Delivery Hub – Build 是整体 Atlas 中的 M4 Build 工具。该位置用于说明分工，首页与路演首先回答交付问题及价值。现有能力包括 16 个 IBM i Skills、Program / File Chain、评审、UT Plan / SQL-CL Scaffold 与编排协议；具体入口见 [README](../README.md)。

[完整 POC](cases/rpgle-flow-poc/README.md)用于展示已有案例证据；[小示例](samples/atlas-build-tool-mini-output/README.md)用于快速讲解产物形态。没有 IBM i 连接器、部署运行时、编译或业务执行结果，也没有该 POC 完成全部自动编排的日志。保持这些边界，不将愿景和已验证结果混为一谈。
