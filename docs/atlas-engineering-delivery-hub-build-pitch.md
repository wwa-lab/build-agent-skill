# Atlas Build：企业级 SDD 交付实践

本页是仓库价值定位和路演主线的维护入口。README 与参赛提交材料是本页的摘要；案例库保存支撑结论的证据。更新主张时先核对证据，再同步摘要，不另建一份独立的价值定义。

[中文交互路演](presentations/README.md)将本页主线展开为 18 页 HTML 演示，支持流程切换、POC 证据查看和逐页讲稿。

## 核心价值主张

**企业级 SDD（规范驱动开发）交付实践：将业务需求、系统知识和工程约束，转化为可追溯、可评审、可验证的交付过程。**

我们希望减少开发者反复理解、补充上下文和修正生成结果的投入，并让个人维护经验成为团队可复用的规则。当前已交付工作流和合成 POC 的静态证据；效率、返工和业务质量收益仍需真实案例对比测量。

面向企业开发者与评审者，首个深入实践场景是 IBM i。交付负责人关注人工投入、返工和交接质量；业务方最终关注需求是否正确落地及交付是否可预期。首先验证开发与评审环节的改善，再讨论对整体业务交付的影响。

## 定位：企业问题、实践方法与领域实现

项目关注企业软件长期演进中的交付问题。业务规则、现有系统依赖和团队维护约定需要进入 AI 的工作依据，生成结果需要能够被人核对。SDD 在这里既组织规格产物，也组织范围判断、契约、人工确认和验证反馈。

| 层次 | 核心内容 | 与当前证据的关系 |
| --- | --- | --- |
| 企业交付问题 | 上下文缺失、变更范围、维护约定、交接与评审负担 | 用 Pilot 和 POC 具体说明，不推断行业发生率 |
| SDD 实践方法 | 按范围选路径、分层规格、显式契约、规则追踪、人工确认与验证反馈 | 已有工作流协议、模板和规则，可作为其他领域设计参考 |
| 领域实现 | 特定平台的生成、评审和验证约束 | 当前是 IBM i 的 16 个 Skills；尚无第二技术场景的验证证据 |

IBM i 是方法的首个实践锚点。其文件布局、参数顺序、固定格式与既有源码约定，为检验领域约束如何进入交付提供了具体材料。项目定位面向企业 SDD，现成工具的支持范围仍按实际实现说明。

## 1. Situation — 为什么要做？

### 企业交付中的核心问题

当 AI 参与企业软件变更，开发与评审需要回答：模型依据哪些业务规则和系统知识？缺少信息时由谁确认？产物怎样与需求对应？生成之后的修正和验证是否被记录？这些问题定义本项目的设计范围，下面用首个场景的事实说明。

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

### 特点二：将领域经验写进生成依据

在首个 IBM i 实现中，旧代码理解与影响分析提供变更背景；Program Spec 描述处理步骤；File Spec 同时提供可阅读文档与 JSON 契约；参考源码提供 fixed-format 布局、命名、分隔和异常处理惯例。生成与评审各有明确职责。

依据：[源码风格规则](../.claude/ibm-i-code-generator/references/source-style-profile.md)、[Program / File Spec 引用模型](../.claude/ibm-i-file-spec/references/interop-model.md)、[POC 共享契约](cases/rpgle-flow-poc/snapshot/docs/specifications/shared-contract.md)。

**价值逻辑：** 将资深开发者每次需要口头补充的约定变成可审阅、可复用的工作材料。当前已形成这些规则与契约；它们对整理和返工成本的改善仍待测量。

### 特点三：把实现依据与检查证据一起交付

BR 编号贯穿规格和源码定位；程序与文件通过稳定引用衔接；生成之后使用评审规则和检查工具核对。未确认内容标记为 TBD，关键审批、阻塞和后续交接由编排协议表达。

依据：[task.md 执行协议](../.claude/ibm-i-workflow-orchestrator/references/task-md-execution-protocol.md)、[POC 追踪记录](cases/rpgle-flow-poc/snapshot/docs/source/static-checks.json)、[POC 检查与修正](cases/rpgle-flow-poc/snapshot/docs/source/validation-report.md)。

**价值逻辑：** 评审可以沿着规则、步骤、源码和发现检查实现依据。POC 提供了实际产物与静态检查；编排门禁目前有文本协议，不能据此声称全部停点均由独立运行系统强制执行，或 POC 已跑完自动编排。

### 这些特点如何构成差异？

项目的差异化主张是将企业约束贯穿 SDD 交付，并通过领域案例检验三项机制的组合：**适合维护工作的入口、可复用的领域规则、随产物交付的检查证据。** SDD、Skills 和评审本身不是本项目发明的技术；当前没有竞品对照试验，不使用“行业首创”“唯一方案”或“已优于所有通用 Agent”等说法。

通用能力维护在 [Agent Skills 技能目录](../.claude/)，案例保存实际应用与发现。POC 专用静态检查器目前仍含业务对象和约束，通用化及问题反馈是后续改进，不能作为已交付的通用检查平台宣传。

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

## 领域扩展路线

可迁移性是下一步需要验证的主张。当前不将 IBM i Skills 重命名为通用生成器，也不声称已具备跨平台运行能力。

1. **选择一个范围明确的新领域案例。** 记录系统背景、变更目标、验收条件和既有交付方式，使用案例模板管理来源与证据。
2. **映射领域契约。** 将本场景的程序／文件契约映射到目标领域实际存在的模块、接口、数据结构与依赖；字段类型和技术约束必须由该领域资料提供。
3. **补充实现与评审规则。** 为目标语言、框架和团队惯例建立生成规则、参考产物及评审检查，明确哪些方法沿用、哪些规则重新编写。
4. **接入实际验证。** 按目标技术环境记录构建、测试与回归结果，区分静态检查和运行证据。
5. **比较交付结果。** 记录总人工投入、返工、交接质量与失败情况，再判断方法是否可迁移。通过后才扩展支持范围说明。

这是一条扩展路线，当前仓库没有通用领域适配器或第二技术栈的已验证实现。

## 路演讲述顺序

建议按以下五段组织价值叙事，完整网页演示可用更多页面展开。先讲企业问题与方法，再进入 IBM i 实践，技术清单作为备份材料：

1. **Situation：** 从企业 AI 交付需要补齐上下文、核对约束和追踪规则的问题开场，再以 IBM i Pilot 中“小改动先走完整文档链”的反馈提供具体依据。
2. **Solution：** 展示三项设计：按范围选路径、将领域约定写入依据、随实现交付检查证据。解释每项设计如何回应一个问题。
3. **Demonstration：** 将 IBM i 介绍为首个深入实践场景，打开合成 POC 的 BR-06：相同内容的重复请求返回已知结果。沿 ORDMAIN Program Spec 的 Step 5 `MDEDUP`、REQPF 文件契约、源码及静态追踪记录核对；再看去重查询修正记录，说明没有订单主记录时也要保留原请求结果的语义。
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

我们探索企业级 SDD 如何落地：让业务需求、系统知识和工程约束进入 AI 的工作依据，并随实现保留可核对的证据。方法包括按范围选择路径、将领域经验显式化，以及关联规格、源码和检查记录。首个深入实践场景是 IBM i，已有 Pilot 定性反馈和合成 POC；POC 形成 8 份程序规格、24 份文件规格和 35 个源码文件，具备静态追踪与修正记录。下一步通过真实案例测量人工投入、返工和交接质量，并在新的技术场景中检验方法的可迁移性。

### English

Atlas Build explores enterprise SDD in practice: bringing business requirements, system knowledge and engineering constraints into AI-assisted delivery, with evidence reviewers can follow. The method combines scope-aware routing, explicit domain conventions and linked specifications, source and checking records. IBM i is the first in-depth practice context, with qualitative pilot feedback and a synthetic POC containing 8 program specs, 24 file specs and 35 source files. Static checks and recorded corrections are available. Real cases will measure human effort, rework and handoff quality; additional technology contexts will test transferability.

## 产品范围与技术备份

Atlas Engineering Delivery Hub – Build 是整体 Atlas 中的 M4 Build 工具。该位置用于说明分工，首页与路演首先回答交付问题及价值。现有能力包括 16 个 IBM i Skills、Program / File Chain、评审、UT Plan / SQL-CL Scaffold 与编排协议；具体入口见 [README](../README.md)。

[完整 POC](cases/rpgle-flow-poc/README.md)用于展示已有案例证据；[小示例](samples/atlas-build-tool-mini-output/README.md)用于快速讲解产物形态。没有 IBM i 连接器、部署运行时、编译或业务执行结果，也没有该 POC 完成全部自动编排的日志。保持这些边界，不将愿景和已验证结果混为一谈。
