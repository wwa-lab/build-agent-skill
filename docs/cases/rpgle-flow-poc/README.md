# RPGLE Flow POC：多仓订单履约与结算

This synthetic case demonstrates the IBM i skill family producing requirements, design, specifications, and source drafts with static checks. It also includes a frozen flow-analysis benchmark kit; IBM i compilation, business execution, and model evaluation have not been performed in the recorded snapshot.

[返回案例库](../README.md) · [来源快照](https://github.com/wwa-lab/rpgle-migration-benchmark/tree/ede8a8c6792f186d5b0da8cc5d27b8fbe419bee2) · [完整性与适配清单](provenance.json)

## 案例信息

| 字段 | 内容 |
| --- | --- |
| 案例 ID | `rpgle-flow-poc` |
| 来源类型 | `synthetic`，全部业务、对象与数据设定均为虚构 |
| 来源版本 | `rpgle-migration-benchmark` / `ede8a8c6792f186d5b0da8cc5d27b8fbe419bee2` |
| Skills 版本 | 本仓库 `main` 的 `0c331e86fad2b13d5f9d1313bdd928b29d355b43` |
| 纳入日期 | 2026-09-05 |
| 材料状态 | 需求、设计、规格和源码为 Draft；RPGFLOW-1.0 评测输入已冻结 |
| 当前验证范围 | 结构、引用、契约、追踪及评测材料完整性；未编译、未运行 IBM i、未调用被测模型 |

## 1. Situation — 要检验什么交付问题？

案例设计了一个多仓订单履约与结算系统，包含订单校验、计价、库存分配、履约、取消、退货、结算、回执和日终等流程。它用一个具有跨程序、跨文件依赖的合成业务，考察分层规格到固定格式 RPGLE / DDS 源码草案的交付过程，并准备后续代码理解评测材料。

对 Build 项目而言，重点是需求、设计、程序规格、文件规格和源码之间能否保持一致，以及检查过程中发现了什么问题。对来源 benchmark 而言，后续目标是比较不同平台与模型配置下的 Operation、System、Data、Transaction Flow 分析表现。两者的评价对象分开记录。

订单履约与结算是合成工作负载；此案例不表述为已经解决真实客户的库存或结算问题。它为[项目价值主线](../../atlas-engineering-delivery-hub-build-pitch.md)提供交付产物与静态检查证据，没有人工基线，也没有实际业务收益记录。

## 2. Solution — 实际采用的方法

此案例走需求、技术设计、程序／文件规格到源码草案的完整路径，沿用本仓库固定版本的领域 Skills。共享契约明确程序参数和文件字段，BR 编号关联实现步骤；生成后结合代码审阅维度和案例专用工具检查结构、引用及契约，并保留修正记录。

这里展示的是多程序、多文件场景中的规格与契约衔接。它不是日常小增强的短路径试验，也未提供端到端自动编排日志，因此不能用此案例衡量短路径收益或无人介入的交付能力。

## 3. Result — 已有结果和价值边界

- **形成可核对的实现材料：** 需求、设计、规格和源码可在同一案例内对照；BR-01～BR-32 与 94 个步骤有静态关联。
- **保留问题处理依据：** 审阅记录包含消息覆盖、重复声明、回滚结果与去重查询等修正，读者可以检查处理理由。
- **支持本地复核：** 源码结构／契约和冻结评测包都有可执行检查，材料版本有来源及校验值。

这些是当前能展示的结果。开发效率提升、返工减少、交接成本下降和业务正确性尚无对照或运行证据，后续应按[案例模板](../case-template.md)补充测量，不能从产物规模推算收益。

## 已有产物

| 阶段 | 本地入口 | 内容 |
| --- | --- | --- |
| 需求 | [Functional Requirements](snapshot/docs/requirements/functional-requirements.md) | 虚构业务范围、BR-01～BR-32、验收场景及未确认项 |
| 技术设计 | [Technical Design](snapshot/docs/design/technical-design.md) | 8 个程序、24 个文件的职责、接口及 Flow 设计 |
| 规格 | [规格索引](snapshot/docs/specifications/README.md) | 8 份 Program Spec、24 份 File Spec 及 JSON、94 个逻辑步骤 |
| 共享契约 | [字段与调用契约](snapshot/docs/specifications/shared-contract.md) | 16 个共享结构、参数顺序、长度与跨规格引用 |
| 源码 | [源码索引](snapshot/docs/source/README.md) | 7 RPGLE、1 CLLE、24 DDS、3 COPY，共 35 个文件 |
| 静态审查 | [规格检查](snapshot/docs/specifications/validation-report.md)、[源码检查与修正](snapshot/docs/source/validation-report.md) | 静态检查结果、已修正问题和未验证范围 |
| 评测准备 | [RPGFLOW-1.0](snapshot/benchmark/README.md) | 相同材料的文件版／分段文本版、统一提示词、隔离评分参考及空白运行模板 |

主程序 ORDMAIN 为 10,114 个物理行；全部源码为 13,918 个物理行。行数描述样本规模，不代表业务正确性、独立逻辑数量或生产效率。BR 与源码位置的关联也不等于运行覆盖率。

此 POC 已使用功能规格、技术设计、程序规格、文件规格、代码与 DDS 生成，以及代码审阅维度。它没有运行 UT Plan / Test Scaffold 阶段，也没有提供 Orchestrator 自动执行全部步骤的运行日志，不作为这些能力已完成执行的证据。

## 建议的 Demo 阅读路径

1. 在需求中选择一条与重复请求、库存或结算相关的 BR。
2. 从技术设计与规格中找到它所属程序、步骤和数据契约。
3. 对照 [Program Spec](snapshot/docs/specifications/programs/ORDMAIN.md)、[ORDMAIN 源码](snapshot/src/QRPGLESRC/ORDMAIN.rpgle)和[机器追踪结果](snapshot/docs/source/static-checks.json)，定位实际代码。
4. 展示一次审阅中发现并修正的问题，解释静态证据能支持什么结论。
5. 展示尚无编译和运行证据的部分，以及后续验证如何补充。

现有记录没有人工基线、完整模型调用日志、工时或收益测量，因此不报告节省比例、生产就绪率或实际迁移成功率。

## 本仓库中的复核

在仓库根目录运行（Python 3.9+，仅标准库）：

```bash
python3 docs/cases/rpgle-flow-poc/snapshot/tools/validate_static_source.py
python3 docs/cases/rpgle-flow-poc/snapshot/tools/prepare_benchmark.py --check
```

第一条检查源文件结构、调用与数据契约、追踪关系及导入完整性，并重写案例内 `snapshot/docs/source/static-checks.json`；未改变的快照应得到相同结果。第二条以只读方式检查冻结材料、源码变换、文本重建、ZIP 边界和评分引用。二者不连接 IBM i，不编译或执行业务程序，也不调用模型。

源规格检查报告是保留的历史记录，以上命令不声称重新执行了报告中的全部人工检查。静态检查器仍包含本业务的对象名和约束，是案例专用工具；不能直接当作通用 RPGLE 编译器或通用业务验证器。

如需进行模型评测，只向被测会话提供[文件包](snapshot/benchmark/dist/RPGFLOW-1.0-files.zip)或[文本包](snapshot/benchmark/dist/RPGFLOW-1.0-text.zip)，按[运行办法](snapshot/benchmark/runs/README.md)执行。整个 Build repo 含有规格、生成说明与评分参考，不能作为被测会话的可见工作区。新的结果放在案例外层 `runs/<run-id>/`，保留冻结模板。

## 已发现问题与上游反馈

[源码检查记录](snapshot/docs/source/validation-report.md)保留了消息覆盖、重复声明、回滚结果、恢复上下文、去重查询、历史分配和批次返回码等修正。这些是本案例的修正记录，不代表通用技能已覆盖所有同类错误。

其中还记录了 DDS 小数位和 UNIQUE 位置与上游文字说明的冲突，后续应核对生成器、评审器、示例及测试并反馈到 Skills。本次纳入案例未修改任何技能规则，也未把该待核对事项标为已修复。

## 快照与维护边界

- `snapshot/` 实际收录来源提交的 `docs/`、`src/`、`tools/`、`benchmark/`、README 和上游技能清单／许可。克隆当前仓库即可读取完整案例及复核材料，无需再克隆来源项目。
- [provenance.json](provenance.json)记录每个文件的来源路径、来源 SHA-256、纳入后的 SHA-256 和适配说明。
- 只适配了快照 README 的导航，以及静态检查器的仓库布局检查：历史 `.agent` 副本校验改为本案例完整性校验；原有业务、格式、契约和追踪检查保留。源文档、源码、评分材料及冻结包保持原字节。
- 没有复制 16 个技能或来源仓库的 AGENTS.md。历史文档中的 `.agent` 指来源项目布局；本仓库继续以根目录 `.claude/` 为技能维护入口。
- 来源项目的阶段报告保持历史原文，个别“下一阶段”描述反映写作时状态。当前案例状态以本页及对应证据为准，不回写历史记录。
- 后续真实案例独立加入[案例库](../README.md)，使用[统一模板](../case-template.md)。本合成案例不改名为真实案例，不用新 Skills 重生成后覆盖旧证据。
- 保留[来源项目的许可说明](snapshot/README.md#许可)和[上游技能许可副本](snapshot/provenance/UPSTREAM-LICENSE)；技能许可副本不自动为来源项目新增案例材料指定许可证。
