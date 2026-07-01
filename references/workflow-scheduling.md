# 工作流调度配置模板

本文档提供计划书中工作流调度配置的详细模板。

## 调度模式

### 1. 执行模式配置

```yaml
execution_mode:
  type: "hybrid"  # sequential | parallel | hybrid
  global_order: "sequential_with_parallel_subtasks"
  
  # 可并行的任务类型
  allow_parallel:
    - "按届次采集数据"
    - "按样本处理数据"
    - "按维度进行分析"
    - "按分组运行统计"
  
  # 必须串行的依赖关系
  sequential_dependencies:
    - "阶段1 研究分析 -> 阶段2 样本核验"
    - "阶段2 样本核验 -> 阶段3 数据采集"
    - "阶段3 数据采集 -> 阶段4 数据处理"
    - "阶段4 数据处理 -> 阶段5 分析建模"
    - "阶段5 分析建模 -> 阶段6 论文写作"
```

### 2. 任务优先级

```yaml
priority_levels:
  P0: "关键路径任务，必须完成"
  P1: "重要任务，影响后续阶段"
  P2: "辅助任务，可延迟完成"
  P3: "优化任务，可选完成"
```

---

## 任务队列设计

### 任务队列模板

```csv
task_id,stage,priority,depends_on,parallel_group,input,output,status,owner,next_action
```

### 字段说明

| 字段 | 说明 | 示例 |
|---|---|---|
| task_id | 任务唯一标识 | T00, T01_E01 |
| stage | 所属阶段 | init, sample_verify, video_download |
| priority | 优先级 | P0, P1, P2, P3 |
| depends_on | 依赖任务 | T00, T01_ALL |
| parallel_group | 并行分组 | global, edition, sample |
| input | 输入 | 文件路径或描述 |
| output | 输出 | 文件路径或描述 |
| status | 状态 | pending, running, completed, failed |
| owner | 执行者 | agent, agent_or_manual |
| next_action | 下一步动作 | 具体动作描述 |

### 完整任务队列示例

```csv
task_id,stage,priority,depends_on,parallel_group,input,output,status,owner,next_action
T00,init,P0,,global,plan,manifest,pending,agent,create_dirs
T01,research_review,P0,T00,global,research_direction+baseline_pdf,research_alignment,pending,agent,extract_alignment
T02_E01,sample_verify,P0,T01,edition,E01_pages,E01_awards_csv,pending,agent,verify_awards
T02_E02,sample_verify,P0,T01,edition,E02_pages,E02_awards_csv,pending,agent,verify_awards
T02_E03,sample_verify,P0,T01,edition,E03_pages,E03_awards_csv,pending,agent,verify_awards
T02_E04,sample_verify,P0,T01,edition,E04_pages,E04_awards_csv,pending,agent,verify_awards
T02_E05,sample_verify,P0,T01,edition,E05_pages,E05_awards_csv,pending,agent,verify_awards
T02_E06,sample_verify,P0,T01,edition,E06_pages,E06_awards_csv,pending,agent,verify_awards
T03,data_collection,P1,T02_ALL,sample,url_list,data_files,pending,agent_or_manual,collect_data
T04,data_processing,P1,T03,sample,raw_data,cleaned_data,pending,agent,process_data
T05,analysis,P1,T04,group,cleaned_data,analysis_results,pending,agent,run_analysis
T06,writing,P2,T05,global,all_outputs,paper_draft,pending,agent,write_draft
T07,review,P0,T06,global,paper_draft,final_report,pending,agent,review_and_revise
```

---

## 调度伪代码

### 基础调度函数

```python
def run_research_workflow():
    """研究工作流调度主函数"""
    # 阶段0: 初始化
    run("T00")
    gate("init_gate")
    
    # 阶段1: 研究分析
    run("T01")
    gate("research_review_gate")
    
    # 阶段2: 样本核验（可并行）
    parallel_run(["T02_E01", "T02_E02", "T02_E03", "T02_E04", "T02_E05", "T02_E06"])
    gate("sample_gate")
    
    # 阶段3: 数据采集（可并行，带重试）
    parallel_run_by_sample("T03", max_workers=3, retry=2)
    gate("data_collection_gate", allow_partial=True)
    
    # 阶段4: 数据处理（可并行）
    parallel_run_by_sample("T04", max_workers=2, retry=1)
    gate("data_processing_gate")
    
    # 阶段5: 分析建模（可并行）
    parallel_run(["T05_analysis_1", "T05_analysis_2", "T05_analysis_3"])
    gate("analysis_gate")
    
    # 阶段6: 论文写作
    run("T06")
    gate("writing_gate")
    
    # 阶段7: 审核修订（循环直到通过）
    run_loop("T07", until="all_quality_gates_pass", max_iterations=3)
```

### 调度辅助函数

```python
def run(task_id):
    """执行单个任务"""
    task = get_task(task_id)
    update_status(task_id, "running")
    try:
        result = execute_task(task)
        update_status(task_id, "completed")
        return result
    except Exception as e:
        update_status(task_id, "failed")
        handle_failure(task_id, e)

def parallel_run(task_ids, max_workers=None):
    """并行执行多个任务"""
    from concurrent.futures import ThreadPoolExecutor
    
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = {executor.submit(run, tid): tid for tid in task_ids}
        results = []
        for future in as_completed(futures):
            results.append(future.result())
    return results

def parallel_run_by_group(task_pattern, group_values, max_workers=None):
    """按分组并行执行任务"""
    task_ids = [f"{task_pattern}_{group}" for group in group_values]
    return parallel_run(task_ids, max_workers)

def run_loop(task_id, until, max_iterations=5):
    """循环执行任务直到条件满足"""
    for iteration in range(max_iterations):
        run(task_id)
        if check_condition(until):
            return
    raise RuntimeError(f"Max iterations reached without meeting condition: {until}")

def gate(gate_name, allow_partial=False):
    """质量门禁检查"""
    validation_result = validate_gate(gate_name)
    if not validation_result["passed"]:
        if allow_partial and validation_result["pass_rate"] > 0.8:
            log_warning(f"Gate {gate_name} partially passed: {validation_result}")
            return
        raise RuntimeError(f"Gate {gate_name} failed: {validation_result}")
```

---

## 检查点配置

### 检查点定义

```yaml
checkpoints:
  stage0:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_stage0"
    required_data:
      - "project-manifest.yaml"
      - "workflow-schedule.yaml"
    description: "初始化完成检查点"
  
  stage1:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_stage1"
    required_data:
      - "research-direction-alignment.md"
      - "baseline-summary.md"
      - "baseline-sample-table.csv"
    description: "研究分析完成检查点"
  
  stage2:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_stage2"
    required_data:
      - "verified-samples.csv"
    description: "样本核验完成检查点"
  
  stage3:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_stage3"
    required_data:
      - "data-collection-report.md"
    description: "数据采集完成检查点"
  
  stage4:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_stage4"
    required_data:
      - "cleaned_data/"
      - "qc-report.md"
    description: "数据处理完成检查点"
  
  final:
    file: "[输出目录]/08-质量验证/stage-checkpoints/.checkpoint_final"
    required_data:
      - "final-acceptance-report.md"
      - "paper-final.pdf"
    description: "最终完成检查点"
```

### 检查点操作

```python
def create_checkpoint(checkpoint_config, stage_outputs):
    """创建检查点"""
    import json
    from datetime import datetime
    
    checkpoint_data = {
        "timestamp": datetime.now().isoformat(),
        "stage": checkpoint_config["stage"],
        "files": {},
        "validation": "passed"
    }
    
    # 验证必需文件
    for file_pattern in checkpoint_config["required_data"]:
        files = glob.glob(file_pattern)
        if not files:
            checkpoint_data["validation"] = "failed"
            checkpoint_data["missing"].append(file_pattern)
        else:
            checkpoint_data["files"][file_pattern] = files
    
    # 写入检查点文件
    with open(checkpoint_config["file"], "w") as f:
        json.dump(checkpoint_data, f, indent=2)
    
    return checkpoint_data["validation"] == "passed"

def restore_from_checkpoint(checkpoint_file):
    """从检查点恢复"""
    import json
    
    with open(checkpoint_file, "r") as f:
        checkpoint = json.load(f)
    
    return {
        "stage": checkpoint["stage"],
        "files": checkpoint["files"],
        "timestamp": checkpoint["timestamp"]
    }
```

---

## 进度监控

### 监控指标

```yaml
progress_monitor:
  enabled: true
  log_file: "[输出目录]/09-调度日志/progress.log"
  update_frequency: "每完成一个样本或一个阶段"
  
  metrics:
    # 样本相关
    - total_samples_verified
    - included_samples
    - data_collection_success_count
    - data_processing_success_count
    
    # 分析相关
    - analysis_completed_dimensions
    - figures_completed
    
    # 质量相关
    - quality_gate_pass_rate
    - issues_resolved_count
    
    # 时间相关
    - stage_duration
    - estimated_remaining_time
```

### 进度报告

```python
def generate_progress_report(metrics, output_file):
    """生成进度报告"""
    from datetime import datetime
    
    report = {
        "timestamp": datetime.now().isoformat(),
        "overall_progress": calculate_overall_progress(metrics),
        "stage_progress": {},
        "issues": []
    }
    
    # 计算各阶段进度
    for stage, stage_metrics in metrics.items():
        report["stage_progress"][stage] = {
            "completed": stage_metrics["completed"],
            "total": stage_metrics["total"],
            "percentage": stage_metrics["completed"] / stage_metrics["total"] * 100
        }
    
    # 写入报告
    with open(output_file, "w") as f:
        json.dump(report, f, indent=2)
    
    return report
```

---

## 数据流转配置

### 数据流 DAG

```yaml
data_flow:
  # 主数据流
  main_flow:
    - node: "research_direction"
      type: "input"
      outputs: ["research_alignment"]
    
    - node: "baseline_paper"
      type: "input"
      outputs: ["baseline_summary", "baseline_sample_table"]
    
    - node: "official_sources"
      type: "input"
      outputs: ["verified_samples"]
    
    - node: "verified_samples"
      type: "intermediate"
      inputs: ["research_alignment", "baseline_sample_table"]
      outputs: ["data_collection_tasks"]
    
    - node: "data_collection"
      type: "intermediate"
      inputs: ["data_collection_tasks"]
      outputs: ["raw_data"]
    
    - node: "data_processing"
      type: "intermediate"
      inputs: ["raw_data"]
      outputs: ["cleaned_data"]
    
    - node: "analysis"
      type: "intermediate"
      inputs: ["cleaned_data"]
      outputs: ["analysis_results", "figures"]
    
    - node: "paper_writing"
      type: "output"
      inputs: ["analysis_results", "figures", "research_alignment"]
      outputs: ["paper_draft"]
```

### 文件命名规范

```yaml
file_naming:
  # 样本相关文件
  sample_data: "{sample_id}_{short_title}_data.{ext}"
  sample_text: "{sample_id}_{short_title}_cleaned.txt"
  sample_video: "{sample_id}_{platform_id}_{short_title}.{ext}"
  
  # 分析结果文件
  analysis_result: "{analysis_type}_{group_id}_result.{ext}"
  figure: "fig_{number}_{short_name}.{ext}"
  table: "table_{number}_{short_name}.{ext}"
  
  # 报告文件
  report: "{stage}_{report_type}_report.md"
  log: "{stage}_execution.log"
```

---

## 异常处理配置

### 异常类型定义

```yaml
exception_types:
  # 数据异常
  data_not_found:
    severity: "high"
    action: "retry_with_alternative_source"
    max_retries: 3
  
  data_quality_issue:
    severity: "medium"
    action: "log_and_continue"
    requires_manual_review: true
  
  # 执行异常
  task_timeout:
    severity: "medium"
    action: "kill_and_restart"
    max_retries: 2
  
  dependency_failed:
    severity: "critical"
    action: "block_downstream"
  
  # 资源异常
  disk_full:
    severity: "critical"
    action: "stop_and_notify"
  
  network_error:
    severity: "medium"
    action: "retry_with_backoff"
    max_retries: 3
    backoff_seconds: 5
```

### 失败恢复策略

```yaml
failure_recovery:
  # 单任务失败
  task_failure:
    strategy: "retry"
    max_attempts: 3
    backoff: "exponential"
    
    escalation:
      - condition: "attempts >= max_attempts"
        action: "mark_as_failed"
      - condition: "priority == P0"
        action: "notify_user"
  
  # 阶段失败
  stage_failure:
    strategy: "checkpoint_rollback"
    rollback_to: "last_successful_checkpoint"
    
    escalation:
      - condition: "critical_stage"
        action: "stop_workflow"
      - condition: "non_critical_stage"
        action: "skip_and_continue"
  
  # 工作流失败
  workflow_failure:
    strategy: "save_state_and_exit"
    save_state_to: "workflow-state.json"
    
    recovery:
      - "用户可从保存的状态恢复"
      - "提供详细的失败诊断报告"
```

### 重试配置

```python
def execute_with_retry(task, max_retries=3, backoff="exponential"):
    """带重试的任务执行"""
    import time
    
    for attempt in range(max_retries):
        try:
            return task.execute()
        except Exception as e:
            if attempt == max_retries - 1:
                raise
            
            # 计算退避时间
            if backoff == "exponential":
                wait_time = 2 ** attempt
            elif backoff == "linear":
                wait_time = attempt + 1
            else:
                wait_time = 1
            
            time.sleep(wait_time)
            log_retry(task.id, attempt + 1, e)
```

---

## 调度日志

### 日志格式

```yaml
logging:
  level: "INFO"  # DEBUG, INFO, WARNING, ERROR
  file: "[输出目录]/09-调度日志/workflow.log"
  format: "{timestamp} [{level}] {stage}/{task}: {message}"
  
  fields:
    - timestamp
    - level
    - stage
    - task_id
    - message
    - duration
    - status
```

### 日志记录函数

```python
def log_task_event(task_id, event, details=None):
    """记录任务事件"""
    from datetime import datetime
    
    entry = {
        "timestamp": datetime.now().isoformat(),
        "task_id": task_id,
        "event": event,  # started, completed, failed, retried
        "details": details or {}
    }
    
    with open(LOG_FILE, "a") as f:
        f.write(json.dumps(entry) + "\n")
```
