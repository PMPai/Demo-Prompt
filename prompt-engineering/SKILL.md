---
name: prompt-engineering
description: "Turn a user's rough request, notes, requirements, context, or scattered information into a copy-ready high-quality prompt using exactly six sections: ROLE, REQUEST, RESOURCE, RESTRAIN, RESULT, and REFERENCE. Use when the user asks for prompt engineering, a perfect prompt, prompt rewriting, prompt optimization, or wants their input converted into a reusable AI prompt."
---

# 提示词工程

## 工作方式

把用户提供的全部信息整理成一段可以直接复制使用的高质量提示词。默认使用用户输入的语言；如果用户要求特定语言、平台、模型或场景，按用户要求处理。

除非关键信息缺失到无法生成有效提示词，否则不要先追问；用清晰占位符标出不确定项，例如 `[请补充目标受众]`、`[请粘贴原始资料]`。

## 输出规则

只输出最终提示词本身，除非用户明确要求解释、对比版本或评分。

最终提示词必须使用以下 6 个英文大写标题，顺序固定，不要增删标题：

```text
ROLE
REQUEST
RESOURCE
RESTRAIN
RESULT
REFERENCE
```

每个标题下面写完整、清楚、可执行的内容。不要把用户的零散原话简单搬运；要重组为明确任务、边界、资源、输出格式和参考标准。

## 六段内容

### ROLE

明确 AI 应该扮演的角色，包括专业身份、经验层级、思考方式、语气和服务对象。

优先写成具体角色，例如“你是一名面向 B2B SaaS 企业的资深增长顾问”，不要只写“你是一个 AI 助手”。

### REQUEST

描述 AI 要完成的工作。把用户输入中的目标、任务步骤、处理对象、判断标准和交付范围整理清楚。

如果任务复杂，拆成编号步骤。确保执行者看完这一段就知道要做什么、先做什么、后做什么。

### RESOURCE

列出 AI 可以使用的资源，包括用户提供的文本、文件、数据、链接、上下文、工具、已有草稿、品牌信息、参考材料或允许联网查询的来源。

不要编造不存在的资源。若资源缺失但明显需要，写成占位符，例如 `[上传产品说明书]`。

### RESTRAIN

写清楚约束和禁止事项，包括不要做什么、避免什么风格、不能使用哪些来源、不能越过哪些边界、哪些假设不能擅自补充。

如果用户没有提供约束，也要补充通用安全约束：不要捏造事实；不确定时明确标注；不要输出与任务无关的内容。

### RESULT

定义最终输出的形式、结构、语言、长度、格式、文件类型、表格字段、章节安排、质量标准和可验收标准。

让结果可检查、可交付、可直接使用。例如要求“输出 Markdown 表格，包含列：问题、原因、修改建议、优先级”。

### REFERENCE

放参考案例、风格样例、one-shot 或 multi-shot 示例、对标对象、可模仿的语气和格式。

如果用户没有提供参考，写明“暂无具体参考案例；请基于上述要求生成，并优先保证清晰、准确、可执行。”不要虚构案例来源。

## 质量检查

输出前检查：

- 六个标题是否完整且顺序正确。
- 每段是否能直接指导另一个 AI 执行任务。
- 是否保留了用户输入中的关键目标、背景、资源和限制。
- 是否移除了含糊措辞、空泛形容和重复内容。
- 是否没有编造事实、资源、引用或用户未提供的要求。

## 输出模板

```text
ROLE
[明确 AI 的角色、专业背景、语气和服务对象。]

REQUEST
[明确要完成的任务、步骤、范围和判断标准。]

RESOURCE
[列出可用资料、上下文、文件、链接、工具或待补充资源。]

RESTRAIN
[列出禁止事项、边界条件、风格限制和事实约束。]

RESULT
[说明最终输出格式、结构、语言、长度和验收标准。]

REFERENCE
[列出参考案例、样例、对标风格；没有时说明暂无参考案例。]
```
