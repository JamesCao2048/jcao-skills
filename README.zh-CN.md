# jcao-skills

[English](./README.md) | 简体中文

反复做同一件事做烦了，市面上又没有合适的工具，就自己写了这些 Skills。

> 有些是朋友一直找我要的，就整理出来放在这里了。

---

## Skills 列表

| Skill | 功能 | 状态 |
|-------|------|------|
| [flow-viz-prompt](./flow-viz-prompt/) | 把任意 pipeline 描述转成可直接粘贴到 Gemini 的流程图生成 Prompt——NotebookLM 风格 | 🔥 Battle-tested |
| [skill-publisher](./skill-publisher/) | 扫描内部 Skill 的隐私泄露、i18n 问题和结构问题，输出脱敏后的发布版本，不修改源文件 | 🌱 New |

---

## 示例

### flow-viz-prompt

以下两张图用这个 Skill 生成提示词，再由 Gemini 图片生成模型渲染输出。

---

**Codex Subagent Spawn：关键设计决策**

触发词：*"帮我画一张 Codex agent 的代码级设计图——parent model 怎么 spawn 子 agent、经过哪些 safety gate、async 完成通知怎么工作？基于 `codex-rs/` 代码库。"*

→ 命中**设计粒度**（关键词"代码级"）。Skill 读取代码库，确认控制流（AgentControl → 安全门 → fork vs 新线程决策 → 完成监控器），然后生成提示词。

![Codex Subagent Spawn](flow-viz-prompt/examples/codex-subagent-spawn.png)

---

**OpenClaw 内存架构——哪些被记住，哪些被丢弃**

触发词：*"给我一张 OpenClaw memory architecture 的流程图，要展示哪些内容会被记住、哪些会被丢弃。从上到下排列。"*

→ 命中**汇报粒度**（默认）。Skill 逐一确认输入/输出层和核心步骤（channel history buffer → session transcript → 持久化路径 → 长期内存 flush），然后生成提示词。

![OpenClaw Memory Architecture](flow-viz-prompt/examples/openclaw-memory-arch.png)

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

每个 Skill 是一个 `SKILL.md` 文件，复制到 Claude 的 skills 目录即可：

```bash
# macOS / Linux
cp flow-viz-prompt/SKILL.md ~/.claude/skills/flow-viz-prompt/SKILL.md
cp skill-publisher/SKILL.md ~/.claude/skills/skill-publisher/SKILL.md
```

然后在 Claude Code 中直接调用：

```
/flow-viz-prompt
/skill-publisher
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

- [obra/superpowers](https://github.com/obra/superpowers) — 开发工作流 Skills：TDD、系统化调试、git worktrees、代码审查。我开始写自己的 Skills 的直接原因。
- [mattpocock/skills](https://github.com/mattpocock/skills) — Matt 直接从 `.claude` 目录开放的个人 Skills，是我见过最真实的个人 Skill 仓库参考。
