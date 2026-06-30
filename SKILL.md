---
name: aris-workflow-skill
description: |
  ARIS论文工作流计划书生成助手，用于根据用户的论文需求、研究线索和相关要求生成规范的计划书文档，并按照规范的文档运作整个研究工作流。Use when the user asks to create a research plan, generate a thesis workflow plan, 研究计划书, 论文执行计划, or needs to structure a research project with clear stages, constraints, and quality gates; or wants to transform research ideas into an executable workflow document.
---

# ARIS 工作流计划书生成助手

使用本 skill 生成规范的论文研究计划书文档，将研究需求转化为可执行的工作流。

## 核心原则

| 原则 | 做法 |
|---|---|
| 先问后做 | 在生成计划书前，必须和用户确认关键资料、路径和约束 |
| 路径验证 | 检测所有输入文件、工具目录和 skill 路径是否存在且可用 |
| 规范输出 | 在指定的输出根目录下创建标准化的子目录结构 |
| 可追溯性 | 所有产物必须有明确的来源、版本和生成时间记录 |

## 工作流

### 阶段 1: 信息收集与验证

在生成计划书前，必须完成以下信息收集和验证：

#### 1.1 必需信息清单

```yaml
required_information:
  # 研究基本信息
  research_topic: "研究主题/论文题目"
  research_type: "研究类型（技术类/实证类/混合类）"
  
  # 输入文件
  research_direction_file: "研究方向文档路径（可选）"
  baseline_paper: "基准研究论文路径（可选）"
  framework_file: "技术框架文档路径（可选）"
  additional_inputs: "其他输入文件列表（可选）"
  
  # 输出配置
  output_root_dir: "输出根目录（必需）"
  project_short_name: "项目简称（用于文件命名）"
  
  # 约束条件
  timeline: "预计研究周期"
  constraints: "特殊约束或要求"
```

#### 1.2 路径检测

```yaml
path_detection:
  # ARIS 工作流路径
  aris_workflow_paths:
    - path: "/Users/didi/Desktop/code/aris-workflow.md"
      description: "ARIS 工作流主文档"
      required: false
    
    - path: "/Users/didi/Desktop/code/rag/skills"
      description: "Skills 安装目录"
      required: true
      
    - path: "/Users/didi/Desktop/code"
      description: "Auto-claude-code-research-in-sleep 根目录"
      required: true
  
  # 核心 Skills 检测
  core_skills:
    - name: "paper-plan"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/paper-plan"
        - "/Users/didi/.codex/skills/paper-plan"
      required: false
      description: "论文规划 skill"
    
    - name: "paper-write"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/paper-write"
        - "/Users/didi/.codex/skills/paper-write"
      required: false
      description: "论文写作 skill"
    
    - name: "paper-figure"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/paper-figure"
        - "/Users/didi/.codex/skills/paper-figure"
      required: false
      description: "图表生成 skill"
    
    - name: "auto-review-loop"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/auto-review-loop"
        - "/Users/didi/.codex/skills/auto-review-loop"
      required: false
      description: "自动审核循环 skill"
    
    - name: "idea-discovery"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/idea-discovery"
        - "/Users/didi/.codex/skills/idea-discovery"
      required: false
      description: "研究问题发现 skill"
    
    - name: "experiment-bridge"
      paths:
        - "/Users/didi/Desktop/code/rag/skills/experiment-bridge"
        - "/Users/didi/.codex/skills/experiment-bridge"
      required: false
      description: "实验桥接 skill"
  
  # 工具检测
  tools:
    - name: "python3"
      check: "python3 --version"
      required: true
    
    - name: "latex"
      check: "xelatex --version"
      required: false
    
    - name: "ffmpeg"
      check: "ffmpeg -version"
      required: false
```

### 阶段 2: 用户交互确认

使用 AskUserQuestion 工具向用户确认关键信息：

```yaml
interaction_sequence:
  step_1_research_basic:
    questions:
      - question: "请确认您的研究类型？"
        options: ["技术方法类", "实证研究类", "文献综述类", "混合研究类"]
      
      - question: "是否有研究方向文档或技术框架文档？"
        options: ["有研究方向文档", "有技术框架文档", "两者都有", "都没有"]
  
  step_2_input_files:
    action: "检测用户提供的文件路径是否存在"
    questions:
      - question: "检测到以下输入文件，是否正确？"
        # 动态生成选项
      
  step_3_output_config:
    questions:
      - question: "计划书和研究产物将保存到哪个目录？"
        # 使用用户输入
      
      - question: "项目简称是什么？（用于文件命名，如 jsa, cao）"
        # 使用用户输入
  
  step_4_skill_detection:
    action: "检测核心 skills 是否已安装"
    questions:
      - question: "检测到以下 skills 状态，是否继续？"
        # 显示已安装/未安装的 skills
```

### 阶段 3: 输出目录创建

在用户指定的输出根目录下创建标准化的子目录结构：

```text
[输出根目录]/
├── 00-项目清单/
│   ├── project-manifest.yaml      # 项目元数据
│   ├── source-url-registry.csv    # 来源URL注册表（如适用）
│   ├── sample-registry.csv        # 样本注册表（如适用）
│   ├── skill-availability.yaml    # Skill 可用性报告
│   ├── path-validation.yaml       # 路径验证报告
│   └── execution-log.md           # 执行日志
│
├── 01-需求与规划/
│   ├── research-requirements.md   # 研究需求文档
│   ├── research-direction.md      # 研究方向文档（从输入复制）
│   ├── baseline-summary.md        # 基准研究摘要
│   ├── constraints.yaml           # 约束条件
│   └── workflow-mapping.md        # 工作流映射
│
├── 02-数据处理/                    # 数据采集和处理产物（如适用）
│   ├── raw/                       # 原始数据
│   ├── cleaned/                   # 清洗后数据
│   ├── processed/                 # 处理后数据
│   └── metadata/                  # 元数据
│
├── 03-分析结果/                    # 分析产物
│   ├── figures/                   # 图表文件
│   ├── tables/                    # 表格文件
│   ├── statistics/                # 统计结果
│   └── analysis-reports/          # 分析报告
│
├── 04-论文产出/                    # 论文相关产物
│   ├── paper-plan.md              # 论文规划
│   ├── chapter-outline.md         # 章节大纲
│   ├── draft/                     # 论文草稿
│   ├── final/                     # 最终版本
│   └── references/                # 参考文献
│
├── 05-计划书/                      # 计划书文档
│   ├── 计划书-[项目简称].md        # 主计划书
│   ├── plan-changelog.md          # 计划书变更记录
│   └── plan-templates/            # 使用的模板
│
├── 06-质量验证/                    # 质量验证产物
│   ├── stage-checkpoints/         # 阶段检查点
│   ├── quality-reports/           # 质量报告
│   └── validation-logs/           # 验证日志
│
└── 07-调度日志/                    # 工作流调度日志
    ├── workflow-schedule.yaml     # 调度配置
    ├── task-queue.csv             # 任务队列
    └── progress.log               # 进度日志
```

### 阶段 4: 生成计划书

根据收集的信息和验证结果生成完整的计划书文档。

## 交互原则

| 场景 | 原则 |
|---|---|
| 信息不足 | 使用 AskUserQuestion 收集必需信息 |
| 路径不存在 | 提示用户并提供替代选项 |
| Skill 未安装 | 记录缺失的 skills，提供安装建议 |
| 研究类型不明 | 通过问答确认研究类型，选择合适的模板 |

## 操作路由

按用户需求和验证结果选择对应参考文档：

| 目标 | 何时读取 | 文档 |
|---|---|---|
| 计划书模板 | 需要生成完整的计划书文档 | `references/plan-template.md` |
| 阶段定义模板 | 需要设计具体执行阶段 | `references/stage-template.md` |
| 质量门禁设计 | 需要设计检查点和验证机制 | `references/quality-gate-template.md` |
| 工作流调度配置 | 需要设计任务队列和数据流转 | `references/workflow-scheduling.md` |
| 目录结构规范 | 需要创建输出目录 | `references/directory-structure.md` |
| 路径检测配置 | 需要验证路径和 skills | `references/path-detection.md` |
| 计划书示例 | 需要参考完整示例 | `references/plan-examples.md` |
| 用户交互模板 | 需要设计问答流程 | `references/interaction-template.md` |

## 输出要求

生成的计划书必须：
- 保存为 Markdown 格式
- 文件名遵循 `计划书-{项目简称}.md` 格式
- 包含完整的元数据、约束、配置、阶段定义
- 所有路径使用绝对路径
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

## 参考文档

详细模板和示例请参阅 `references/` 目录。
