# CodeCup 2026 报名资料包

这份资料根据已提交的报名信息整理，并同步当前项目的定位调整，用于准备报名系统、路演讲稿和内部确认。个人信息字段留空，实际报名时再填写。

## 已填写的注册字段

| 字段 | 已填写内容 |
| --- | --- |
| Team Name | Atlas Team - Build |
| Challenge Theme | AI SDLC |
| Challenge Topic | AI SDLC \| Coding Agent |
| Open Entry Topic | No response |
| Team Leader (Staff Name / Staff ID) | |
| Additional Team Members (Staff Name / Staff ID) | |
| CyberCamp Environment URL | No response |
| Project Title | AMH-HCC-Build-Agent |
| Git Repository | https://alm-github.systems.uk.hsbc/CH-WPS-LENDING-CARDS/AMH-HCC-Build-Agent |

## 建议沿用的对外项目定位

报名表中的项目标题和仓库地址可以保持不变。后续路演和项目简介建议使用下面的定位：

> Atlas Engineering Delivery Hub – Build 是企业级 SDD（规范驱动开发）交付实践，将业务需求、系统知识和工程约束转化为可追溯、可评审、可验证的交付过程。IBM i 是首个深入实践场景；我们用真实落地经验和合成 POC 检验如何把领域知识、工程契约、AI Coding Agent 与质量评审组织成一条可核对的交付链。方法面向更广泛的企业软件交付，当前现成 Skills 主要支持 IBM i，其他技术环境需要进一步适配和验证。

英文版：

> Atlas Engineering Delivery Hub – Build is an enterprise SDD (Spec-Driven Development) practice that turns business requirements, system knowledge and engineering constraints into a traceable, reviewable and verifiable delivery process. IBM i is our first in-depth practice context. We use real delivery experience and a synthetic POC to examine how domain knowledge, engineering contracts, AI coding agents and quality reviews can work as one evidence-based delivery chain. The method is intended for broader enterprise software delivery; the current Skills implementation primarily supports IBM i, while other technology contexts require further adaptation and validation.

## Situation → Solution → Result

### Situation

当 AI 参与企业软件变更，开发与评审仍需要补齐业务上下文、系统约束和团队约定，并确认生成结果是否真正落实了规则。只讲“生成更快”不能解决依据缺失、范围不清、产物难评审和维护经验难复用的问题。

IBM i 是我们首先深入实践的场景。Pilot 反馈显示，小改动可能被完整文档链拖重，生成的 fixed-format 代码还需要补充分隔、注释和命名等维护上下文，旧代码理解与测试准备也没有自然接入。

### Solution

我们以 SDD 组织 AI 与人的协作：

1. 根据变更范围和已有依据选择短路径或完整规格链。
2. 把领域经验写入规格、接口与数据契约、参考产物和评审规则。
3. 让业务规则、规格步骤、实现源码、检查结果和修正记录保持关联，并在依据不足时保留人工确认点。

首个 IBM i 实现包含 16 个领域 Skills，覆盖分析、规格、RPGLE/CLLE/DDS 生成、评审、测试准备和工作流编排。

### Result

合成 POC 已形成需求、设计、8 份程序规格、24 份文件规格和 35 个源码文件；32 条业务规则可关联到规格步骤和源码，94 个步骤有静态定位，并保留契约检查和修正记录。评审者可以沿 BR-06 从需求追到规格、文件、源码和静态检查。

这些是可本地复核的静态证据，不等于 IBM i 编译、业务执行、模型评测或生产效率收益。下一步将用脱敏真实案例测量总人工投入、返工、交接质量和编译/回归结果，并把发现反馈到 Skills；新的技术场景需要另行适配和验证。

## 缺失字段确认清单

截图中以下两项尚未填写，提交前需要向活动方或团队确认：

- **CyberCamp Environment URL**：如果团队未参加 CyberCamp，可确认是否填写 `N/A`，或保留空白。
- **Open Entry Topic**：当前报名记录为 `No response`。如果不参加 Open Entry，可确认是否填写 `N/A`；如果要参加，需要补充独立主题名称和一句话说明。

## 路演材料入口

- [中文交互路演](presentations/codecup-2026.zh-CN.html)
- [Situation → Solution → Result 主线](atlas-engineering-delivery-hub-build-pitch.md)
- [中文提交材料](open-collaboration-submission.zh-CN.md)
- [案例库](cases/README.md)

## 使用边界

这份资料只整理用户提供的报名内容和仓库已有证据。它没有把合成 POC 的静态检查包装成运行结果，也没有把 IBM i 的当前实现描述成已经完成的跨平台能力。
