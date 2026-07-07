---
name: aris-workflow-skill
description: |
  ARIS论文工作流计划书生成助手，用于根据用户的论文需求、研究线索和相关要求生成规范的计划书文档，并按照规范的文档运作整个研究工作流。Use when the user asks to create a research plan, generate a thesis workflow plan, 研究计划书, 论文执行计划, or needs to structure a research project with clear stages, constraints, and quality gates; or wants to transform research ideas into an executable workflow document.
---

# ARIS 工作流计划书生成助手

将研究想法、论文需求、输入资料和约束转成可执行的 ARIS 研究计划书。

## 核心原则

| 原则 | 做法 |
|---|---|
| 先确认再生成 | 在生成计划书前，必须和用户确认关键资料、路径和约束 |
| 路径和输入验证 | 检测所有输入文件、工具目录和 skill 路径是否存在且可用 |
| 输出目录标准化 | 在指定的输出根目录下创建标准化的子目录结构 |
| 可执行、可追踪、可验收 | 计划书要有明确的阶段、质量门禁和交付物 |
| 不编造缺失信息 | 缺失的资料、路径或约束必须标注为待确认 |

## 核心工作流

1. **收集研究信息**
   - 研究主题、研究类型、输入文件、输出目录、项目简称、约束条件
   - 研究类型：技术方法类、实证研究类、文献综述类、混合研究类

2. **验证路径和可用 skill**
   - 检测输入文件是否存在
   - 检测核心 skills（paper-plan、paper-write、paper-figure、idea-discovery 等）是否已安装
   - 检测工具（python3、xelatex、ffmpeg 等）是否可用
   - 生成路径验证报告和 skill 可用性报告

3. **创建或规划输出目录**
   - 在用户指定的输出根目录下规划标准化子目录结构
   - 按研究类型决定必需和可选的子目录

4. **生成计划书**
   - 根据收集的信息和验证结果生成完整计划书文档
   - 包含元数据、输入来源、约束、阶段、质量门禁、路径验证和 skill 可用性

5. **设计阶段、质量门禁和调度**
   - 按研究类型选择合适的阶段模板
   - 设计阶段间的依赖、数据流转和质量检查点
   - 配置调度日志和任务队列

6. **输出验证结果和待确认项**
   - 列出所有检测到的错误和警告
   - 列出缺失信息或需要用户补充确认的内容

## 操作路由

| 目标 | 何时读取 | 文档 |
|---|---|---|
| 计划书模板 | 需要生成完整的计划书文档 | `references/plan-template.md` |
| 阶段定义模板 | 需要设计具体执行阶段 | `references/stage-template.md` |
| 质量门禁设计 | 需要设计检查点和验证机制 | `references/quality-gate-template.md` |
| 工作流调度配置 | 需要设计任务队列和数据流转 | `references/workflow-scheduling.md` |
| 目录结构规范 | 需要创建输出目录 | `references/directory-structure.md` |
| 路径检测配置 | 需要验证路径和 skills | `references/path-detection.md` |
| 用户交互模板 | 需要设计问答流程 | `references/interaction-template.md` |
| 计划书示例 | 需要参考完整示例或生成风格不明确 | `references/plan-examples.md` |

## 输出要求

生成的计划书必须：

- 保存为 Markdown 格式
- 文件名遵循 `计划书-{项目简称}.md` 格式
- 包含完整的元数据、约束、配置、阶段定义
- 路径可接收相对路径、`~` 路径或绝对路径；计划书中保留用户原始输入（raw），并在路径验证报告中记录解析后的绝对路径（resolved）
- 包含路径验证报告和 skill 可用性报告
- 包含质量检查机制和失败处理方案

## 验证

生成计划书后，运行以下检查：

1. **路径有效性检查**
   - 所有输入文件是否存在
   - 输出目录是否可创建
   - 必需的 skills 是否可用

2. **结构完整性检查**
   - 所有必需模块是否存在
   - 章节编号是否连续

3. **逻辑一致性检查**
   - 阶段依赖是否合理
   - 数据流转是否闭环