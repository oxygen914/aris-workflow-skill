# 输出目录结构规范

本文档定义了 ARIS 工作流输出目录的标准化结构。

> 阅读导航：先按研究类型选择目录；创建前检查“目录创建原则”；生成后使用“目录验证”检查必需路径和命名。

## 目录创建原则

| 原则 | 说明 |
|---|---|
| 根目录指定 | 用户必须在计划书生成前指定输出根目录 |
| 子目录自动创建 | 按照规范自动创建标准化的子目录结构 |
| 分类清晰 | 不同类型的产物存放在不同的子目录 |
| 可追溯 | 每个目录都有明确的用途说明 |
| 可扩展 | 支持根据研究类型添加额外的子目录 |

## 标准目录结构

### 完整结构

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
│   ├── research-direction.md      # 研究方向文档
│   ├── baseline-summary.md        # 基准研究摘要
│   ├── constraints.yaml           # 约束条件
│   └── workflow-mapping.md        # 工作流映射
│
├── 02-数据处理/
│   ├── raw/                       # 原始数据
│   ├── cleaned/                   # 清洗后数据
│   ├── processed/                 # 处理后数据
│   └── metadata/                  # 元数据
│
├── 03-分析结果/
│   ├── figures/                   # 图表文件
│   ├── tables/                    # 表格文件
│   ├── statistics/                # 统计结果
│   └── analysis-reports/          # 分析报告
│
├── 04-论文产出/
│   ├── paper-plan.md              # 论文规划
│   ├── chapter-outline.md         # 章节大纲
│   ├── draft/                     # 论文草稿
│   ├── final/                     # 最终版本
│   └── references/                # 参考文献
│
├── 05-计划书/
│   ├── 计划书-[项目简称].md        # 主计划书
│   ├── plan-changelog.md          # 计划书变更记录
│   └── plan-templates/            # 使用的模板
│
├── 06-质量验证/
│   ├── stage-checkpoints/         # 阶段检查点
│   ├── quality-reports/           # 质量报告
│   └── validation-logs/           # 验证日志
│
└── 07-调度日志/
    ├── workflow-schedule.yaml     # 调度配置
    ├── task-queue.csv             # 任务队列
    └── progress.log               # 进度日志
```

### 最小结构（无数据采集的研究）

```text
[输出根目录]/
├── 00-项目清单/
│   ├── project-manifest.yaml
│   ├── skill-availability.yaml
│   └── execution-log.md
│
├── 01-需求与规划/
│   ├── research-requirements.md
│   └── constraints.yaml
│
├── 04-论文产出/
│   ├── paper-plan.md
│   └── draft/
│
├── 05-计划书/
│   └── 计划书-[项目简称].md
│
└── 06-质量验证/
    └── stage-checkpoints/
```

## 目录用途说明

### 00-项目清单/

**用途**: 存储项目元数据、来源追踪和验证报告

**核心文件**:

| 文件 | 用途 | 必需性 |
|---|---|---|
| project-manifest.yaml | 项目元数据（名称、类型、时间、路径） | 必需 |
| source-url-registry.csv | 数据来源URL注册表 | 按需 |
| sample-registry.csv | 样本注册表 | 按需 |
| skill-availability.yaml | Skill可用性检测报告 | 必需 |
| path-validation.yaml | 路径验证报告 | 必需 |
| execution-log.md | 执行日志 | 必需 |

**project-manifest.yaml 示例**:

```yaml
project:
  name: "基于观察与评估量表的留学生语法类微课研究"
  short_name: "cao"
  type: "empirical"  # technical, empirical, literature_review, mixed
  created_at: "2024-06-30T10:00:00"
  updated_at: "2024-06-30T10:00:00"

input_files:
  research_direction:
    raw: "<research-root>/研究方向.md"
    resolved: "<absolute-path-to>/研究方向.md"
  baseline_paper:
    raw: "<research-root>/baseline.pdf"
    resolved: "<absolute-path-to>/baseline.pdf"

output_directory:
  raw: "<output-root>/workflow-output-cao"
  resolved: "<absolute-path-to>/workflow-output-cao"

workflow:
  type: "Workflow 1 -> Workflow 1.5 -> Workflow 2 -> Workflow 3"
  estimated_duration_days: 21

constraints:
  - "研究方向文档绑定"
  - "基准研究绑定"
  - "输出目录限制"
```

> 注：`raw` 保留用户原始输入格式（可以是相对路径、`~` 路径或占位符），`resolved` 为解析后的绝对路径。

### 01-需求与规划/

**用途**: 存储研究需求、方向文档、基准研究摘要和约束条件

**核心文件**:

| 文件 | 用途 | 必需性 |
|---|---|---|
| research-requirements.md | 研究需求文档 | 必需 |
| research-direction.md | 研究方向文档（从输入复制或生成） | 按需 |
| baseline-summary.md | 基准研究摘要 | 按需 |
| constraints.yaml | 约束条件配置 | 必需 |
| workflow-mapping.md | 工作流映射 | 必需 |

### 02-数据处理/

**用途**: 存储数据采集、清洗和处理过程中的产物

**子目录**:

| 目录 | 用途 | 说明 |
|---|---|---|
| raw/ | 原始数据 | 未经处理的原始数据文件 |
| cleaned/ | 清洗后数据 | 经过去重、格式化等清洗操作的数据 |
| processed/ | 处理后数据 | 经过分析处理后的数据 |
| metadata/ | 元数据 | 数据描述、字段说明等 |

**适用场景**:
- 需要采集外部数据的研究
- 需要处理文本、视频、音频等数据的研究
- 需要进行词频统计、内容分析的研究

### 03-分析结果/

**用途**: 存储分析过程中生成的图表、统计结果和报告

**子目录**:

| 目录 | 用途 | 说明 |
|---|---|---|
| figures/ | 图表文件 | PNG、PDF、SVG 等格式的图表 |
| tables/ | 表格文件 | CSV、Excel 等格式的表格 |
| statistics/ | 统计结果 | 统计分析结果文件 |
| analysis-reports/ | 分析报告 | Markdown 格式的分析报告 |

### 04-论文产出/

**用途**: 存储论文写作过程中的产物

**核心文件和目录**:

| 文件/目录 | 用途 | 必需性 |
|---|---|---|
| paper-plan.md | 论文规划文档 | 必需 |
| chapter-outline.md | 章节大纲 | 必需 |
| draft/ | 论文草稿 | 必需 |
| final/ | 最终版本 | 必需 |
| references/ | 参考文献 | 必需 |

### 05-计划书/

**用途**: 存储计划书文档和变更记录

**核心文件**:

| 文件 | 用途 | 必需性 |
|---|---|---|
| 计划书-[项目简称].md | 主计划书文档 | 必需 |
| plan-changelog.md | 计划书变更记录 | 推荐 |
| plan-templates/ | 使用的模板 | 可选 |

### 06-质量验证/

**用途**: 存储质量验证和检查点文件

**子目录**:

| 目录 | 用途 | 说明 |
|---|---|---|
| stage-checkpoints/ | 阶段检查点 | 每个阶段完成后的检查点文件 |
| quality-reports/ | 质量报告 | 各阶段的质量验证报告 |
| validation-logs/ | 验证日志 | 验证过程的详细日志 |

### 07-调度日志/

**用途**: 存储工作流调度和任务队列信息

**核心文件**:

| 文件 | 用途 | 必需性 |
|---|---|---|
| workflow-schedule.yaml | 工作流调度配置 | 必需 |
| task-queue.csv | 任务队列 | 必需 |
| progress.log | 进度日志 | 必需 |

## 按研究类型的目录配置

### 技术方法类研究

```yaml
required_directories:
  - "00-项目清单"
  - "01-需求与规划"
  - "04-论文产出"
  - "05-计划书"
  - "06-质量验证"
  - "07-调度日志"

optional_directories:
  - "02-数据处理"  # 如有实验数据
  - "03-分析结果"  # 如有性能测试结果
```

### 实证研究类研究

```yaml
required_directories:
  - "00-项目清单"
  - "01-需求与规划"
  - "02-数据处理"
  - "03-分析结果"
  - "04-论文产出"
  - "05-计划书"
  - "06-质量验证"
  - "07-调度日志"
```

### 文献综述类研究

```yaml
required_directories:
  - "00-项目清单"
  - "01-需求与规划"
  - "03-分析结果"  # 文献统计图表
  - "04-论文产出"
  - "05-计划书"
  - "06-质量验证"
  - "07-调度日志"

optional_directories:
  - "02-数据处理"  # 如有文献元数据处理
```

## 目录创建脚本

```bash
#!/bin/bash

create_output_directories() {
    local OUTPUT_ROOT="$1"
    local RESEARCH_TYPE="$2"  # technical, empirical, literature_review
    
    # 核心目录（所有研究类型）
    mkdir -p "$OUTPUT_ROOT/00-项目清单"
    mkdir -p "$OUTPUT_ROOT/01-需求与规划"
    mkdir -p "$OUTPUT_ROOT/04-论文产出/draft"
    mkdir -p "$OUTPUT_ROOT/04-论文产出/final"
    mkdir -p "$OUTPUT_ROOT/04-论文产出/references"
    mkdir -p "$OUTPUT_ROOT/05-计划书"
    mkdir -p "$OUTPUT_ROOT/06-质量验证/stage-checkpoints"
    mkdir -p "$OUTPUT_ROOT/06-质量验证/quality-reports"
    mkdir -p "$OUTPUT_ROOT/06-质量验证/validation-logs"
    mkdir -p "$OUTPUT_ROOT/07-调度日志"
    
    # 按研究类型添加目录
    case "$RESEARCH_TYPE" in
        empirical)
            mkdir -p "$OUTPUT_ROOT/02-数据处理/raw"
            mkdir -p "$OUTPUT_ROOT/02-数据处理/cleaned"
            mkdir -p "$OUTPUT_ROOT/02-数据处理/processed"
            mkdir -p "$OUTPUT_ROOT/02-数据处理/metadata"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/figures"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/tables"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/statistics"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/analysis-reports"
            ;;
        technical)
            mkdir -p "$OUTPUT_ROOT/03-分析结果/figures"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/tables"
            ;;
        literature_review)
            mkdir -p "$OUTPUT_ROOT/03-分析结果/figures"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/tables"
            mkdir -p "$OUTPUT_ROOT/03-分析结果/statistics"
            ;;
    esac
    
    echo "目录结构创建完成: $OUTPUT_ROOT"
}

# 使用示例
# create_output_directories "<output-root>/工作流输出-cao" "empirical"
```

## 文件命名规范

### 通用命名规范

```yaml
naming_rules:
  # 使用日期时间前缀
  dated_files: "YYYYMMDD_HHMMSS_[描述].[ext]"
  
  # 使用阶段前缀
  stage_files: "stage[N]_[描述].[ext]"
  
  # 使用样本ID
  sample_files: "{sample_id}_[描述].[ext]"
  
  # 使用版本号
  versioned_files: "[文件名]_v{version}.[ext]"
```

### 具体命名示例

```yaml
examples:
  # 项目清单文件
  manifest: "project-manifest.yaml"
  
  # 阶段产物
  stage_output: "stage1_baseline_summary.md"
  stage_report: "stage2_sample_verification_report.md"
  
  # 数据文件
  sample_data: "E01_A2_S01_transcript_cleaned.txt"
  frequency_table: "overall_wordfreq_20240630.csv"
  
  # 图表文件
  figure: "fig_01_architecture.pdf"
  table: "table_02_sample_distribution.csv"
  
  # 检查点文件
  checkpoint: ".checkpoint_stage1"
  
  # 日志文件
  log: "execution_20240630.log"
```
