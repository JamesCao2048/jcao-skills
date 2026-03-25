# jcao-skills

[English](./README.md) | 简体中文

反复做同一件事做烦了，市面上又没有合适的工具，就自己写了这些 Skills。

> 有些是朋友一直找我要的，就整理出来放在这里了。

---

## Skills 列表

| Skill | 功能 | 状态 |
|-------|------|------|
| [flow-viz-prompt](./skills/flow-viz-prompt/) | 把任意 pipeline 描述或代码转成可直接粘贴的 NotebookLM 风格流程图生成 Prompt（支持汇报粒度和代码粒度） | 🔥 Battle-tested |
| [skill-publisher](./skills/skill-publisher/) | 扫描个人或私有 Skill 的隐私泄露、i18n 问题和结构问题，输出脱敏后的发布版本，不修改源文件 | 🌱 New |
| [device-env-setup](./skills/device-env-setup/) | 配置 Claude 在带有加速卡（NVIDIA GPU或Ascend NPU）的机器上执行命令的方式。支持本地、SSH、Docker 和 Conda 环境 | 🌱 New |

🔥 Battle-tested — 在真实工作流中反复使用，经过验证 · 🌱 New — 刚发布，欢迎反馈

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
| notebook-llm-pipeline | 自动化 NotebookLM 工作流：通过 MCP 添加资料、查询笔记本、生成音频或学习材料 |

---

## 安装方法

**方式一 — npx（一行搞定）**

```bash
npx skills@latest add JamesCao2048/jcao-skills/skills/flow-viz-prompt
```

直接安装到 `~/.claude/skills/`，用 `/flow-viz-prompt` 调用。

**方式二 — Claude Code Plugin**

所有 skill 统一在 `jcao-skills:` 命名空间下：

```
/plugin marketplace add JamesCao2048/jcao-skills
/plugin install jcao-skills@jcao-skills
```

用 `/jcao-skills:flow-viz-prompt` 调用。

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

## 更新方法

**npx** — 重新执行安装命令即可覆盖：
```bash
npx skills@latest add JamesCao2048/jcao-skills/skills/flow-viz-prompt
```

**Plugin** — 更新全部已安装插件：
```
/plugin update
```
Claude Code 启动时也会后台自动更新。

---

## 贡献

发现问题或有改进建议？欢迎提 Issue 或 PR。

---

## 我日常在用的 Skills

- [obra/superpowers](https://github.com/obra/superpowers) — 开发工作流 Skills：TDD、系统化调试、git worktrees、代码审查。有了它，Claude Code 对我来说变成了一个完全不同的工具。
- [mattpocock/skills](https://github.com/mattpocock/skills) — Matt 直接从 `.claude` 目录开放的个人 Skills，是我见过最真实的个人 Skill 仓库参考。
