你是一个对于OpenClaw非常了解的专家。同时对于 Antfarm workflow auotmation(https://github.com/snarktank/antfarm )有很深的认识。我现在要开发一整套的 Antfarm workflow， 来作为 目标客户企業智能自動化顧問框架，並將其系統化為可複製、可落地、可持續發展的諮詢方法論。工作流的名称是 3AI-Consulting

最终報告需具備戰略高度、方法論深度與可執行性。語氣專業、清晰、有結構，符合國際顧問公司（如 McKinsey / BCG / Accenture）同级别详细并且专业的内容。
 
下面是系统要求：

整个系统是分为5个连续的阶段，Workflow 建立并且调动 5个 Sub-Agent来分别执行。并且如果需要，可以依照任务在其中再建立 Sub-agent，并且严格要求每一阶段都都由前面的信息输入，不可以另外猜想。

## 阶段1： Information：
 - 主要是收集所有的相关信息，包括客户公司的 1）基本信息以及内在优势/劣势，2）实际业务操作，3）财务内外报告，4）行业整体趋势，5）现有和潜在竞争对手状况，等等的相关资料。
 - 收集的资料要尽量详细完整。并且使用 MECE 的概念，把内容整体相关资料整理为分类清楚的子项目。
 - 将本阶段所有资料输出为一个报告。和用户确认内容之后，进行修改。如果确认之后再将信息传给下一阶段。
 - 需要使用 工具： WebSearch，本地文件建立。
 - 输入：客户名称/输出：Information.html + information.json

## 阶段2： Insights：
 - 主要是将之前收集所有的相关信息，进行横向交叉分析和 SWOT 分析。寻找客户公司不同区域中的实际現象、造成問題、本質原因、关键核心。
 - 充分理解 QBQ 和 Minto Pyramid Principle 的思维方式，把每个部分的SWOT 分析结果，进行：问题，原因，来源，核心的方式。进行论述。
 - 将本阶段所有资料输出为一个报告。和用户确认内容之后，进行修改。如果确认之后再将信息传给下一阶段。
 - 需要使用工具：本地文档建立。
 - 输入：information.json / 输出：insights.html+insights.json

## 阶段3： Goal：
 - 将前期发现的“现象-关键点”的所有部分，提出简单想法作为测试思路，
 - 通过使用ROI 计算的方式，预估收益/支出，提出可量化的目标，进行评估选定最优先最具有效果的2-3个作为目标，并且界定 KPI计算方式 。
 - 将本阶段所有资料输出为一个报告。和用户确认内容之后，进行修改。如果确认之后再将信息传给下一阶段。
- 需要使用工具：本地文档建立。
- 输入：insights.json / 输出：goal.html + goal.json

## 阶段4： Initiatives：
 - 将前期的2-3个目标，通过参考其他类似的案例和跨行业的做法，提出执行方向。
 - 主要使用人工智能，提高效率或是降低成泵，或是操作方式自动化或是商业模式的创新化。指出战略方向。
 - 将本阶段所有资料输出为一个报告。和用户确认内容之后，进行修改。如果确认之后再将信息传给下一阶段。
 - 需要使用工具：本地文档建立。
 - 输入： goal.json / 输出：Initiatives.html + initatives.json

## 阶段5： Implementation：
 - 将前期的主要方向，做成可以具体执行的操作方式和手段。包括：建议系统架构建议/沙箱测试机制/知识转移计划/12–18个月路线图/预算
 - 考量 所需的资源或技能，考量预算和时间计划等在执行中的必要条件。
 - 将之前所有的资料，全部汇总为一份完整的报告，使用HTML 的方式进行风格调试。同时列出所有参考的资料。
- 需要使用工具：本地文档建立。
- 输入：initatives.json / 输出: Master.html


## 系统需求：
1.每一个阶段的报告都用阶段名称做文件名（如：Information stage -> information.html)。 并且确保每一个阶段，都清楚定义了输入/输出，如：information.josn 文档是作为下一个insights 阶段的输入。
2.对于 报告中的图像，如：SWOT，Minto的金字塔，使用图像加入实际的内容，而不是泛泛之轮。
3.对于上述的5个阶段，请建立 一个完整的建立的 workflow.yml 和 agents directory 的设立文档，能够在 Antfarm 中执行，然后打包成一个zip file。
4. 每一个报告的HTML，风格是专业扼要，清楚分明，使用多层次的排版（H1/H2/H3/...)，最重要的是所有报告的排版风格完全统一。

# 方法论

下列是公司顾问系统的方法论：

一，本报告的目的在于建立一套 3A 框架，将分散的技术试点转化为端到端（E2E）的业务重塑 [Source: 2: The Roadmap for End-to-End Reinvention | BCG] 。通过系统化的咨询方法论，帮助中小企业从“人工操作”过渡到“智能驱动”，实现效率与效能的双重飞跃。

二、 核心方法论：双三角顾问模型本框架的核心是“双三角模型”，它将咨询过程分为 “策略收敛” 与 “执行展开” 两个阶段，确保每一个技术投入都直接指向企业的核心战略目标。

## 第一三角：
策略收敛（Information → Insight → Goal）这是发现价值的过程，强调“做正确的事”。

### Information（全方位资讯收集） 

采用 MECE 原则（相互独立，完全穷尽）进行企业诊断 [Source: 3: The Minto Pyramid Principle Explained (With Example Frameworks) - BetterUp]  [Source: 4: The Minto Pyramid Principle Explained with Examples] 。

- 收集范围涵盖：端到端流程、历史数据、组织结构、IT 架构、决策模式、业务瓶颈及现有 KPI [Source: 5: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 6: When Lean Meets Industry 4.0 - Boston Consulting Group] 。    
- 应用 QBQ（The Question Behind the Question） 技巧，不满足于表面需求，而是聚焦于真正的经营问题 [Source: 7: AAA- Pai：Consultant Framework V1.pdf] 。例如：客户要求的不是“更快的 RPA”，而是“更低的客户流失率”。    
- 核心理念： 基于经营实务经验进行判断，而非单纯依赖数据分析，避免“垃圾进，垃圾出” [Source: 8: The Minto Pyramid Principle Explained with Examples] 。


### Insight（结构化洞察）：    

- 使用 Minto 金字塔原则 进行逻辑重组 [Source: 9: The Minto Pyramid Principle Explained (With Example Frameworks) - BetterUp] 
- 将分散的信息上升为结构化洞察，明确区分现象（如报表延迟）、问题（如数据孤岛）、本质原因（如缺乏 API 集成）与可控杠杆（如引入 iPaaS） [Source: 10: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 11: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 。 

### Goal（战略目标定义）：    

- 确定最具影响力的目标。目标必须具备 KPI 化 的可衡量性（如：降低 40% 的操作成本） [Source: 12: Maximizing Value Potential from AI in 2025] 。    
- 目标必须与企业现实能力（人才、预算、数字化成熟度）匹配 [Source: 13: 1.1 -Deep Research Report:  全球领先咨询机构企业内部流程自动化实施方法论与理论架构深度研究报告] 。 

## 第二三角：
执行展开（Goal → Initiative → Implementation）这是创造价值的过程，强调“把事做正确”。

### Initiative（关键战略行动）：    

- 将目标拆解为 2–3 个关键行动。每个行动必须对应明确的 价值假设 [Source: 14: Maximizing Value Potential from AI in 2025] 。例如：为了降低采购成本，关键行动可能是“建立 AI 询价助理” [Source: 7: AAA- Pai：Consultant Framework V1.pdf] 。

 ### Implementation（具体执行模组）：    

 将每个 Initiative 细化为可交付的模块：        

-  系统建设： 现代化数字底座，连接孤立系统 [Source: 15: Digital Maturity Model and Digital Pivots | Deloitte Insights] 。 
-  AI 应用： 导入 LLM 驱动的智能代理（Agentic AI）或预测模型 [Source: 16: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 17: Intelligent Automation - KPMG International] 。        
- 自动流程： 实施超自动化（Hyperautomation）技术 [Source: 18: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 19: Hyperautomation - The next frontier - Deloitte] 。        
- 组织与流程优化： 重新定义岗位职能，进行人机协作设计 [Source: 20: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 21: Maximizing Value Potential from AI in 2025] 。    
- 明确执行步骤、时间表与责任分工（RACI 矩阵）。

## 3A 服务的核心定位

### Analysis（结构化诊断）

-  流程可视： 使用流程挖掘（Process Mining）工具获得端到端的透明度 [Source: 5: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 。
-  数据驱动： 建立机会热图（Opportunity Heatmap），识别高价值、低难度的切入点 [Source: 22: Maximizing Value Potential from AI in 2025] 。
-  KPI 优化： 建立 KPI 驱动树，将财务目标下放至操作指标 [Source: 23: The Roadmap for End-to-End Reinvention | BCG]  [Source: 24: Unlocking Potential from AI and GenAI] 。

### AI（人工智能应用）

- 场景设计： 针对特定职能（如财务、采购、HR）设计 AI 助理 [Source: 25: Intelligent Automation - KPMG International]  [Source: 26: Unlocking Potential from AI and GenAI] 。
- 决策支援： 利用 AI 进行预测性分析（如需求预测、风险监测） [Source: 27: Maximizing Value Potential from AI in 2025]  [Source: 28: Unlocking Potential from AI and GenAI] 。
- 人机协作： 设计“人在回路中”（Human-in-the-loop）的审核机制，确保 AI 生成结果的合规与质量 [Source: 29: Digital Maturity Model and Digital Pivots | Deloitte Insights]  [Source: 30: Intelligent Automation - KPMG International] 。


### Automation（流程自动化）

- 超自动化： 整合 RPA、AI、iBPMS 和低代码工具，消除重复性手动任务 [Source: 11: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 18: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 。
- 系统互联： 通过 API 整合，打破数据孤岛 [Source: 5: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 。
- 工作流改造： 从底层重塑流程，而非简单地“自动化现有的浪费” [Source: 31: The Roadmap for End-to-End Reinvention | BCG]  [Source: 32: When Lean Meets Industry 4.0 - Boston Consulting Group] 。
- 

## 我们的竞争优势

### 第一三角的核心能力：

- 真实洞察力• 创新收集： 结合深度访谈、专家影子观测与数字化工具，挖掘企业真实的“非结构化”信息。
- 实战经验： 我们的顾问拥有丰富的经营背景，能区分什么是“完美的理论”，什么是“实际的可行性” [Source: 7: AAA- Pai：Consultant Framework V1.pdf] 。
-  QBQ 导向： 拒绝形式主义，直击影响经营结果的关键变量。 

### 第二三角的核心能力：

- 敏捷落地力• 全球视野与本地实务： 掌握全球最新技术趋势（如自主 AI 代理），并结合本地企业的数字化阶段进行降维打击 [Source: 16: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 33: Industrial Business Consulting Services &amp; Solutions - Accenture] 。 
- 快速落地： 强调 MVP（最小可行性产品）策略，在 12 周内产生可见成效 [Source: 10: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 。
- 弹性架构： 方案具有可扩展性，确保技术架构能随业务增长而演进。


## 持续经营与知识转移机制
为避免“系统荒废”与“技术过时”，我们设计了以下保障机制：

1. 建立内部卓越中心（CoE）： 在咨询结束后，协助企业建立自己的自动化与 AI 治理团队 [Source: 34: Intelligent Automation - KPMG International]  [Source: 35: Intelligent Automation - KPMG International] 。
2. 人才赋能与再培训： 设计“公民开发者”计划与 Prompt 工程培训，提升员工的数字化技能 [Source: 36: 4 Steps to a Hyperautomation Roadmap - ProcessMaker]  [Source: 37: Maximizing Value Potential from AI in 2025] 。
3. 价值监控体系： 部署实时看板，监控自动化的投资回报率（ROTI），确保持续效率提升 [Source: 38: The Roadmap for End-to-End Reinvention | BCG]  [Source: 39: The Roadmap for End-to-End Reinvention | BCG] 。
4. 动态迭代机制： 建立每半年的技术复盘计划，针对最新的 AI 进展升级现有模型 [Source: 40: Maximizing Value Potential from AI in 2025] 。

## 顾问服务 Roadmap (12–18 个月)阶段周期核心活动交付物阶段 

1. 诊断与蓝图月 1-3数字化成熟度评估、价值流分析 [Source: 43: Maximizing Value Potential from AI in 2025]  [Source: 44: The Roadmap for End-to-End Reinvention | BCG] 数字化转型战略蓝图、KPI 框架阶段 
2. MVP 试点月 4-6选择高价值场景（如财务结账、客户服务）进行试点 [Source: 45: Maximizing Value Potential from AI in 2025] 最小可行性产品、业务验证报告阶段 
3. 全面落地月 7-12跨职能端到端自动化重塑、AI 引擎集成 [Source: 2: The Roadmap for End-to-End Reinvention | BCG]  [Source: 46: 4 Steps to a Hyperautomation Roadmap - ProcessMaker] 自动化工作流、AI 决策支援系统阶段 
4. 能力转让月 13-18内部人才再造、卓越中心（CoE）移交 [Source: 35: Intelligent Automation - KPMG International] 培训手册、认证证书、持续经营指南
5.  持续沟通，确保内部的信息保持最新最优的能力。同时对于公司的战略部分提出建议和支持。


# 结论： 
3A 框架不仅是一套技术指南，更是一场管理革命。通过双三角模型，我们将确保中小企业在智能自动化的浪潮中，不只是观望者，而是真正的价值收获者。
