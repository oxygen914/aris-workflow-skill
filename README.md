<p align="center">
  <img alt="ARIS Workflow Skill" src="assets/aris-workflow-skill-title.svg" width="720">
</p>

# ARIS Workflow Skill

有个论文想法，但不知道怎么把它拆成能一步步推进的执行计划？这个 skill 可以帮你把研究主题、输入资料和约束，变成一份可执行、可追踪、可验收的 ARIS 研究计划书。

它不是帮你写论文正文，而是先把研究想法落成**计划书**：明确有哪些阶段、每个阶段输入输出是什么、阶段之间怎么依赖、在哪些点做质量检查、文件放哪个目录。把"我要写论文"这件没头绪的事，变成一份能照着推进的施工图。

> 🌙 **它从哪来**：这个 skill 脱胎于 [**Auto-claude-code-research-in-sleep (ARIS ⚔️🌙)**](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep)——一套"睡眠中自动推进科研"的 skill 工作流方法论。ARIS 主项目把科研拆成 idea 发现、计划、写作、图表、对抗审稿等环节；本 skill 只抽取其中"**生成研究计划书**"这一步，做成可独立安装的跨平台 Agent Skill，方便你在 Codex / Claude Code 里单独使用。标题图的像素月牙🌙、像素文档和 8-bit 风格，正是对 ARIS 主项目视觉的致敬。

它默认面向跨平台 Agent Skills，同时完整兼容 Codex 和 Claude Code。

## 兼容性

| 平台 | 支持状态 | 说明 |
|---|---|---|
| Agent Skills 标准 | 完整兼容 | 使用 `SKILL.md` + `name` / `description` + `references/`。 |
| Codex | 完整兼容 | 支持 `$CODEX_HOME` / `~/.codex/skills` 安装和 `agents/openai.yaml` UI 元信息。 |
| Claude Code | 完整兼容 | 支持 `~/.claude/skills` 和项目 `.claude/skills` 安装，可用 `/aris-workflow-skill` 调用。 |

## 适合做什么

- 生成论文研究计划书，包含元数据、约束、阶段和质量门禁。
- 把研究想法转成可执行阶段，定义每个阶段的输入、输出和依赖关系。
- 设计输出目录结构，按研究类型（技术方法 / 实证 / 文献综述 / 混合）规划必需和可选目录。
- 验证输入路径和依赖 skill，生成可用性报告和缺失提醒。
- 配置工作流调度，设计任务队列、数据流转和异常处理。

## 核心工作流

1. **收集研究信息**：研究主题、研究类型、输入文件、输出目录、项目简称、约束条件。
2. **验证路径和可用 skill**：检测输入文件、核心 skills（paper-plan、paper-write 等）和工具（python3、xelatex、ffmpeg 等），生成路径验证报告和 skill 可用性报告。
3. **创建或规划输出目录**：在用户指定的输出根目录下规划标准化子目录，按研究类型决定必需和可选目录。
4. **生成计划书**：根据收集的信息和验证结果生成完整计划书，包含元数据、输入来源、约束、阶段、质量门禁、路径验证和 skill 可用性。
5. **设计阶段、质量门禁和调度**：按研究类型选阶段模板，设计阶段依赖、数据流转和检查点，配置调度日志和任务队列。
6. **输出验证结果和待确认项**：列出所有检测到的错误、警告，以及需要你补充确认的内容。

## 安装

### Codex

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R aris-workflow-skill "${CODEX_HOME:-$HOME/.codex}/skills/"
```

安装后重启或刷新 Codex，让 skill metadata 被重新发现。

### Claude Code

个人级安装：

```bash
mkdir -p ~/.claude/skills
cp -R aris-workflow-skill ~/.claude/skills/
```

项目级安装：

```bash
mkdir -p .claude/skills
cp -R aris-workflow-skill .claude/skills/
```

Claude Code 中可直接调用：

```text
/aris-workflow-skill
```

也可以让 Claude 根据 `description` 自动判断是否使用本 skill。

### 干净安装（可选）

如果从 Git 仓库复制，建议用 `rsync` 排除发布包装文件，只保留运行时必需内容：

```bash
# Codex 干净安装
rsync -av \
  --exclude ".git" \
  --exclude ".github" \
  --exclude ".DS_Store" \
  aris-workflow-skill/ "${CODEX_HOME:-$HOME/.codex}/skills/aris-workflow-skill/"

# Claude Code 个人级干净安装
rsync -av \
  --exclude ".git" \
  --exclude ".github" \
  --exclude ".DS_Store" \
  aris-workflow-skill/ ~/.claude/skills/aris-workflow-skill/
```

> 注：干净安装不复制 `.git`、`.github`、`CONTRIBUTING.md` 等发布包装，只保留运行时必需的 `SKILL.md`、`agents/`、`references/`、`assets/`。

## 使用示例

中文直接说就行：

```text
用 $aris-workflow-skill。研究主题是"基于深度学习的图像分类方法研究"，类型是技术方法类，输出目录是 ~/papers/workflow-output。
```

```text
用 $aris-workflow-skill 把我的研究想法整理成一份可执行的计划书，先和我确认关键信息和约束再生成。
```

英文也可以：

```text
Use $aris-workflow-skill to create a research plan for my thesis on empirical study of AI agent adoption.
```

## 仓库结构

```text
aris-workflow-skill/
|-- SKILL.md
|-- README.md
|-- agents/
|   `-- openai.yaml
|-- assets/
|   `-- aris-workflow-skill-title.svg
`-- references/
    |-- plan-template.md
    |-- stage-template.md
    |-- quality-gate-template.md
    |-- workflow-scheduling.md
    |-- directory-structure.md
    |-- path-detection.md
    |-- interaction-template.md
    `-- plan-examples.md
```

## 重要文件

- `SKILL.md`：运行时入口，定义触发场景、核心流程和资源路由。
- `agents/openai.yaml`：Codex/OpenAI UI 展示信息；Claude Code 会忽略它。
- `assets/aris-workflow-skill-title.svg`：GitHub README 顶部标题图。
- `references/plan-template.md`：计划书主模板。
- `references/stage-template.md`：阶段定义模板。
- `references/quality-gate-template.md`：质量门禁设计模板。
- `references/workflow-scheduling.md`：工作流调度配置。
- `references/directory-structure.md`：输出目录结构规范。
- `references/path-detection.md`：路径和 skill 可用性检测配置。
- `references/interaction-template.md`：用户交互流程和问题模板。
- `references/plan-examples.md`：完整计划书示例库。

## 校验

运行 Codex 官方基础校验：

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-creator/scripts/quick_validate.py" .
```

运行增强检查（三平台，依赖 [skill-developer](../skill-developer)）：

```bash
python3 <path-to-skill-developer>/scripts/common/check_skill_structure.py . --platform agent
python3 <path-to-skill-developer>/scripts/common/check_skill_structure.py . --platform claude
python3 <path-to-skill-developer>/scripts/common/check_skill_structure.py . --platform codex
```

> 将 `<path-to-skill-developer>` 替换为 skill-developer 在你机器上的实际路径。

## 使用边界

- 计划书不是论文最终稿，只是执行框架。
- 缺失的资料、路径或约束必须标注为待确认，不能编造。
- 不应伪造输入文件、实验结果、样本规模或研究结论。
- 路径检测发现的缺失项应提醒用户，不能自动跳过必需项；但缺少辅助 skill 不应阻塞计划书生成，只列为后续依赖缺口。

## License

本项目暂未包含 `LICENSE` 文件。公开发布前建议选择并添加合适的开源许可证。
