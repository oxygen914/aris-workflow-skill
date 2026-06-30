# 路径检测配置

本文档定义了 ARIS 工作流计划书生成前的路径和 skill 可用性检测机制。

## 检测流程

```text
开始
  ↓
检测 ARIS 工作流主文档
  ↓
检测 Auto-claude-code-research-in-sleep 根目录
  ↓
检测 Skills 安装目录
  ↓
检测核心 Skills 可用性
  ↓
检测输入文件路径
  ↓
检测输出目录可创建性
  ↓
检测工具可用性
  ↓
生成检测报告
  ↓
向用户确认
```

## 路径检测配置

### 1. ARIS 工作流核心路径

```yaml
aris_core_paths:
  aris_workflow_main:
    description: "ARIS 工作流主文档"
    paths:
      - "/Users/didi/Desktop/code/aris-workflow.md"
      - "~/Desktop/code/aris-workflow.md"
    required: false
    on_missing: "warn"
    fallback: "将在计划书中使用占位符"
  
  auto_claude_code_root:
    description: "Auto-claude-code-research-in-sleep 根目录"
    paths:
      - "/Users/didi/Desktop/code"
      - "~/Desktop/code"
    required: true
    on_missing: "error"
    check_content:
      - "rag/skills/"
      - ".claude/"
    message: "Auto-claude-code-research-in-sleep 目录不存在或结构不完整"
  
  skills_install_dir:
    description: "Skills 安装目录"
    paths:
      - "/Users/didi/Desktop/code/rag/skills"
      - "~/.codex/skills"
    required: true
    on_missing: "error"
    message: "Skills 安装目录不存在，请先安装必要的 skills"
```

### 2. 核心 Skills 检测

```yaml
core_skills_detection:
  # 论文规划类
  paper_plan:
    name: "paper-plan"
    description: "论文规划 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/paper-plan"
      - "/Users/didi/Desktop/code/.claude/skills/paper-plan"
      - "~/.codex/skills/paper-plan"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
    install_hint: "从 skills 仓库获取或手动创建"
  
  paper_write:
    name: "paper-write"
    description: "论文写作 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/paper-write"
      - "/Users/didi/Desktop/code/.claude/skills/paper-write"
      - "~/.codex/skills/paper-write"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  paper_figure:
    name: "paper-figure"
    description: "图表生成 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/paper-figure"
      - "/Users/didi/Desktop/code/.claude/skills/paper-figure"
      - "~/.codex/skills/paper-figure"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  paper_compile:
    name: "paper-compile"
    description: "论文编译 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/paper-compile"
      - "/Users/didi/Desktop/code/.claude/skills/paper-compile"
      - "~/.codex/skills/paper-compile"
    required: false
    required_for: "Workflow 3 - Paper Writing (LaTeX)"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 自动改进类
  auto_review_loop:
    name: "auto-review-loop"
    description: "自动审核循环 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/auto-review-loop"
      - "/Users/didi/Desktop/code/.claude/skills/auto-review-loop"
      - "~/.codex/skills/auto-review-loop"
    required: false
    required_for: "Workflow 2 - Iterative Improvement"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  auto_paper_improvement_loop:
    name: "auto-paper-improvement-loop"
    description: "论文自动改进循环 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/auto-paper-improvement-loop"
      - "/Users/didi/Desktop/code/.claude/skills/auto-paper-improvement-loop"
      - "~/.codex/skills/auto-paper-improvement-loop"
    required: false
    required_for: "Workflow 3 - Paper Writing"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 研究发现类
  idea_discovery:
    name: "idea-discovery"
    description: "研究问题发现 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/idea-discovery"
      - "/Users/didi/Desktop/code/.claude/skills/idea-discovery"
      - "~/.codex/skills/idea-discovery"
    required: false
    required_for: "Workflow 1 - Idea Discovery"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 实验桥接类
  experiment_bridge:
    name: "experiment-bridge"
    description: "实验桥接 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/experiment-bridge"
      - "/Users/didi/Desktop/code/.claude/skills/experiment-bridge"
      - "~/.codex/skills/experiment-bridge"
    required: false
    required_for: "Workflow 1.5 - Experiment Bridge"
    check_files:
      - "SKILL.md"
    on_missing: "warn"
  
  # 辅助类
  experiment_plan:
    name: "experiment-plan"
    description: "实验计划 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/experiment-plan"
      - "/Users/didi/Desktop/code/.claude/skills/experiment-plan"
      - "~/.codex/skills/experiment-plan"
    required: false
    check_files:
      - "SKILL.md"
    on_missing: "ignore"
  
  novelty_check:
    name: "novelty-check"
    description: "新颖性检查 skill"
    paths:
      - "/Users/didi/Desktop/code/rag/skills/novelty-check"
      - "/Users/didi/Desktop/code/.claude/skills/novelty-check"
      - "~/.codex/skills/novelty-check"
    required: false
    check_files:
      - "SKILL.md"
    on_missing: "ignore"
```

### 3. 工具检测

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
  
  bibtex:
    name: "bibtex"
    description: "BibTeX 参考文献"
    check_command: "bibtex --version"
    required: false
    required_for: "LaTeX 参考文献"
    on_missing: "warn"
  
  ffmpeg:
    name: "ffmpeg"
    description: "音视频处理工具"
    check_command: "ffmpeg -version"
    required: false
    required_for: "视频/音频数据处理"
    on_missing: "warn"
    install_hint: "brew install ffmpeg"
  
  yt_dlp:
    name: "yt-dlp"
    description: "视频下载工具"
    check_command: "yt-dlp --version"
    required: false
    required_for: "在线视频下载"
    on_missing: "warn"
    install_hint: "pip install yt-dlp"
  
  git:
    name: "git"
    description: "版本控制"
    check_command: "git --version"
    required: false
    on_missing: "warn"
```

### 4. Python 库检测

```yaml
python_libraries_detection:
  # 数据处理
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
  
  # 中文处理
  jieba:
    import: "jieba"
    required: false
    required_for: "中文分词"
    install: "pip install jieba"
  
  # 可视化
  matplotlib:
    import: "matplotlib"
    required: false
    required_for: "图表生成"
    install: "pip install matplotlib"
  
  seaborn:
    import: "seaborn"
    required: false
    required_for: "统计图表"
    install: "pip install seaborn"
  
  # 文档处理
  pypdf:
    import: "pypdf"
    required: false
    required_for: "PDF 读取"
    install: "pip install pypdf"
  
  python_docx:
    import: "docx"
    required: false
    required_for: "Word 文档处理"
    install: "pip install python-docx"
  
  # 语音识别
  whisper:
    import: "whisper"
    required: false
    required_for: "语音转文字"
    install: "pip install openai-whisper"
  
  faster_whisper:
    import: "faster_whisper"
    required: false
    required_for: "快速语音转文字"
    install: "pip install faster-whisper"
```

## 检测脚本

```python
#!/usr/bin/env python3
"""
路径和 Skill 可用性检测脚本
"""

import os
import sys
import subprocess
import json
from pathlib import Path
from typing import Dict, List, Optional, Tuple

class PathDetector:
    """路径检测器"""
    
    def __init__(self):
        self.results = {
            "core_paths": {},
            "skills": {},
            "tools": {},
            "python_libraries": {},
            "input_files": {},
            "output_directory": {}
        }
        self.errors = []
        self.warnings = []
    
    def expand_path(self, path: str) -> str:
        """扩展路径中的 ~ 和环境变量"""
        return os.path.expanduser(os.path.expandvars(path))
    
    def check_path_exists(self, path: str, description: str) -> Tuple[bool, str]:
        """检查路径是否存在"""
        expanded = self.expand_path(path)
        exists = os.path.exists(expanded)
        return exists, expanded
    
    def check_skill_available(self, skill_name: str, paths: List[str]) -> Dict:
        """检查 skill 是否可用"""
        for path in paths:
            expanded = self.expand_path(path)
            skill_md = os.path.join(expanded, "SKILL.md")
            if os.path.exists(skill_md):
                return {
                    "available": True,
                    "path": expanded,
                    "skill_md": skill_md
                }
        return {
            "available": False,
            "path": None,
            "skill_md": None
        }
    
    def check_tool_available(self, command: str) -> Tuple[bool, str]:
        """检查工具是否可用"""
        try:
            result = subprocess.run(
                command.split(),
                capture_output=True,
                text=True,
                timeout=5
            )
            return True, result.stdout.strip()
        except (subprocess.TimeoutExpired, FileNotFoundError, Exception) as e:
            return False, str(e)
    
    def check_python_library(self, import_name: str) -> Tuple[bool, str]:
        """检查 Python 库是否可用"""
        try:
            __import__(import_name)
            return True, "available"
        except ImportError as e:
            return False, str(e)
    
    def detect_all(self, config: Dict) -> Dict:
        """执行所有检测"""
        # 检测核心路径
        for key, path_config in config.get("aris_core_paths", {}).items():
            paths = path_config.get("paths", [])
            for path in paths:
                exists, expanded = self.check_path_exists(path, path_config["description"])
                if exists:
                    self.results["core_paths"][key] = {
                        "status": "available",
                        "path": expanded,
                        "description": path_config["description"]
                    }
                    break
            else:
                self.results["core_paths"][key] = {
                    "status": "missing",
                    "path": None,
                    "description": path_config["description"]
                }
                if path_config.get("required"):
                    self.errors.append(f"核心路径缺失: {path_config['description']}")
                elif path_config.get("on_missing") == "warn":
                    self.warnings.append(f"路径缺失: {path_config['description']}")
        
        # 检测 skills
        for skill_name, skill_config in config.get("core_skills_detection", {}).items():
            result = self.check_skill_available(skill_name, skill_config["paths"])
            self.results["skills"][skill_name] = {
                **result,
                "description": skill_config["description"],
                "required_for": skill_config.get("required_for", "")
            }
            if not result["available"] and skill_config.get("on_missing") == "warn":
                self.warnings.append(f"Skill 未安装: {skill_name} ({skill_config['description']})")
        
        # 检测工具
        for tool_name, tool_config in config.get("tools_detection", {}).items():
            available, info = self.check_tool_available(tool_config["check_command"])
            self.results["tools"][tool_name] = {
                "available": available,
                "info": info,
                "description": tool_config["description"]
            }
            if not available and tool_config.get("required"):
                self.errors.append(f"工具缺失: {tool_name}")
        
        # 检测 Python 库
        for lib_name, lib_config in config.get("python_libraries_detection", {}).items():
            available, info = self.check_python_library(lib_config["import"])
            self.results["python_libraries"][lib_name] = {
                "available": available,
                "info": info,
                "required_for": lib_config.get("required_for", "")
            }
        
        return {
            "results": self.results,
            "errors": self.errors,
            "warnings": self.warnings,
            "success": len(self.errors) == 0
        }
    
    def generate_report(self, output_path: str):
        """生成检测报告"""
        report = {
            "timestamp": datetime.now().isoformat(),
            "results": self.results,
            "errors": self.errors,
            "warnings": self.warnings,
            "summary": {
                "total_errors": len(self.errors),
                "total_warnings": len(self.warnings),
                "skills_available": sum(1 for s in self.results["skills"].values() if s.get("available")),
                "skills_total": len(self.results["skills"])
            }
        }
        
        with open(output_path, "w") as f:
            yaml.dump(report, f, default_flow_style=False, allow_unicode=True)
        
        return report


def main():
    """主函数"""
    # 加载配置
    config_path = os.path.join(os.path.dirname(__file__), "path-detection.yaml")
    with open(config_path) as f:
        config = yaml.safe_load(f)
    
    # 执行检测
    detector = PathDetector()
    result = detector.detect_all(config)
    
    # 输出结果
    print(json.dumps(result, indent=2, ensure_ascii=False))
    
    return 0 if result["success"] else 1


if __name__ == "__main__":
    sys.exit(main())
```

## 检测报告格式

```yaml
# skill-availability.yaml 示例

timestamp: "2024-06-30T10:00:00"

results:
  core_paths:
    auto_claude_code_root:
      status: "available"
      path: "/Users/didi/Desktop/code"
      description: "Auto-claude-code-research-in-sleep 根目录"
    
    skills_install_dir:
      status: "available"
      path: "/Users/didi/Desktop/code/rag/skills"
      description: "Skills 安装目录"
  
  skills:
    paper-plan:
      available: true
      path: "/Users/didi/Desktop/code/rag/skills/paper-plan"
      description: "论文规划 skill"
    
    paper-write:
      available: true
      path: "/Users/didi/Desktop/code/rag/skills/paper-write"
      description: "论文写作 skill"
    
    idea-discovery:
      available: false
      path: null
      description: "研究问题发现 skill"
  
  tools:
    python3:
      available: true
      info: "Python 3.11.0"
    
    xelatex:
      available: false
      info: "command not found"
  
  python_libraries:
    pandas:
      available: true
      required_for: "数据处理和分析"
    
    jieba:
      available: true
      required_for: "中文分词"

errors: []

warnings:
  - "Skill 未安装: idea-discovery (研究问题发现 skill)"
  - "工具缺失: xelatex"

summary:
  total_errors: 0
  total_warnings: 2
  skills_available: 6
  skills_total: 8
```

## 用户确认界面

当检测完成后，向用户展示检测结果并请求确认：

```yaml
confirmation_questions:
  - question: "检测到以下路径和 Skills 状态，是否继续生成计划书？"
    display:
      - "✅ Auto-claude-code-research-in-sleep 根目录: /Users/didi/Desktop/code"
      - "✅ Skills 安装目录: /Users/didi/Desktop/code/rag/skills"
      - "✅ 已安装 Skills: paper-plan, paper-write, paper-figure, ..."
      - "⚠️ 未安装 Skills: idea-discovery, experiment-bridge"
      - "⚠️ 缺少工具: xelatex (LaTeX 编译器)"
    options:
      - "继续生成计划书（使用已有 Skills）"
      - "先安装缺失的 Skills"
      - "修改配置后重试"
```
