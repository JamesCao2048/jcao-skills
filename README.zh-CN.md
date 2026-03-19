# jcao-skills

[English](./README.md) | 简体中文

反复做同一件事做烦了，市面上又没有合适的工具，就自己写了这些 Skills。

> 有些是朋友一直找我要的，就整理出来放在这里了。

---

## Skills 列表

| Skill | 功能 | 状态 |
|-------|------|------|
| [flow-viz-prompt](./flow-viz-prompt/) | 把任意 pipeline 描述或代码转成可直接粘贴的 NotebookLM 风格流程图生成 Prompt（支持汇报粒度和代码粒度） | 🔥 Battle-tested |
| [skill-publisher](./skill-publisher/) | 扫描个人或私有 Skill 的隐私泄露、i18n 问题和结构问题，输出脱敏后的发布版本，不修改源文件 | 🌱 New |

---

## 示例

### flow-viz-prompt

以下两张图用这个 Skill 生成提示词，再由 Gemini Pro（Nano-Banana 2）渲染输出。

---

**Codex Subagent Spawn：关键设计决策**

触发词：*"帮我生成 Codex subagent 机制的代码级设计图提示词，重点展示关键设计决策和原因。代码库：`xxx/codex/`。"*

→ 命中**设计粒度**（关键词"代码级"）。Skill 读取代码库，与用户逐步确认控制流（AgentControl → 安全门 → fork vs 新线程决策 → 完成监控器）。用户可以审阅并修改，最终确认后 agent 生成提示词。

![Codex Subagent Spawn](examples/flow-viz-prompt/codex-subagent-spawn.png)

---

**OpenClaw 内存架构——哪些被记住，哪些被丢弃**

触发词：*"给我一个 memory architecture 的流程图提示词，展示 Discord / Slack 频道里哪些内容被记住、哪些被丢弃。代码库：`xxx/openclaw`。"*

→ 命中**汇报粒度**（默认）。Skill 逐一确认输入/输出层和核心步骤（channel history buffer → session transcript → 持久化路径 → 长期内存 flush），然后生成提示词。

![OpenClaw Memory Architecture](examples/flow-viz-prompt/openclaw-memory-arch.png)

---

## 即将发布

| Skill | 功能 |
|-------|------|
| multi-fallback-websearch | 抓取任何反爬 URL——六层降级策略：curl → Jina Reader → Scrapling → Tavily Extract → Wayback Machine → Tavily Search |
| verify-deep-research | 验证 AI 生成研究报告中的每条 URL 引用，逐一核查原文，评估来源权威性，输出修正后的文档 |
| npu-env-setup | 交互式配置 NPU 执行环境——支持本地、SSH 远程及 Docker 容器内部署 |
| notebook-llm-pipeline | 自动化 NotebookLM 工作流：通过 MCP 添加资料、查询笔记本、生成音频或学习材料 |

---

## 安装方法

**方式一 — 让 Claude Code 帮你装（最简单，全平台通用）**

在 Claude Code 里粘贴一句话：

```
Install this skill: https://github.com/JamesCao2048/jcao-skills/tree/main/flow-viz-prompt
```

Claude 会自动拉取文件并复制到你的 skills 目录。

**方式二 — 手动复制**

克隆仓库后复制整个 Skill 文件夹：

```bash
git clone https://github.com/JamesCao2048/jcao-skills.git

# macOS / Linux
cp -r jcao-skills/flow-viz-prompt ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse jcao-skills\flow-viz-prompt $HOME\.claude\skills\
```

> 复制整个文件夹，不要只复制 `SKILL.md`，部分 Skill 在同目录下有配套的 reference 文件。

**调用方式**

用 slash 命令，或者直接用自然语言描述需求：

```
/flow-viz-prompt

# 或者直接说：
"给我的 data pipeline 生成一个流程图提示词"
"帮我画这个代码库的代码级架构图"
"这个 agent 的工作流，给我生成一张设计图的 prompt"
```

---

## 状态说明

| 标签 | 含义 |
|------|------|
| 🔥 Battle-tested | 在真实工作流中反复使用，经过验证 |
| 🌱 New | 刚发布，欢迎反馈 |

---

## 贡献

发现问题或有改进建议？欢迎提 Issue 或 PR。

---

## 我日常在用的 Skills

- [obra/superpowers](https://github.com/obra/superpowers) — 开发工作流 Skills：TDD、系统化调试、git worktrees、代码审查。有了它，Claude Code 对我来说变成了一个完全不同的工具。
- [mattpocock/skills](https://github.com/mattpocock/skills) — Matt 直接从 `.claude` 目录开放的个人 Skills，是我见过最真实的个人 Skill 仓库参考。
