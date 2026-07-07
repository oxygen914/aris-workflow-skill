# 用户交互模板

本文档定义了 ARIS 工作流计划书生成过程中的用户交互流程和问题模板。

## 交互流程

```text
开始
  ↓
阶段1: 研究基本信息
  ↓
阶段2: 输入文件确认
  ↓
阶段3: 输出配置
  ↓
阶段4: 路径和 Skill 检测
  ↓
阶段5: 约束条件确认
  ↓
阶段6: 最终确认
  ↓
生成计划书
```

## 阶段1: 研究基本信息

### 问题组 1.1: 研究类型

```yaml
question_group: "research_basic_info"
order: 1

questions:
  - id: "research_type"
    question: "请选择您的研究类型？"
    header: "研究类型"
    multiSelect: false
    options:
      - label: "技术方法类"
        description: "提出新的技术方法、算法或系统框架，有明确的技术创新点"
        preview: |
          特点：
          - 有技术框架文档或方法论
          - 需要实现和验证技术方案
          - 通常包含实验对比和性能评估
          
          适用工作流: Workflow 3 (Paper Writing)
      
      - label: "实证研究类"
        description: "基于数据采集和分析的实证研究，如问卷调查、内容分析、案例研究"
        preview: |
          特点：
          - 需要采集外部数据
          - 包含数据处理和分析过程
          - 通常有评估工具或量表设计
          
          适用工作流: Workflow 1 -> 1.5 -> 2 -> 3
      
      - label: "文献综述类"
        description: "系统性文献综述、元分析或研究综述"
        preview: |
          特点：
          - 以文献为主要数据源
          - 需要文献检索和筛选
          - 包含文献统计和主题分析
          
          适用工作流: Workflow 1 -> 3
      
      - label: "混合研究类"
        description: "结合多种研究方法的混合研究"
        preview: |
          特点：
          - 结合技术和实证方法
          - 多阶段多方法
          - 根据具体需求定制工作流
```

### 问题组 1.2: 研究主题

```yaml
questions:
  - id: "research_topic"
    question: "请简要描述您的研究主题或论文题目？"
    header: "研究主题"
    type: "text_input"  # 需要用户输入
    placeholder: "例如：基于深度学习的图像分类方法研究"
    validation:
      min_length: 5
      max_length: 200
    required: true
```

### 问题组 1.3: 项目简称

```yaml
questions:
  - id: "project_short_name"
    question: "请为项目设置一个简称（用于文件命名）？"
    header: "项目简称"
    type: "text_input"
    placeholder: "例如：jsa, cao, dl-image"
    validation:
      pattern: "^[a-z0-9-]+$"
      min_length: 2
      max_length: 20
    help_text: "只能使用小写字母、数字和连字符，建议2-20个字符"
    required: true
```

---

## 阶段2: 输入文件确认

### 问题组 2.1: 研究方向文档

```yaml
question_group: "input_files"
order: 2

questions:
  - id: "has_research_direction"
    question: "是否有研究方向文档？"
    header: "研究方向"
    multiSelect: false
    options:
      - label: "有研究方向文档"
        description: "已有明确的研究方向说明文档"
        action: "请求输入路径"
      
      - label: "没有，需要生成"
        description: "将在计划书中生成研究方向框架"
        action: "跳过路径输入"
```

### 问题组 2.2: 研究方向文档路径

```yaml
questions:
  - id: "research_direction_path"
    question: "请提供研究方向文档的路径？"
    header: "文档路径"
    type: "text_input"
    placeholder: "/Users/xxx/Desktop/研究方向.md"
    condition:
      depends_on: "has_research_direction"
      value: "有研究方向文档"
    validation:
      check_exists: true
    on_validation_fail:
      message: "文件不存在，请确认路径是否正确"
      action: "重新输入"
```

### 问题组 2.3: 基准研究/技术框架

```yaml
questions:
  - id: "has_baseline"
    question: "是否有基准研究论文或技术框架文档？"
    header: "基准研究"
    multiSelect: false
    options:
      - label: "有基准研究论文"
        description: "在既有研究基础上延伸"
        action: "请求输入论文路径"
      
      - label: "有技术框架文档"
        description: "有明确的技术方法框架"
        action: "请求输入框架文档路径"
      
      - label: "两者都有"
        description: "同时有基准研究和技术框架"
        action: "请求输入两个路径"
      
      - label: "都没有"
        description: "全新研究，无基准"
        action: "跳过"
```

### 问题组 2.4: 文档路径输入

```yaml
questions:
  - id: "baseline_paper_path"
    question: "请提供基准研究论文的路径？"
    header: "论文路径"
    type: "text_input"
    placeholder: "/Users/xxx/Desktop/基准研究.pdf"
    condition:
      depends_on: "has_baseline"
      value_in: ["有基准研究论文", "两者都有"]
    validation:
      check_exists: true
  
  - id: "framework_file_path"
    question: "请提供技术框架文档的路径？"
    header: "框架路径"
    type: "text_input"
    placeholder: "/Users/xxx/Desktop/技术框架.md"
    condition:
      depends_on: "has_baseline"
      value_in: ["有技术框架文档", "两者都有"]
    validation:
      check_exists: true
```

---

## 阶段3: 输出配置

### 问题组 3.1: 输出目录

```yaml
question_group: "output_config"
order: 3

questions:
  - id: "output_root_dir"
    question: "计划书和研究产物将保存到哪个目录？"
    header: "输出目录"
    type: "text_input"
    placeholder: "/Users/xxx/Desktop/论文/工作流输出"
    default: "<output-root>/工作流输出-{项目简称}"
    validation:
      check_parent_exists: true
      check_writable: true
    on_validation_fail:
      message: "父目录不存在或不可写"
      action: "重新输入"
```

### 问题组 3.2: 预计周期

```yaml
questions:
  - id: "timeline"
    question: "预计研究周期是多长？"
    header: "研究周期"
    multiSelect: false
    options:
      - label: "1周内"
        description: "快速研究项目"
      - label: "1-2周"
        description: "短期研究项目"
      - label: "2-4周"
        description: "中期研究项目"
      - label: "1-2个月"
        description: "长期研究项目"
      - label: "2个月以上"
        description: "大型研究项目"
```

---

## 阶段4: 路径和 Skill 检测

### 问题组 4.1: 检测结果确认

在用户回答以上问题后，系统会自动执行路径和 Skill 检测，然后向用户展示检测结果：

```yaml
question_group: "detection_results"
order: 4

questions:
  - id: "detection_confirmation"
    question: "检测到以下环境状态，是否继续？"
    header: "环境检测"
    multiSelect: false
    
    # 动态生成选项内容
    dynamic_content:
      source: "path_detection_results"
      template: |
        📁 核心路径:
        {{#each core_paths}}
        {{#if available}}✅{{else}}❌{{/if}} {{description}}: {{path}}
        {{/each}}
        
        🔧 已安装 Skills ({{skills_available}}/{{skills_total}}):
        {{#each skills}}
        {{#if available}}✅{{else}}⚠️{{/if}} {{name}} - {{description}}
        {{/each}}
        
        🛠️ 工具状态:
        {{#each tools}}
        {{#if available}}✅{{else}}⚠️{{/if}} {{name}}: {{info}}
        {{/each}}
        
        {{#if warnings}}
        ⚠️ 警告:
        {{#each warnings}}
        - {{this}}
        {{/each}}
        {{/if}}
        
        {{#if errors}}
        ❌ 错误:
        {{#each errors}}
        - {{this}}
        {{/each}}
        {{/if}}
    
    options:
      - label: "继续生成计划书"
        description: "使用当前环境继续"
        condition: "no_errors"
      
      - label: "查看缺失项详情"
        description: "查看需要安装的 Skills 和工具"
      
      - label: "修改配置"
        description: "返回修改输入信息"
```

---

## 阶段5: 约束条件确认

### 问题组 5.1: 特殊约束

```yaml
question_group: "constraints"
order: 5

questions:
  - id: "has_special_constraints"
    question: "是否有特殊约束或要求？"
    header: "特殊约束"
    multiSelect: true
    options:
      - label: "需要严格遵循框架文档"
        description: "所有内容必须与框架文档一致"
      
      - label: "有数据伦理要求"
        description: "涉及敏感数据或需要伦理审批"
      
      - label: "有工具能力缺口"
        description: "已知某些工具不可用，需要替代方案"
      
      - label: "有特定的输出格式要求"
        description: "需要特定的文档格式或模板"
      
      - label: "无特殊约束"
        description: "无额外约束"
```

---

## 阶段6: 最终确认

### 问题组 6.1: 信息汇总

```yaml
question_group: "final_confirmation"
order: 6

questions:
  - id: "final_summary"
    question: "请确认以下信息是否正确？"
    header: "信息确认"
    multiSelect: false
    
    # 动态生成汇总内容
    dynamic_content:
      source: "user_inputs"
      template: |
        📋 研究信息汇总
        
        研究类型: {{research_type}}
        研究主题: {{research_topic}}
        项目简称: {{project_short_name}}
        
        📁 输入文件:
        {{#if research_direction_path}}
        - 研究方向: {{research_direction_path}}
        {{/if}}
        {{#if baseline_paper_path}}
        - 基准论文: {{baseline_paper_path}}
        {{/if}}
        {{#if framework_file_path}}
        - 技术框架: {{framework_file_path}}
        {{/if}}
        
        📂 输出配置:
        - 输出目录: {{output_root_dir}}
        - 预计周期: {{timeline}}
        
        ⚙️ 环境状态:
        - Skills 可用: {{skills_available}}/{{skills_total}}
        - 工具可用: {{tools_available}}/{{tools_total}}
        
        📌 特殊约束:
        {{#each constraints}}
        - {{this}}
        {{/each}}
    
    options:
      - label: "确认无误，生成计划书"
        description: "根据以上信息生成完整计划书"
      
      - label: "修改部分信息"
        description: "返回修改某些信息"
      
      - label: "取消"
        description: "取消本次操作"
```

---

## 交互状态管理

### 状态存储

```yaml
interaction_state:
  # 用户输入
  user_inputs:
    research_type: null
    research_topic: null
    project_short_name: null
    research_direction_path: null
    baseline_paper_path: null
    framework_file_path: null
    output_root_dir: null
    timeline: null
    constraints: []
  
  # 检测结果
  detection_results:
    core_paths: {}
    skills: {}
    tools: {}
    warnings: []
    errors: []
  
  # 交互进度
  progress:
    current_stage: 1
    completed_stages: []
    total_stages: 6
```

### 状态持久化

```python
def save_interaction_state(state: dict, output_path: str):
    """保存交互状态"""
    import yaml
    from datetime import datetime
    
    state["timestamp"] = datetime.now().isoformat()
    
    with open(output_path, "w") as f:
        yaml.dump(state, f, default_flow_style=False, allow_unicode=True)

def load_interaction_state(input_path: str) -> dict:
    """加载交互状态"""
    import yaml
    
    with open(input_path, "r") as f:
        return yaml.safe_load(f)
```

---

## 交互模板使用示例

### 在 Skill 中调用

```markdown
## 阶段 1: 信息收集与验证

### 步骤 1.1: 收集研究基本信息

向用户确认问题：
  - id: research_type
    question: "请选择您的研究类型？"
    header: "研究类型"
    options:
      - label: "技术方法类"
        description: "提出新的技术方法、算法或系统框架"
      - label: "实证研究类"
        description: "基于数据采集和分析的实证研究"
      - label: "文献综述类"
        description: "系统性文献综述"
      - label: "混合研究类"
        description: "结合多种研究方法"

保存用户回复到: interaction_state.user_inputs.research_type

### 步骤 1.2: 检测路径和 Skills

执行路径和 Skills 检测
输入: interaction_state.user_inputs
输出: interaction_state.detection_results

### 步骤 1.3: 确认检测结果

向用户确认检测结果：
  - id: detection_confirmation
    question: "检测到以下环境状态，是否继续？"
    header: "环境检测"
    dynamic_content:
      source: interaction_state.detection_results
```
