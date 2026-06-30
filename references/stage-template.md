# 执行阶段定义模板

本文档提供计划书中执行阶段定义的详细模板。

## 阶段定义结构

每个执行阶段必须包含以下部分：

### 基本信息

```markdown
### 阶段 [编号]: [阶段名称] `/[skill命令]`

**执行指令**：
```text
CALL /[skill名称]
TASK: [任务标识]  # 可选，用于多任务阶段
INPUT:
  - [输入文件1]
  - [输入文件2]

CONSTRAINT:  # 可选
  - [约束条件1]
  - [约束条件2]

PARAMETERS:
  - [参数1]: [说明]
  - [参数2]: [说明]

OUTPUT:
  - [输出文件1路径]
  - [输出文件2路径]
```
```

### 工具/方法说明（可选）

```yaml
strategy:
  priority_1_[方法名称]:
    description: "[描述]"
    candidate_tool: "[工具名称]"
    prerequisite:
      - "[前置条件1]"
      - "[前置条件2]"
  
  priority_2_[方法名称]:
    description: "[描述]"
    action: "[动作说明]"
```

### 必须抽取/生成的信息

```yaml
required_outputs:
  - [输出项1]
  - [输出项2]

required_fields:  # 用于数据提取类阶段
  - [字段1]
  - [字段2]
```

### 建议命令模板（用于需要执行命令的阶段）

```bash
# [命令说明]
[命令1]
[命令2]
```

### 输出格式要求（用于需要特定格式的输出）

```yaml
output_format:
  [文件类型]:
    required_columns:
      - [列1]
      - [列2]
    optional_columns:
      - [列3]
```

### 质量检查

```python
def check_stage[编号](outputs, manifest=None):
    """
    [检查说明]
    """
    checks = {
        "[检查项1]": outputs.[条件1],
        "[检查项2]": outputs.[条件2],
        "[检查项3]": outputs.[条件3],
        # ...
    }
    
    # 可选：添加依赖检查
    if manifest:
        checks["matches_manifest"] = outputs.match_manifest(manifest)
    
    return all(checks.values())
```

### 失败处理

```markdown
**失败处理**：

- 如果[条件1]，则[处理方式1]
- 如果[条件2]，则[处理方式2]
- 如果[条件3]，则[处理方式3]
```

---

## 常见阶段类型

### 类型1：初始化阶段

```markdown
### 阶段 0: 项目初始化与环境盘点 `/research-pipeline init`

**执行指令**：
```text
CALL /research-pipeline init
INPUT:
  - [aris-workflow.md路径]
  - [计划书路径]
  - [研究方向文件路径]
  - [基准研究文件路径]

PARAMETERS:
  - 创建输出目录结构
  - 建立 project-manifest.yaml
  - 检测工具可用性
  - 记录能力缺口
  - 初始化任务队列

OUTPUT:
  - [输出目录]/00-项目清单/project-manifest.yaml
  - [输出目录]/00-项目清单/execution-log.md
  - [输出目录]/08-质量验证/risk-register.md
  - [输出目录]/09-调度日志/workflow-schedule.yaml
```

**工具检测建议**：
```bash
[工具1] --version
[工具2] --version
python3 -c "import [模块]; print('ok')"
```

**质量检查**：
```python
def check_stage0(env_report):
    checks = {
        "output_dirs_created": env_report.output_dirs_created,
        "research_direction_exists": env_report.exists("[研究方向文件]"),
        "baseline_exists": env_report.exists("[基准研究文件]"),
        "tool_gap_recorded": env_report.has_gap_record(),
        "scheduler_initialized": env_report.exists("workflow-schedule.yaml"),
    }
    return all(checks.values())
```

**失败处理**：
- 如果[工具]缺失，记录安装建议，但不强行执行联网安装
- 如果[能力]缺失，标注为关键阻塞项，但可先执行其他阶段
```

---

### 类型2：研究分析与复核阶段

```markdown
### 阶段 1: [研究方向与基准研究复核] `/idea-discovery`

**执行指令**：
```text
CALL /idea-discovery
INPUT:
  - [研究方向文件路径]
  - [基准研究文件路径]

PARAMETERS:
  - 提取研究方向文档中的研究对象、研究方法、核心分析维度
  - 提取基准研究的研究问题、理论基础、样本表、结论与不足
  - 建立 baseline-sample-table.csv
  - 标记可继承维度与需要调整的维度

OUTPUT:
  - [输出目录]/01-基准论文复核/research-direction-alignment.md
  - [输出目录]/01-基准论文复核/baseline-summary.md
  - [输出目录]/01-基准论文复核/baseline-sample-table.csv
  - [输出目录]/01-基准论文复核/baseline-method-notes.md
  - [输出目录]/01-基准论文复核/extension-gap-analysis.md
```

**必须抽取的基准信息**：
```yaml
baseline_required_fields:
  - sample_id
  - [字段1]
  - [字段2]

research_direction_required_outputs:
  - research_object: "[研究对象]"
  - method_core: "[核心方法]"
  - core_dimensions: [[维度1], [维度2]]
```

**质量检查**：
```python
def check_stage1(baseline_table):
    checks = {
        "baseline_count_correct": len(baseline_table) == [预期数量],
        "research_direction_aligned": baseline_table.meta.has_file("research-direction-alignment.md"),
        "extension_questions_defined": baseline_table.meta.has_extension_questions,
    }
    return all(checks.values())
```

**失败处理**：
- 如果PDF抽取不完整，优先人工查看目录和关键章节
- 如果样本表无法自动提取，手动录入
- 如果原研究与官方复核结果冲突，保留冲突记录
```

---

### 类型3：数据采集阶段

```markdown
### 阶段 2: [样本核验与数据采集] `/idea-discovery sample-scope`

**执行指令**：
```text
CALL /idea-discovery
TASK: [具体任务标识]
INPUT:
  - baseline-sample-table.csv
  - [官方来源页面]

PARAMETERS:
  - 按[维度]建立官方来源清单
  - 抽取所有符合条件的样本
  - 记录关键元数据
  - 建立最终样本纳入表和排除表

OUTPUT:
  - [输出目录]/02-[名称]/[输出文件1].csv
  - [输出目录]/02-[名称]/[输出文件2].csv
  - [输出目录]/02-[名称]/missing-or-excluded-samples.md
```

**样本编号规则**：
```yaml
sample_id_format: "[格式说明]"
examples:
  - "[示例1]"
  - "[示例2]"
```

**最终样本表字段**：
```csv
sample_id,[字段1],[字段2],...,inclusion_decision,notes
```

**质量检查**：
```python
def check_stage2(sample_registry):
    required_cols = ["sample_id", "[字段1]", "source_url", "inclusion_decision"]
    checks = {
        "all_required_cols": all(c in sample_registry.columns for c in required_cols),
        "no_unverified_included": sample_registry.query("inclusion_decision == 'include'")["source_url"].notna().all(),
        "excluded_samples_explained": sample_registry.meta.has_file("missing-or-excluded-samples.md"),
    }
    return all(checks.values())
```

**失败处理**：
- 如果官方页面不可访问，记录访问失败时间和替代来源
- 如果信息不完整，单独列为"待补充样本"
```

---

### 类型4：数据处理阶段

```markdown
### 阶段 3: [数据处理与转换] `/experiment-bridge [task]`

**执行指令**：
```text
CALL /experiment-bridge
TASK: [任务标识]
INPUT:
  - [输入目录或文件]

CONSTRAINT:
  - [约束条件1]
  - [约束条件2]

OUTPUT:
  - [输出目录]/raw/
  - [输出目录]/cleaned/
  - [输出目录]/[报告文件].md
```

**处理方案优先级**：
```yaml
strategy:
  priority_1_[方法]:
    input: "[输入类型]"
    action: "[动作说明]"
    advantage: "[优势]"

  priority_2_[方法]:
    input: "[输入类型]"
    candidate_tools:
      - "[工具1]"
      - "[工具2]"
```

**建议命令模板**：
```bash
# [命令说明]
[命令]
```

**质量检查**：
```python
def check_stage3(outputs):
    checks = {
        "every_item_has_id": outputs["id"].notna().all(),
        "cleaned_exists": outputs["cleaned_path"].notna().all(),
        "status_valid": outputs["status"].isin(["success", "failed", "needs_review"]).all(),
    }
    return all(checks.values())
```
```

---

### 类型5：分析与建模阶段

```markdown
### 阶段 4: [分析与建模] `/experiment-bridge [task]`

**执行指令**：
```text
CALL /experiment-bridge
TASK: [任务标识]
INPUT:
  - [输入数据]

PARAMETERS:
  - [参数1说明]
  - [参数2说明]

OUTPUT:
  - [输出文件1]
  - [输出文件2]
  - [报告文件]
```

**分析配置**：
```yaml
analysis_config:
  dimensions:
    - [维度1]
    - [维度2]
  methods:
    - [方法1]
    - [方法2]
  output_format:
    - [格式1]
    - [格式2]
```

**质量检查**：
```python
def check_stage4(analysis_results):
    checks = {
        "all_dimensions_covered": all(d in analysis_results for d in ["[维度1]", "[维度2]"]),
        "results_complete": analysis_results["status"] == "complete",
        "figures_generated": analysis_results.has_figures(),
    }
    return all(checks.values())
```
```

---

### 类型6：论文写作阶段

```markdown
### 阶段 5: 论文规划与正文写作 `/paper-writing`

**执行指令**：
```text
CALL /paper-writing
INPUT:
  - [所有输入文件]

PARAMETERS:
  - 先生成 paper-plan.md 和 chapter-outline.md
  - 再撰写 paper-draft.md
  - 写作主线必须[说明]
  - 必须区分"基准研究已有结论"与"本研究延伸发现"

OUTPUT:
  - [输出目录]/paper-plan.md
  - [输出目录]/chapter-outline.md
  - [输出目录]/paper-draft.md
  - [输出目录]/references.md
```

**建议章节结构**：
```yaml
chapters:
  - chapter: 1
    title: "[章节标题]"
    sections:
      - "[小节1]"
      - "[小节2]"
  # ...
```

**质量检查**：
```python
def check_stage5(draft):
    checks = {
        "scope_statement_precise": draft.contains("[关键表述]"),
        "baseline_distinguished": draft.distinguishes_baseline_and_extension,
        "methods_covered": draft.has_methods_section(),
        "limitations_present": draft.has_limitations_section,
    }
    return all(checks.values())
```
```

---

## 阶段依赖设计原则

1. **串行依赖**：后一阶段必须等待前一阶段完成
   - 研究分析 -> 样本核验 -> 数据采集 -> 数据处理 -> 分析建模 -> 论文写作

2. **并行执行**：多个任务可以同时进行
   - 按届次/分组采集数据
   - 按样本处理数据
   - 按维度进行分析

3. **条件依赖**：根据上一阶段结果决定下一步
   - 如果数据采集成功 -> 进入数据处理
   - 如果数据采集失败 -> 进入人工获取流程

4. **可选阶段**：根据研究需要选择执行
   - 量表设计阶段（仅评估类研究）
   - 实验验证阶段（仅实验类研究）
