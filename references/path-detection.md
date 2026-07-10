# 路径检测配置

本文档定义了 ARIS 工作流计划书生成前的路径和 skill 可用性检测机制。

> 阅读导航：先按“路径解析规则”规范化输入；再按“检测配置”检查目录、skills 和工具；最后用“验证报告”输出结果。论文依赖分为编排 skill 与可独立调用的子 skill。

## 检测流程

```text
开始
  ↓
检测用户提供的输入文件
  ↓
检测输出目录可创建性
  ↓
检测 Skills 安装目录（推荐）
  ↓
检测辅助 Skills 可用性（推荐）
  ↓
检测工具和 Python 库可用性（推荐）
  ↓
生成检测报告
  ↓
向用户确认
```

## 路径检测分层原则

路径检测分为三层：

| 层级 | 说明 | 缺失时处理 |
|---|---|---|
| **必需** | 用户提供的输入文件、输出目录、计划书写入位置 | 报错或列为待确认项，不继续生成 |
| **推荐** | 论文写作相关 skills、LaTeX、数据处理库、常用工具 | 警告，继续生成计划书框架，在报告中列出后续依赖缺口 |
| **本地增强** | 作者机器上的特定工作流路径（可选） | 忽略或警告，不影响通用用户 |

## 路径检测配置

### 1. 必需路径（用户输入）

```yaml
required_paths:
  # 用户必须提供的输入文件（如适用）
  input_files:
    description: "用户提供的输入文件（研究方向文档、基准论文、技术框架等）"
    source: "user_input"
    validation:
      - check_exists: true
      - on_missing: "error"
      - message: "输入文件不存在，请确认路径"
    
  # 用户必须指定的输出目录
  output_directory:
    description: "计划书和研究产物的输出根目录"
    source: "user_input"
    validation:
      - check_parent_exists: true
      - check_writable: true
      - on_missing: "error"
      - message: "输出目录父目录不存在或不可写"
    
  # 用户必须提供的项目简称
  project_short_name:
    description: "项目简称，用于文件命名"
    source: "user_input"
    validation:
      - pattern: "^[a-z0-9-]+$"
      - min_length: 2
      - max_length: 20
      - on_missing: "prompt_user"
```

### 2. 推荐路径（Skills 安装目录）

```yaml
recommended_skills_paths:
  # Skills 安装目录检测顺序
  skills_install_dirs:
    description: "Skills 安装目录"
    search_order:
      - path: "<project-root>/.claude/skills"
        description: "项目级 skills 目录"
      - path: "${HOME}/.claude/skills"
        description: "Claude Code 用户级 skills 目录"
      - path: "${CODEX_HOME:-$HOME/.codex}/skills"
        description: "Codex skills 目录"
      - path: "<user-specified>"
        description: "用户显式指定的 skills 目录"
    required: false
    on_missing: "warn"
    message: "Skills 安装目录不存在，部分辅助 skills 可能不可用"
```

### 3. 核心 Skills 检测（推荐）

```yaml
recommended_skills_detection:
  # 完整论文写作编排器
  paper_writing:
    name: "paper-writing"
    role: "orchestrator"
    description: "完整论文写作流水线 skill"
    search_order:
      - "<project-root>/.claude/skills/paper-writing"
      - "${HOME}/.claude/skills/paper-writing"
      - "${CODEX_HOME:-$HOME/.codex}/skills/paper-writing"
    required: false
    required_for: "完整 Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
    fallback: "按顺序调用 paper-plan、paper-figure、paper-write、paper-compile"

  # 论文规划类
  paper_plan:
    name: "paper-plan"
    role: "component"
    description: "论文规划 skill"
    search_order:
      - "<project-root>/.claude/skills/paper-plan"
      - "${HOME}/.claude/skills/paper-plan"
      - "${CODEX_HOME:-$HOME/.codex}/skills/paper-plan"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
    install_hint: "从 skills 仓库获取或手动创建"
  
  paper_write:
    name: "paper-write"
    role: "component"
    description: "论文写作 skill"
    search_order:
      - "<project-root>/.claude/skills/paper-write"
      - "${HOME}/.claude/skills/paper-write"
      - "${CODEX_HOME:-$HOME/.codex}/skills/paper-write"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  paper_figure:
    name: "paper-figure"
    role: "component"
    description: "图表生成 skill"
    search_order:
      - "<project-root>/.claude/skills/paper-figure"
      - "${HOME}/.claude/skills/paper-figure"
      - "${CODEX_HOME:-$HOME/.codex}/skills/paper-figure"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  paper_compile:
    name: "paper-compile"
    role: "component"
    description: "论文编译 skill"
    search_order:
      - "<project-root>/.claude/skills/paper-compile"
      - "${HOME}/.claude/skills/paper-compile"
      - "${CODEX_HOME:-$HOME/.codex}/skills/paper-compile"
    required: false
    required_for: "Workflow 3 - Paper Writing (LaTeX)"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 自动改进类
  auto_review_loop:
    name: "auto-review-loop"
    description: "自动审核循环 skill"
    search_order:
      - "<project-root>/.claude/skills/auto-review-loop"
      - "${HOME}/.claude/skills/auto-review-loop"
      - "${CODEX_HOME:-$HOME/.codex}/skills/auto-review-loop"
    required: false
    required_for: "Workflow 2 - Iterative Improvement"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 研究发现类
  idea_discovery:
    name: "idea-discovery"
    description: "研究问题发现 skill"
    search_order:
      - "<project-root>/.claude/skills/idea-discovery"
      - "${HOME}/.claude/skills/idea-discovery"
      - "${CODEX_HOME:-$HOME/.codex}/skills/idea-discovery"
    required: false
    required_for: "Workflow 1 - Idea Discovery"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 实验桥接类
  experiment_bridge:
    name: "experiment-bridge"
    description: "实验桥接 skill"
    search_order:
      - "<project-root>/.claude/skills/experiment-bridge"
      - "${HOME}/.claude/skills/experiment-bridge"
      - "${CODEX_HOME:-$HOME/.codex}/skills/experiment-bridge"
    required: false
    required_for: "Workflow 1.5 - Experiment Bridge"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
```

### 4. 工具检测（推荐）

```yaml
tools_detection:
  python3:
    name: "python3"
    description: "Python 3 解释器"
    check_command: "python3 --version"
    required: true
    on_missing: "error"
    install_hint: "安装 Python 3: https://www.python.org/downloads/"
  
  pip3:
    name: "pip3"
    description: "Python 包管理器"
    check_command: "pip3 --version"
    required: false
    on_missing: "warn"
  
  xelatex:
    name: "xelatex"
    description: "XeLaTeX 编译器"
    check_command: "xelatex --version"
    required: false
    required_for: "LaTeX 论文编译"
    on_missing: "warn"
    install_hint: "安装 MacTeX 或 TeX Live"
  
  ffmpeg:
    name: "ffmpeg"
    description: "音视频处理工具"
    check_command: "ffmpeg -version"
    required: false
    required_for: "视频/音频数据处理"
    on_missing: "warn"
    install_hint: "brew install ffmpeg (macOS) 或 apt install ffmpeg (Linux)"
  
  git:
    name: "git"
    description: "版本控制"
    check_command: "git --version"
    required: false
    on_missing: "warn"
```

### 5. Python 库检测（推荐）

```yaml
python_libraries_detection:
  pandas:
    import: "pandas"
    required: false
    required_for: "数据处理和分析"
    install: "pip install pandas"
  
  numpy:
    import: "numpy"
    required: false
    required_for: "数值计算"
    install: "pip install numpy"
  
  jieba:
    import: "jieba"
    required: false
    required_for: "中文分词"
    install: "pip install jieba"
  
  matplotlib:
    import: "matplotlib"
    required: false
    required_for: "图表生成"
    install: "pip install matplotlib"
  
  pypdf:
    import: "pypdf"
    required: false
    required_for: "PDF 读取"
    install: "pip install pypdf"
```

### 6. 本地增强路径（可选，作者机器专用）

以下为作者本机示例，不是默认要求。公开发布前应替换为占位符或移除。

```yaml
local_enhancement_paths:
  # 作者本机的工作流文档路径（可选）
  aris_workflow_main:
    description: "ARIS 工作流主文档（作者本机增强）"
    paths:
      - "<workspace-root>/aris-workflow.md"
    required: false
    on_missing: "ignore"
    fallback: "将在计划书中使用通用模板"
  
  # 作者本机的特定项目路径（可选）
  custom_project_root:
    description: "特定项目根目录（作者本机增强）"
    paths:
      - "<workspace-root>"
    required: false
    on_missing: "ignore"
    note: "此配置仅供作者本机增强使用，通用用户无需配置"
```

## 路径解析规则

接收用户输入时允许以下路径格式：

| 格式 | 示例 | 处理方式 |
|---|---|---|
| 绝对路径 | `/Users/xxx/data/source.pdf` | 直接使用 |
| `~` 路径 | `~/Desktop/papers/output` | 扩展为 `${HOME}/...` |
| 相对路径 | `./data/source.pdf` | 相对于当前工作目录解析 |
| 项目相对路径 | `<project-root>/data` | 相对于项目根目录解析 |
| 环境变量 | `${CODEX_HOME}/skills` | 扩展环境变量 |

在生成计划书时，同时保留原始输入和解析后的路径：

```yaml
path:
  raw: "./data/source.pdf"
  resolved: "/absolute/path/to/data/source.pdf"
```

## 检测报告格式

```yaml
# skill-availability.yaml 示例

timestamp: "2024-06-30T10:00:00"

results:
  required_paths:
    input_files:
      - path: "<research-root>/研究方向.md"
        status: "available"
        raw: "~/Desktop/研究方向.md"
        resolved: "/Users/xxx/Desktop/研究方向.md"
    
    output_directory:
      status: "available"
      raw: "~/papers/output"
      resolved: "/Users/xxx/papers/output"
  
  recommended_skills:
    paper-writing:
      role: "orchestrator"
      available: true
      path: "${HOME}/.claude/skills/paper-writing"

    paper-plan:
      role: "component"
      available: true
      path: "${HOME}/.claude/skills/paper-plan"
    
    paper-write:
      role: "component"
      available: false
      path: null
      note: "后续论文写作需要此 skill"
  
  tools:
    python3:
      available: true
      info: "Python 3.11.0"
    
    xelatex:
      available: false
      info: "command not found"
      note: "LaTeX 编译不可用，论文写作阶段可能需要"

errors: []

warnings:
  - "推荐 Skill 未安装: paper-write (论文写作 skill)"
  - "推荐工具缺失: xelatex (LaTeX 编译器)"

summary:
  total_errors: 0
  total_warnings: 2
  skills_available: 5
  skills_total: 8
  blocking_issues: 0
  enhancement_gaps: 2
```

## 缺失处理策略

| 缺失类型 | 处理方式 |
|---|---|
| 必需路径缺失 | 报错，停止生成，要求用户确认或修正 |
| 推荐 skill 缺失 | 警告，继续生成计划书框架，在报告中列出"后续依赖缺口" |
| 推荐工具缺失 | 警告，继续生成，标注特定阶段可能受限 |
| 本地增强路径缺失 | 忽略，不影响通用用户 |

## 发布前检查清单

- 检查 references 中是否残留个人绝对路径
- 将本机示例移入"本地增强"小节并明确标注
- 确保路径检测配置使用占位符或环境变量
- 确保必需路径只包含用户输入相关内容
