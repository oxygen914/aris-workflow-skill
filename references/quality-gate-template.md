# 质量门禁设计模板

本文档提供计划书中质量门禁和验证机制的设计模板。

## 质量门禁类型

### 1. 阶段质量门禁

在每个阶段完成后进行验证，确保输出质量：

```yaml
quality_gates:
  - stage: 1
    name: "[阶段名称]质量门禁"
    checks:
      - metric: "[检查指标]"
        threshold: [阈值]
        description: "[描述]"
      - metric: "[检查指标]"
        threshold: [阈值]
        description: "[描述]"
    action_on_fail: "block"  # block | continue_improvement | warn
```

### 2. 数据质量门禁

对数据完整性和正确性进行验证：

```yaml
data_quality_gates:
  sample_gate:
    rule: "[规则描述]"
    check: "正文样本数量必须等于样本清单中纳入数量"
    action_on_fail: "block"
    
  source_gate:
    rule: "每个纳入样本必须有 source_url 和 access_date"
    action_on_fail: "block"
    
  transcript_gate:
    rule: "进入分析的样本必须有清洗后的文本和质量状态"
    action_on_fail: "block"
```

### 3. 方法质量门禁

对研究方法的正确性进行验证：

```yaml
method_quality_gates:
  scale_gate:
    rule: "观察量表和评估量表必须明确指标来源"
    check:
      - indicator_source_matrix存在
      - observation_scale存在
      - evaluation_scale存在
    action_on_fail: "block"
    
  analysis_gate:
    rule: "分析必须覆盖所有核心维度"
    check:
      - 所有维度都有分析结果
      - 所有图表都有数据来源
    action_on_fail: "fix_before_write"
```

### 4. 输出质量门禁

对最终输出进行验证：

```yaml
output_quality_gates:
  figure_gate:
    rule: "每个图表必须有数据来源、输出文件和图注"
    action_on_fail: "fix_before_write"
    
  interpretation_gate:
    rule: "分析结果只能支持合理推断，不得过度解释"
    action_on_fail: "rewrite"
    
  reference_gate:
    rule: "所有引用必须有完整来源信息"
    action_on_fail: "block"
```

---

## 验证函数模板

### 基础验证函数

```python
def validate_stage(stage_name, outputs, manifest=None):
    """
    阶段验证基础函数
    
    Args:
        stage_name: 阶段名称
        outputs: 阶段输出对象
        manifest: 项目清单（可选）
    
    Returns:
        bool: 验证是否通过
    
    Raises:
        RuntimeError: 验证失败时抛出异常，包含失败项列表
    """
    checks = {
        "has_source_trace": outputs.has_source_url_and_access_date(),
        "has_sample_id": outputs.all_records_have_sample_id(),
        "has_stage_log": outputs.has_execution_log(stage_name),
        "has_error_list": outputs.has_error_or_missing_list(),
    }
    
    if manifest:
        checks["matches_manifest"] = outputs.match_manifest(manifest)
    
    failed = [k for k, v in checks.items() if not v]
    if failed:
        raise RuntimeError(f"{stage_name} validation failed: {failed}")
    
    return True
```

### 数据完整性验证

```python
def validate_data_integrity(data_registry, sample_registry):
    """
    数据完整性验证
    
    Args:
        data_registry: 数据注册表
        sample_registry: 样本注册表
    
    Returns:
        dict: 验证结果
    """
    checks = {
        "all_samples_have_ids": data_registry["sample_id"].notna().all(),
        "all_samples_have_sources": data_registry["source_url"].notna().all(),
        "all_samples_have_dates": data_registry["access_date"].notna().all(),
        "included_samples_match": set(data_registry.query("status == 'success'").sample_id) == 
                                   set(sample_registry.query("inclusion_decision == 'include'").sample_id),
    }
    
    return {
        "passed": all(checks.values()),
        "checks": checks,
        "failed": [k for k, v in checks.items() if not v]
    }
```

### 分析一致性验证

```python
def validate_analysis_consistency(analysis_results, expected_dimensions):
    """
    分析一致性验证
    
    Args:
        analysis_results: 分析结果
        expected_dimensions: 预期维度列表
    
    Returns:
        dict: 验证结果
    """
    checks = {
        "all_dimensions_covered": all(d in analysis_results for d in expected_dimensions),
        "all_results_have_evidence": all(
            analysis_results[d].has_evidence() 
            for d in analysis_results
        ),
        "all_figures_have_sources": all(
            fig.has_data_source() 
            for fig in analysis_results.figures
        ),
    }
    
    return {
        "passed": all(checks.values()),
        "checks": checks,
        "failed": [k for k, v in checks.items() if not v]
    }
```

### 论文质量验证

```python
def validate_paper_quality(draft, requirements):
    """
    论文质量验证
    
    Args:
        draft: 论文草稿
        requirements: 质量要求
    
    Returns:
        dict: 验证结果
    """
    checks = {
        "scope_statement_precise": draft.contains(requirements["scope_statement"]),
        "baseline_distinguished": draft.distinguishes_baseline_and_extension(),
        "methods_covered": draft.has_methods_section(),
        "results_covered": draft.has_results_section(),
        "limitations_present": draft.has_limitations_section(),
        "references_complete": draft.has_complete_references(),
        "figures_referenced": draft.all_figures_referenced(),
        "word_count_met": draft.word_count() >= requirements["min_words"],
    }
    
    return {
        "passed": all(checks.values()),
        "checks": checks,
        "failed": [k for k, v in checks.items() if not v]
    }
```

---

## 质量门禁配置示例

### 完整质量门禁配置

```yaml
quality_gates:
  # 阶段1：研究分析
  - stage: 1
    name: "研究分析质量门禁"
    checks:
      - metric: "baseline_count_correct"
        threshold: true
        description: "基准研究样本数量与原论文一致"
      - metric: "research_direction_aligned"
        threshold: true
        description: "研究方向文档解析完整"
      - metric: "extension_questions_defined"
        threshold: true
        description: "延伸研究问题明确"
    action_on_fail: "block"
  
  # 阶段2：样本核验
  - stage: 2
    name: "样本核验质量门禁"
    checks:
      - metric: "all_samples_verified"
        threshold: 1.0
        description: "所有样本都有来源验证"
      - metric: "exclusion_reasons_documented"
        threshold: true
        description: "排除样本有明确说明"
    action_on_fail: "block"
  
  # 阶段3：数据采集
  - stage: 3
    name: "数据采集质量门禁"
    checks:
      - metric: "download_success_rate"
        threshold: 0.8
        description: "数据采集成功率至少80%"
      - metric: "metadata_complete"
        threshold: 1.0
        description: "所有成功采集的数据都有完整元数据"
    action_on_fail: "warn"
  
  # 阶段4：数据处理
  - stage: 4
    name: "数据处理质量门禁"
    checks:
      - metric: "cleaning_complete"
        threshold: 1.0
        description: "所有数据都完成清洗"
      - metric: "qc_passed_rate"
        threshold: 0.9
        description: "质量检查通过率至少90%"
    action_on_fail: "continue_improvement"
  
  # 阶段5：分析建模
  - stage: 5
    name: "分析建模质量门禁"
    checks:
      - metric: "all_dimensions_analyzed"
        threshold: 1.0
        description: "所有核心维度都有分析"
      - metric: "figures_generated"
        threshold: true
        description: "所有图表已生成"
      - metric: "results_interpreted"
        threshold: true
        description: "所有结果都有解释"
    action_on_fail: "block"
  
  # 阶段6：论文写作
  - stage: 6
    name: "论文写作质量门禁"
    checks:
      - metric: "structure_complete"
        threshold: 1.0
        description: "所有章节完整"
      - metric: "references_complete"
        threshold: 1.0
        description: "参考文献完整"
      - metric: "word_count"
        threshold: 30000
        description: "字数达标"
      - metric: "logic_coherent"
        threshold: 0.9
        description: "逻辑连贯性评分"
    action_on_fail: "continue_improvement"
```

---

## 验证记录模板

### 阶段验证报告

```markdown
# [阶段名称]验证报告

## 基本信息

- 阶段编号: [编号]
- 阶段名称: [名称]
- 验证时间: [时间]
- 验证结果: [通过/未通过]

## 验证项详情

| 验证项 | 预期 | 实际 | 结果 |
|---|---|---|---|
| [验证项1] | [预期值] | [实际值] | [通过/失败] |
| [验证项2] | [预期值] | [实际值] | [通过/失败] |

## 问题清单

### 必须修复

- [ ] [问题1]
- [ ] [问题2]

### 建议优化

- [ ] [建议1]
- [ ] [建议2]

## 下一步行动

- [行动1]
- [行动2]
```

---

## 异常处理机制

### 验证失败处理流程

```yaml
failure_handling:
  # 必须修复类问题
  block_issues:
    action: "immediate_stop"
    remediation:
      - "记录问题详情"
      - "生成修复任务"
      - "等待修复后重新验证"
  
  # 持续改进类问题
  improvement_issues:
    action: "continue_with_logging"
    remediation:
      - "记录改进项"
      - "继续执行后续阶段"
      - "在改进阶段统一处理"
  
  # 警告类问题
  warning_issues:
    action: "log_and_continue"
    remediation:
      - "记录警告信息"
      - "继续执行"
      - "可选：在后续阶段修正"
```

### 验证历史追踪

```python
def track_validation_history(stage_name, validation_result, log_file):
    """
    追踪验证历史
    
    Args:
        stage_name: 阶段名称
        validation_result: 验证结果
        log_file: 日志文件路径
    """
    import json
    from datetime import datetime
    
    entry = {
        "stage": stage_name,
        "timestamp": datetime.now().isoformat(),
        "passed": validation_result["passed"],
        "failed_checks": validation_result["failed"],
        "checks": validation_result["checks"]
    }
    
    with open(log_file, "a") as f:
        f.write(json.dumps(entry) + "\n")
```
