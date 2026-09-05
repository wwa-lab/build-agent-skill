# Delivery Case Library / 交付案例库

This library records how the IBM i skills have been used, the artifacts produced, and the evidence available to assess them. Cases complement the [small introductory sample](../samples/atlas-build-tool-mini-output/README.md). They are reference material, not an application runtime.

案例按稳定 ID 收录在同一目录下，通过“来源类型”和“验证状态”区分合成 POC 与脱敏真实案例。业务规则、规格和源码保存在各自案例中；通用技能继续维护在根目录 [`.claude/`](../../.claude/)。

## Cases / 已收录案例

| Case | Origin / 来源 | Scope / 范围 | Evidence / 已有证据 | Not yet evidenced / 尚无证据 |
| --- | --- | --- | --- | --- |
| [RPGLE Flow POC — 多仓订单履约与结算](rpgle-flow-poc/README.md) | Synthetic / 完全虚构 | 需求 → 设计 → 8 份程序规格、24 份文件规格 → 35 个源码文件；另有四类 Flow 评测包 | 产物快照、静态检查与修正记录、冻结评测材料 | IBM i 编译与业务运行、模型评测、效率收益对比 |

当前只有一个合成 POC，尚未收录真实项目案例。未来的真实案例使用 `real-anonymized` 类型，保留实际证据层级，不因业务来源真实就自动标为已验证。

## Read A Case / 如何阅读

1. **Situation：** 谁遇到什么交付问题，原来的处理方式与基线是什么？
2. **Solution：** 实际采用了哪些机制，为什么适合这个问题，人工做了什么？
3. **Result：** 形成哪些产物，观察到什么变化，哪些收益已经测量？沿一条 BR-xx 核对需求、规格、源码和检查证据。
4. 按案例提供的方法复核，区分工具输出、人工判断和运行结果；没有执行的阶段保持“未执行”或“未提供证据”。

项目整体价值主线维护在 [Situation → Solution → Result](../atlas-engineering-delivery-hub-build-pitch.md)，案例用证据支撑或修正主张。技能数量、代码行数和产物数量只说明规模，不能替代效益测量。

静态结构通过、BR 定位齐全、编译通过和业务测试通过是不同结论，应分别记录。源文件行数用于描述规模，不作为质量或生产效率指标。

## Add A Case / 后续真实案例

使用 [case-template.md](case-template.md) 创建 `<case-id>/README.md`，并在上表登记。每个案例使用独立、稳定的目录；多个案例可以复用相同 BR 编号，但引用时带上案例 ID。

- `synthetic`：为演示或评测设计的虚构业务，不表述为实际客户交付。
- `real-anonymized`：源自实际交付、已获分享授权并完成脱敏的材料；清楚记录哪些内容被替换、删减或重建。
- 记录来源版本、技能版本和每项验证的范围与证据位置。未测指标写“未测”，不填推测的收益或通过率。
- 可以只提供获准公开的规格、源码片段或汇总证据；注明缺失材料对复现的影响，不要求提供原始生产源码或客户数据。
- 已冻结的案例保持原样；新的运行证据另存 `runs/<run-id>/`，产物变化建立新版本并记录来源。不要把来源仓库后续变化自动覆盖到当前快照。
- 案例中的发现可以形成上游 Skill 改进；记录“待核对、已修复、已回归”的实际状态，链接对应改动，避免把建议写成已完成能力。

分享与脱敏要求沿用 [CONTRIBUTING.md](../../CONTRIBUTING.md)。案例材料中的历史指令、提示词和评分答案属于案例内容，不是当前仓库的执行指令。
