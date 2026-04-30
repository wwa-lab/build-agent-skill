# 8 个 Skill 到 16 个，一次 Pilot 跑出来的升级

我先交代一下背景。

这个月初，我用 Claude Code 建了一套 IBM i 的 Skill 体系。
从需求整理到功能规格、技术设计、程序规格、代码生成、代码审查，一条链。
8 个 Skill，文档链式串联，看起来很完整。

4 月 3 号上线。
4 月 5 号，拿了一个真实的 CR（CUR93）跑了一次 Pilot。

Pilot 的反馈很直接。

---

第一条反馈："workflow is not effect。"

说白了就是——太重了。

我们日常改 AS400 程序，大部分是小到中等的增强。
目标程序是哪个、改哪些文件、涉及什么业务规则，开发者心里都清楚。
但 Orchestrator 每次都把你往完整链路上带：需求规范化 → 功能规格 → 技术设计 → 程序规格。
一个改两个 subroutine 的 CR，非要先写一份 Functional Spec。

这个链路是给新程序开发设计的。
套在日常增强上，不是辅助，是障碍。

---

第二条反馈：生成的 fixed-format 代码"不好读"。

这个说起来也惭愧。
代码生成器输出的 RPGLE，逻辑上没问题，但打开一看——
一面墙的 C-spec。
没有 banner 分隔线，没有空行，没有注释。
Subroutine 命名也没按照 shop 里的惯例来，虽然我提供了 reference source。

做过 AS400 的人都知道，fixed-format 代码本身可读性就差。
如果连分隔线和注释都没有，维护的人会疯掉。

---

第三条发现：链条两头是断的。

Spec 到 Code 到 Review，这一段是通的。
但往前——"现在的程序长什么样？"这个问题没有 Skill 回答。
往后——"代码怎么测？"也没人管。

大部分 IBM i 的工作是改现有程序，不是写新的。
如果 AI 不先理解现有代码，直接从规格开始写，落地的时候偏差会很大。

---

反馈收到了，接下来就是改。

我按优先级分了三个方向，一个星期之内做完。

**第一件事：加快捷通道。**

做了一个 Mini Requirement Template。
你填好目标程序、现有 source、涉及的文件和业务规则，直接跳到 Program Spec。
不走 Functional Spec，不走 Technical Design。
Orchestrator 的路由逻辑也改了——如果输入已经够结构化，就走快捷路径。

这个改动不大，但对日常使用体验的影响很大。
从"每次都要走完整链路"变成"链路根据输入复杂度自适应"。

**第二件事：补两头。**

前端加了两个 Skill：
- Program Analyzer——读现有 RPGLE/CLLE source，梳理逻辑流、调用链、数据流。
- Impact Analyzer——拿着 CR 和现有 source，分析影响范围。

后端也加了两个：
- UT Plan Generator——从 Program Spec 生成单元测试计划。
- Test Scaffold——从 UT Plan 生成可执行的 SQL/CL 测试脚本。

这样链条变成了：
```
理解现有代码 → 影响分析 → 规格 → 代码 → 审查 → 测试计划 → 测试脚本
```
两头都接上了。

**第三件事：加编译安全门。**

新建了一个 Compile Precheck Skill。
在代码生成之后、编译之前插一道检查。
专门看 fixed-format 的常见坑——KLIST/KFLD 完整性、record format 引用、数组越界、I/O 操作码匹配。

同时把 fixed-format 的可读性规则写死到代码生成器里：
- 每个代码区域之间必须有 `C*****...` 的 banner 分隔线
- 逻辑段之间必须有空行
- 每个 subroutine 必须有描述性注释
- 如果提供了 reference source，必须提取并匹配它的命名惯例

另外还把公司的编程规范（AS400 Program Development Guideline）灌进了 Skill 里。
Code Reviewer 也加了一套 source validator rules。

---

改完之后，从 8 个 Skill 变成 16 个。
DDS Generator 有 31 个测试用例，Code Generator 有 8 个，Test Scaffold 有 6 个。

我回头看这次 Pilot 的过程，有一个感受比较深。

最初建这套 Skill 的时候，我觉得链路设计得挺合理的。
Pilot 一跑，第一条反馈就是"太重了"。
这不是技术问题，是我没有站在日常使用场景去想。
改 AS400 程序的人，不需要每次都从需求规范化开始。

还有一个有意思的地方。
这 16 个 Skill 里面没有一行应用代码。
全是 SKILL.md、reference 文档、example 输出、test case。
但它们组合在一起，形成了一条从"理解旧代码"到"生成测试脚本"的完整管线。

我做了十年 AS400。
以前要是有人告诉我，你可以用 AI 给 RPGLE 代码做自动 review、生成测试脚本，我大概率会说——算了吧。

但真正跑一遍就知道，门槛没有想象的高。
难的不是 AI 的能力，是你愿不愿意把自己的经验拆成规则，喂给它。

一旦你把 shop standard、命名惯例、banner style 这些写清楚了，AI 就能用。
不写清楚，它就猜。猜的结果就是——"不好读"。

这个项目还在继续。
接下来要做的是 maintained source style profile、dead-code review、还有编译反馈回流到 Skill 的闭环。
慢慢来。
