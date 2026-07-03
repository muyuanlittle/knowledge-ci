随着 Claude Code、Cursor、OpenCode 等 AI Coding 工具逐渐进入真实软件工程流程，代码生产方式正在发生根本变化。软件开发不再完全依赖人类开发者逐行实现，而是逐渐转向由人类提出目标、AI Agent 执行任务、人类进行校验与治理的新型协作模式。

在这一趋势下，代码变更速度显著提升，复杂需求的交付效率也不断提高。与此同时，一个长期被低估的问题正在被快速放大：**项目知识的维护速度远远落后于代码演化速度**。

Spec、ADR、README、Design Doc、Technical Note 等知识资产原本用于解释系统设计、业务约束与历史决策。但在 AI Coding 加速代码演化之后，这些知识资产往往无法同步更新，逐渐与真实代码产生偏差，最终导致知识漂移、知识债务和知识流失。

**Knowledge CI** 旨在解决这一问题。它不是另一个文档生成工具，也不是传统意义上的 RAG 系统，而是一套面向 AI Coding 时代的 **知识生命周期管理系统**。其核心目标是让项目知识像代码一样具备持续采集、持续演化、持续验证、持续保全和持续复用的工程治理能力。

---

## 1. 背景：AI Coding 正在重塑软件工程

### 1.1 从人驱动开发到 AI 协同开发

过去的软件开发主要由人类开发者完成需求理解、方案设计、代码实现、测试修复和上线发布。随着 AI Coding 工具的发展，软件工程流程正在逐渐演化为：

```text
Requirement
↓
Spec
↓
Task Breakdown
↓
AI-assisted Implementation
↓
Review
↓
Deploy
```

在这一模式下，开发者不再只是代码编写者，而逐渐成为需求定义者、架构把关者、上下文提供者和结果审查者。

Spec Driven Development 已经成为复杂需求开发中的重要实践。越来越多团队开始将以下内容作为项目核心资产维护：

- Product Spec
- ADR（Architecture Decision Record）
- Design Document
- README
- Technical Note
- Engineering Note
- Incident Review
- Architecture Guide

这些知识资产帮助团队沉淀系统设计、业务规则、架构决策和历史背景，是大型软件长期可维护性的基础。

### 1.2 新问题：知识生命周期开始断裂

Spec 能够帮助团队在需求开发阶段建立明确约束，但它并不能自动保证知识在项目上线后持续演化。

真实项目上线后会经历大量持续迭代：

- Bug Fix
- Refactor
- Hotfix
- Vibe Coding
- Performance Optimization
- Business Compromise
- Temporary Workaround
- Legacy Compatibility
- Customer-specific Logic

这些变化会持续改变系统真实行为。

但知识资产往往不会同步变化。最终会出现：

```text
Spec ≠ Code
ADR ≠ Architecture
README ≠ Reality
Design Doc ≠ Current Implementation
```

也就是说，代码仍在持续演进，但知识停止了演进。

### 1.3 AI Coding 放大了知识流失问题

在传统开发时代，代码演化速度受到开发者数量、评审流程和交付节奏的限制。知识维护虽然经常滞后，但滞后速度相对可控。

AI Coding 之后，代码生成速度提升了一个数量级，修改成本显著下降，迭代频率明显提高。

于是形成新的失衡：

```text
知识漂移速度 > 知识维护速度
```

结果是，开发者和 AI Agent 越来越容易看到：

> 系统现在是什么样。

却越来越难理解：

> 系统为什么变成这样。

这正是 Knowledge CI 要解决的核心问题。

---

## 2. 问题定义

Knowledge CI 将项目知识问题拆解为三个层次：Knowledge Drift、Knowledge Debt 和 Knowledge Loss。

### 2.1 Knowledge Drift：知识漂移

Knowledge Drift 指知识资产仍然存在，但内容已经不再准确。

例如：

```text
文档：支付失败后最多重试 5 次
代码：retryCount = 3
```

此时文档并没有缺失，但它已经无法准确描述系统真实行为。

知识漂移的风险在于：

- 开发者基于错误文档做设计
- AI Agent 基于过期上下文生成错误代码
- Review 时误判代码是否符合规范
- 新成员学习到错误系统知识

Knowledge Drift 是知识腐败最常见、也最隐蔽的形式。

### 2.2 Knowledge Debt：知识债务

Knowledge Debt 指代码中存在大量需要解释但没有解释的区域。

典型表现包括：

- Magic Number
- Legacy Branch
- Customer Hack
- Temporary Fix
- Long-term TODO
- Business Exception
- Complex Conditional Logic
- Historical Compatibility Code

这些代码未必是坏代码，但它们背后通常存在重要背景：

- 为什么阈值是这个数字？
- 为什么这个客户需要特殊逻辑？
- 为什么这里不能删除？
- 为什么采用这个非直觉实现？
- 为什么临时方案存在多年？

如果这些背景没有被记录，系统就会积累知识债务。

知识债务不同于技术债务。技术债务关注代码质量，知识债务关注的是：

> 代码背后的原因是否仍然可被理解。

### 2.3 Knowledge Loss：知识流失

Knowledge Loss 是更严重的问题。它指系统中的决策原因已经无法被恢复。

例如：

```text
if risk_score > 80:
    reject_order()
```

开发者可以看到系统行为：

> 风控分大于 80 时拒单。

但无法知道：

> 为什么阈值是 80？  
> 是否来自历史事故？  
> 是否来自业务指标？  
> 是否经过实验验证？  
> 是否与某个大客户有关？  
> 是否仍然有效？

这类知识一旦流失，团队只能通过猜测、访谈、考古和线上事故重新学习。

AI Coding 会进一步放大这一风险，因为 AI Agent 能够快速修改代码，却无法天然知道历史决策背景。

---

## 3. 产品定位

### 3.1 Knowledge CI 是什么

Knowledge CI 是一套面向 AI Coding 时代的知识生命周期管理系统。

它通过自动化方式持续管理项目知识，包括：

- 采集项目知识来源
- 建立知识图谱
- 分析代码变更影响范围
- 发现知识漂移
- 生成知识补丁
- 评估知识变更风险
- 保全历史决策上下文
- 向 AI Agent 注入关键知识

其目标是让知识资产像代码一样进入持续治理流程。

### 3.2 Knowledge CI 不是什么

Knowledge CI 不试图成为：

1. **另一个文档生成工具**  
   它不是在代码变更后重新生成一批 Markdown 文档。

2. **另一个 RAG 系统**  
   它不只是把文档向量化后提供检索能力。

3. **另一个 Wiki 平台**  
   它不以人工维护页面作为核心模式。

4. **另一个代码质量扫描器**  
   它不评价代码是否优雅，而是评估知识是否缺失、过期或不可追溯。

Knowledge CI 关注的是：

> 项目知识是否能够随着代码持续演化，并在 AI Coding 过程中被正确使用。

---

## 4. 核心理念：Knowledge As State

传统文档系统通常采用：

```text
Document As File
```

也就是说，知识被视为仓库中的文件，例如：

```text
docs/payment.md
adr/001-risk-control.md
README.md
```

这种方式在人类协作时代可以工作，但在多 Agent、高频变更、复杂上下文的 AI Coding 时代存在明显局限。

Knowledge CI 采用：

```text
Knowledge As State
```

但这里的 Knowledge State 不应从一开始就要求系统抽取完整、稳定、边界清晰的“知识实体”。在真实项目中，知识实体往往很难一次性定义清楚：同一条业务规则可能散落在代码、README、ADR、PR 和事故复盘中；不同团队对同一规则的命名也可能并不一致。

因此，Knowledge CI 采用更容易落地的渐进式建模方式：

```text
Source Anchor
↓
Knowledge Claim
↓
Directory Knowledge Unit
↓
Knowledge State
```

### 4.1 Source Anchor：从可定位来源开始

Source Anchor 是最小的可追溯来源，可以是：

- 一个文件
- 一个目录
- 一个代码符号
- 一个文档章节
- 一个 PR
- 一个 Issue
- 一段 Commit Message
- 一条 Review Comment

Source Anchor 的目标不是理解全部语义，而是先回答：

> 这条知识来自哪里？

### 4.2 Knowledge Claim：抽取可验证的知识声明

Knowledge Claim 是比“知识实体”更小、更容易抽取的单元。

它通常是一条可以被验证的事实、规则或约束，例如：

- 支付失败后最多重试 3 次
- 风控分大于 80 时拒单
- RefundService 依赖 PaymentService 的最终状态
- 某客户保留特殊兼容逻辑
- 某个临时方案来自一次线上事故

Knowledge Claim 的关键是可验证，而不是完整。

每条 Knowledge Claim 至少包含：

- Claim ID
- Claim Text
- Source Anchor
- Related Code
- Evidence
- Confidence
- Last Verified Time

### 4.3 Directory Knowledge Unit：知识单元优先按目录结构划分

在早期阶段，系统不强求抽取完整的 Payment Retry Policy、Risk Threshold Strategy 这类复杂语义实体。更可落地的方式，是先按照仓库目录结构建立第一层知识边界。

也就是说，Knowledge CI 默认将目录视为一等知识单元：

```text
src/payment/
↓
Payment Directory Knowledge Unit
↓
聚合该目录下的代码、文档、测试、配置和变更记录
```

每个目录知识单元负责维护该目录范围内的 Knowledge Claim、Source Anchor、Evidence、Decision Context 和 Knowledge Debt Signal。

这种划分方式有几个好处：

- 与真实代码组织方式一致，易于落地
- 可以复用仓库已有的模块边界和 ownership
- 便于按目录做增量扫描、影响分析和权限控制
- 降低早期抽取复杂业务实体的难度
- 允许后续再从目录内的 Claim 聚合出跨目录 Topic 或 Policy

因此，Knowledge CI 不要求一开始就按抽象业务概念精准建模，而是优先按目录结构生成 Directory Knowledge Unit。复杂语义实体可以作为目录知识单元之上的二级聚合结果逐步形成。

### 4.4 Knowledge State：管理状态，而不是追求一次性建模完整

Knowledge CI 管理的是持续演化的知识状态。

初始阶段只需要做到：

- 知识来源可定位
- 知识声明可验证
- 影响范围可追踪
- 证据链可回溯
- 目录边界可治理
- 风险等级可评估

复杂语义实体可以在后续阶段从一个或多个目录知识单元中逐步生成和合并。

这样可以显著降低系统落地难度，避免一开始就陷入复杂语义实体抽取问题。

---

## 5. 系统目标

Knowledge CI 的核心目标包括：

1. **自动发现知识漂移**  
   持续检测 Spec、ADR、README 与代码实现之间的不一致。

2. **自动演化知识资产**  
   在代码变更后生成 Knowledge Patch，推动知识资产同步更新。

3. **自动发现知识债务**  
   识别 Magic Number、Legacy Logic、Customer Hack 等缺少解释的代码区域。

4. **自动恢复历史设计上下文**  
   从 Git Log、PR、Issue、ADR 和 Commit History 中恢复决策背景。

5. **自动向 AI 注入关键知识**  
   在 AI Agent 修改相关代码前，提供历史事故、业务约束和设计理由。

6. **自动判断是否需要人工介入**  
   根据影响范围、业务风险和置信度动态决定自动处理、AI 建议或人工审核。

---
