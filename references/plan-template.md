# 论文研究计划书模板

本文档提供完整的论文研究计划书文档结构模板。

> 阅读导航：生成整份计划时顺序读取；只补局部时检索“环境验证报告”“执行阶段定义”“质量门禁”“工作流调度”或“执行方式”。本文的 Python 检查片段均为逻辑伪代码，除非另有明确说明。

## 文档结构

# [研究主题]执行计划 - AI 工作流指令

## 元数据

```yaml
项目名称: [研究主题]
项目简称: [项目简称]
研究类型: [技术类/实证类/文献综述类/混合类]
工作流: [Workflow 组合，如 Workflow 1 -> Workflow 1.5 -> Workflow 2 -> Workflow 3]
执行模式: [自动化联调/半自动/人工辅助]
预计周期: [天数范围]
创建时间: [ISO格式时间]
核心约束: [关键绑定文件和研究边界]
输入文件: [输入文件列表]
输出目录: [输出根目录]
计划书路径: [计划书文件路径]
```

---

## 环境验证报告

### 路径检测结果

```yaml
aris_environment:
  auto_claude_code_root:
    status: [available/missing]
    path: [实际路径]
  
  skills_install_dir:
    status: [available/missing]
    path: [实际路径]
```

### Skills 可用性

```yaml
skills_status:
  available:
    - name: [skill名称]
      path: [skill路径]
      description: [描述]
  
  missing:
    - name: [skill名称]
      description: [描述]
      required_for: [用于哪个工作流]
      install_hint: [安装建议]
```

### 工具可用性

```yaml
tools_status:
  available:
    - name: [工具名称]
      version: [版本信息]
  
  missing:
    - name: [工具名称]
      required_for: [用途]
      install_hint: [安装建议]
```

---

## 核心约束（最高优先级）

### 强制性约束说明

本研究[不是...，也不是...；它是在...基础上的...研究]。Agent 执行时必须同时满足以下要求：

```yaml
mandatory_constraints:
  research_direction_binding:
    source_file: "[研究方向文件路径]"
    binding_level: "mandatory"
    requirements:
      - "[要求1]"
      - "[要求2]"
      - "..."

  baseline_binding:
    source_file: "[基准研究文件路径]"
    binding_level: "reference_and_extension"
    requirements:
      - "[要求1]"
      - "[要求2]"
      - "..."

  output_directory_restriction:
    root: "[输出根目录]"
    binding_level: "mandatory"
    requirements:
      - "所有研究输出文件必须限制在该主文件夹及其子文件夹内"
      - "严格按照目录结构规范创建子目录"
      - "所有产物必须有明确的文件命名"

  sample_definition:
    target_scope: "[样本范围定义]"
    include_rules:
      - "[纳入规则1]"
      - "[纳入规则2]"
    exclude_rules:
      - "[排除规则1]"
      - "[排除规则2]"

  data_ethics:
    - "仅使用公开可访问或用户已授权的数据来源"
    - "保留来源 URL、访问日期、下载日志"
    - "引用作品内容时遵守合理引用原则"

  tooling_gap:
    problem: "[能力缺口描述]"
    required_handling:
      - "[处理方式1]"
      - "[处理方式2]"
```

### 研究边界与核心主线

```text
主线定位:
本研究以[研究对象]为研究对象，在[基准研究]的基础上[延伸内容]。研究重点不是[不是什么]，而是[是什么]，先[步骤1]，再[步骤2]，最后[步骤3]。

关键链条:
1. [链条1]
2. [链条2]
3. [链条3]
...

凝练表述:
本文在[既有研究]基础上，将[延伸内容]，构建"[关键环节]"的一体化工作流，以[研究目标]。
```

### 强制验证机制

每个阶段结束后必须生成验证记录。任何一个关键验证未通过，不得进入下一阶段。

```text
# 质量检查逻辑伪代码，不可直接执行
def validate_stage(stage_name, outputs, manifest):
    checks = {
        "has_source_trace": outputs.has_source_url_and_access_date(),
        "has_sample_id": outputs.all_records_have_sample_id(),
        "has_stage_log": outputs.has_execution_log(stage_name),
        "matches_manifest": outputs.match_manifest(manifest),
    }
    
    failed = [k for k, v in checks.items() if not v]
    if failed:
        raise RuntimeError(f"{stage_name} validation failed: {failed}")
    
    return True
```

---

## 一、项目配置

### 1.1 研究核心参数

```json
{
  "title": "[研究标题]",
  "short_name": "[项目简称]",
  "research_type": "[研究类型]",
  "research_direction_file": "[研究方向文件]",
  "baseline_paper": "[基准研究文件]",
  "baseline_scope": "[基准研究范围]",
  "extended_scope": "[延伸研究范围]",
  "core_methods": [
    "[方法1]",
    "[方法2]",
    "..."
  ],
  "main_dimensions": [
    "[维度1]",
    "[维度2]",
    "..."
  ]
}
```

### 1.2 输入源文件与工具目录

```yaml
input_files:
  aris_workflow: [aris-workflow.md路径]
  research_direction: [研究方向文件路径]
  baseline_pdf: [基准研究PDF路径]
  framework_file: [技术框架文件路径]
  additional_inputs: [其他输入文件]

required_external_sources:
  - "[外部来源1]"
  - "[外部来源2]"

output_directory: [输出目录路径]
plan_file: [计划书文件路径]
```

### 1.3 输出目录结构

```text
[输出目录]/
├── 00-项目清单/
│   ├── project-manifest.yaml
│   ├── skill-availability.yaml
│   ├── path-validation.yaml
│   └── execution-log.md
├── 01-需求与规划/
├── 02-数据处理/
├── 03-分析结果/
├── 04-论文产出/
├── 05-计划书/
├── 06-质量验证/
└── 07-调度日志/
```

详细目录结构见: [directory-structure.md](directory-structure.md)

### 1.4 ARIS 工作流映射

```yaml
workflow_sequence:
  - workflow: "Workflow 1 - Idea Discovery"
    executor:
      type: skill
      name: idea-discovery
    skill_path: "[skill路径]"
    available: [true/false]
    purpose: "[用途说明]"
    output:
      - "[输出1]"
      - "[输出2]"
  
  - workflow: "Workflow 1.5 - Experiment Bridge"
    executor:
      type: skill
      name: experiment-bridge
    skill_path: "[skill路径]"
    available: [true/false]
    purpose: "[用途说明]"
    output:
      - "[输出1]"
      - "[输出2]"
  
  # ... 其他工作流
```

---

## 二、执行阶段定义

### 阶段 0: 项目初始化与环境盘点

**执行指令**：
```yaml
executor:
  type: direct_agent
  action: initialize_project
inputs:
  - [输入文件1]
  - [输入文件2]

parameters:
  - 创建输出目录结构（按 directory-structure.md 规范）
  - 建立 project-manifest.yaml
  - 检测工具可用性
  - 记录能力缺口
  - 初始化任务队列
  - 生成 skill-availability.yaml
  - 生成 path-validation.yaml

outputs:
  - [输出目录]/00-项目清单/project-manifest.yaml
  - [输出目录]/00-项目清单/skill-availability.yaml
  - [输出目录]/00-项目清单/path-validation.yaml
  - [输出目录]/00-项目清单/execution-log.md
  - [输出目录]/06-质量验证/stage-checkpoints/.checkpoint_stage0
  - [输出目录]/07-调度日志/workflow-schedule.yaml
```

**输出目录创建**：
```yaml
action:
  type: direct_agent
  operation: create_output_directories
  output_root: "[输出目录]"
  research_type: "[研究类型]"
```

**质量检查**：
```text
# 质量检查逻辑伪代码，不可直接执行
def check_stage0(env_report):
    checks = {
        "output_dirs_created": env_report.output_dirs_created,
        "research_direction_exists": env_report.exists("[研究方向文件]"),
        "baseline_exists": env_report.exists("[基准研究文件]"),
        "tool_gap_recorded": env_report.has_gap_record(),
        "scheduler_initialized": env_report.exists("workflow-schedule.yaml"),
        "manifest_created": env_report.exists("project-manifest.yaml"),
        "skill_report_created": env_report.exists("skill-availability.yaml"),
    }
    return all(checks.values())
```

**失败处理**：
- 如果[工具]缺失，记录安装建议，但不强行执行联网安装
- 如果[能力]缺失，标注为关键阻塞项，但可先执行其他阶段
- 如果目录创建失败，报告权限问题并请求用户处理

---

### 阶段 1: [阶段名称]

**执行指令**：
```yaml
executor:
  type: skill
  name: [skill名称]
inputs:
  - [输入文件1]
  - [输入文件2]

constraints:
  - [约束条件1]
  - [约束条件2]

parameters:
  - [参数1]
  - [参数2]

outputs:
  - [输出目录]/[子目录]/[输出文件1]
  - [输出目录]/[子目录]/[输出文件2]
  - [输出目录]/06-质量验证/stage-checkpoints/.checkpoint_stage1
```

**必须抽取的信息**：
```yaml
required_fields:
  - [字段1]
  - [字段2]
```

**质量检查**：
```text
# 质量检查逻辑伪代码，不可直接执行
def check_stage1(outputs):
    checks = {
        "[检查项1]": outputs.[条件],
        "[检查项2]": outputs.[条件],
    }
    return all(checks.values())
```

**失败处理**：
- 如果[条件]，则[处理方式]

---

## 三、工作流调度配置

### 3.1 调度总览

```yaml
execution_mode:
  type: "hybrid"
  global_order: "sequential_with_parallel_subtasks"
  allow_parallel:
    - "[可并行的任务1]"
    - "[可并行的任务2]"
  sequential_dependencies:
    - "阶段A -> 阶段B"
```

### 3.2 任务队列设计

```csv
task_id,stage,priority,depends_on,parallel_group,input,output,status,owner,next_action
T00,init,P0,,global,plan,manifest,pending,agent,create_dirs
T01,[阶段],P0,T00,global,[输入],[输出],pending,agent,[动作]
...
```

详细调度配置见: [workflow-scheduling.md](workflow-scheduling.md)

### 3.3 Checkpoint 文件

```yaml
checkpoints:
  stage0:
    file: "[输出目录]/06-质量验证/stage-checkpoints/.checkpoint_stage0"
    required_data:
      - "project-manifest.yaml"
      - "skill-availability.yaml"
  # ...
```

---

## 四、数据流转配置

### 4.1 数据流 DAG

```text
[输入节点]
  -> [中间节点1]
  -> [中间节点2]
  -> [输出节点]
```

### 4.2 文件命名规范

```yaml
file_naming:
  [类型]: "[命名格式]"
```

详细命名规范见: [directory-structure.md#文件命名规范](directory-structure.md#文件命名规范)

### 4.3 元数据绑定规则

每个样本必须绑定以下最小元数据：

```yaml
minimum_metadata:
  - [元数据1]
  - [元数据2]
```

---

## 五、异常处理配置

### 5.1 异常类型

```yaml
exceptions:
  [异常类型]:
    action:
      - "[处理步骤1]"
      - "[处理步骤2]"
```

### 5.2 失败恢复

```yaml
failure_recovery:
  max_retries: [重试次数]
  retry_delay: [延迟秒数]
  fallback: [降级方案]
```

---

## 六、执行方式

计划主体只保存平台无关 executor 声明。调用前先根据当前平台解析 `executor.name`。

### 6.1 完整工作流

```yaml
executor:
  type: skill
  name: [工作流skill名称]
inputs:
  - [配置文件路径]
parameters:
  mode: full
```

### 6.2 单阶段

```yaml
executor:
  type: skill
  name: [skill名称]
inputs:
  - [输入路径]
outputs:
  - [输出路径]
```

### 6.3 平台调用提示

```text
Codex: Use $[skill名称] with [输入/参数]
Claude Code: /[skill名称] [输入/参数]
其他 Agent Skills 实现: 按 executor.name 查找并调用 skill
状态查询: 读取 workflow-schedule.yaml 和 execution-log.md
```

---

## 附录

### A. 路径检测结果详情

详见: [输出目录]/00-项目清单/path-validation.yaml

### B. Skills 可用性详情

详见: [输出目录]/00-项目清单/skill-availability.yaml

### C. 项目元数据

详见: [输出目录]/00-项目清单/project-manifest.yaml
