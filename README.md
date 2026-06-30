# ARIS Workflow Skill

ARIS 论文工作流计划书生成助手，用于根据用户的论文需求、研究线索和相关要求生成规范的计划书文档，并按照规范的文档运作整个研究工作流。

## 核心特点

- **交互式信息收集**: 通过问答方式收集研究需求、输入文件、输出配置等关键信息
- **路径和 Skill 检测**: 自动检测 ARIS 环境、Skills 安装状态、工具可用性
- **规范化输出目录**: 在指定根目录下创建标准化的子目录结构
- **完整计划书生成**: 生成包含环境验证、约束、配置、阶段的完整计划书

## 安装

将此文件夹复制到 Codex skills 目录：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R aris-workflow-skill "${CODEX_HOME:-$HOME/.codex/skills/"
```

## 使用方法

在 Codex 中使用自然语言调用：

```
Use $aris-workflow-skill to create a research plan for my thesis.
```

```
Use $aris-workflow-skill to generate a workflow plan based on my research.
```

## 工作流程

### 阶段 1: 信息收集与验证

在生成计划书前，会通过问答收集以下信息：

| 信息类型 | 内容 |
|---|---|
| 研究基本信息 | 研究类型、研究主题、项目简称 |
| 输入文件 | 研究方向文档、基准研究、技术框架 |
| 输出配置 | 输出目录、预计周期 |
| 约束条件 | 特殊约束和要求 |

### 阶段 2: 路径和 Skill 检测

自动检测以下内容：

```yaml
检测项:
  核心路径:
    - Auto-claude-code-research-in-sleep 根目录
    - Skills 安装目录
    - ARIS 工作流主文档
  
  Skills 可用性:
    - paper-plan, paper-write, paper-figure
    - idea-discovery, experiment-bridge
    - auto-review-loop
  
  工具可用性:
    - python3, xelatex, ffmpeg
    - 常用 Python 库
```

### 阶段 3: 输出目录创建

在用户指定的根目录下创建标准化的子目录：

```text
[输出根目录]/
├── 00-项目清单/         # 元数据、验证报告
├── 01-需求与规划/       # 研究需求、约束
├── 02-数据处理/         # 数据采集、处理产物
├── 03-分析结果/         # 图表、统计结果
├── 04-论文产出/         # 论文草稿、最终版
├── 05-计划书/           # 计划书文档
├── 06-质量验证/         # 检查点、质量报告
└── 07-调度日志/         # 工作流调度日志
```

### 阶段 4: 生成计划书

生成完整的计划书文档，包含：

- 元数据和环境验证报告
- 核心约束和研究边界
- 项目配置和工作流映射
- 执行阶段定义和质量检查
- 工作流调度配置

## 计划书核心结构

生成的计划书包含以下核心模块：

### 1. 元数据和环境验证
- 项目名称、类型、周期
- 路径检测结果
- Skills 可用性报告
- 工具可用性报告

### 2. 核心约束
- 研究方向绑定
- 基准研究绑定
- 输出目录限制
- 样本定义规则
- 数据伦理要求
- 能力缺口处理

### 3. 研究边界与核心主线
- 主线定位
- 关键链条
- 凝练表述

### 4. 项目配置
- 研究核心参数
- 输入源文件
- 输出目录结构
- ARIS 工作流映射

### 5. 执行阶段定义
- 阶段编号与名称
- 执行指令
- 输入输出
- 质量检查
- 失败处理

### 6. 工作流调度配置
- 执行模式
- 任务队列
- 数据流转
- 异常处理
- 检查点

## ARIS 工作流映射

| Workflow | 命令 | 用途 |
|---|---|---|
| Workflow 1 - Idea Discovery | `/idea-discovery` | 确认研究问题、样本范围、变量体系 |
| Workflow 1.5 - Experiment Bridge | `/experiment-bridge` | 把计划落地为可运行的数据采集和处理流水线 |
| Workflow 2 - Iterative Improvement | `/auto-review-loop` | 对数据、分析和文档进行多轮修正 |
| Workflow 3 - Paper Writing | `/paper-writing` | 撰写论文计划、图表、正文和最终稿 |

## 目录结构

```text
aris-workflow-skill/
├── SKILL.md                    # Codex 运行时入口
├── README.md                   # 本文件
├── references/                 # 参考文档
│   ├── plan-template.md        # 完整计划书模板
│   ├── stage-template.md       # 执行阶段定义模板
│   ├── quality-gate-template.md # 质量门禁设计模板
│   ├── workflow-scheduling.md  # 工作流调度配置模板
│   ├── directory-structure.md  # 输出目录结构规范
│   ├── path-detection.md       # 路径检测配置
│   ├── interaction-template.md # 用户交互模板
│   └── plan-examples.md        # 完整计划书示例
├── scripts/                    # 脚本（预留）
├── assets/                     # 资源文件（预留）
└── agents/                     # Agent 配置（预留）
```

## 支持的研究类型

### 技术方法类
- 有完整技术框架文档的研究
- 创新点明确的技术方法研究
- 适用工作流: Workflow 3

### 实证研究类
- 既有研究的延伸和扩展
- 需要数据采集和文本分析
- 适用工作流: Workflow 1 -> 1.5 -> 2 -> 3

### 文献综述类
- 系统性文献综述
- 文献检索和分析
- 适用工作流: Workflow 1 -> 3

### 混合研究类
- 结合多种研究方法
- 根据需求定制工作流

## 核心原则

| 原则 | 做法 |
|---|---|
| 先问后做 | 在生成计划书前，必须和用户确认关键资料、路径和约束 |
| 路径验证 | 检测所有输入文件、工具目录和 skill 路径是否存在且可用 |
| 规范输出 | 在指定的输出根目录下创建标准化的子目录结构 |
| 可追溯性 | 所有产物必须有明确的来源、版本和生成时间记录 |

## 验证

生成计划书后，检查以下内容：

1. **路径有效性**: 输入文件存在，输出目录可创建
2. **结构完整性**: 所有必需模块存在
3. **逻辑一致性**: 阶段依赖合理，数据流转闭环

## 许可证

本项目尚未包含许可证文件。在公开发布前，请选择并添加合适的开源许可证。

## 贡献

欢迎提交 Issue 和 Pull Request 来改进此 skill。
