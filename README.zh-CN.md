# jcao-skills

[English](./README.md) | 简体中文

反复做同一件事做烦了，市面上又没有合适的工具，就自己写了这些 Skills。

> 有些是朋友一直找我要的，就整理出来放在这里了。

---

## Skills 列表

| Skill | 功能 | 状态 |
|-------|------|------|
| [flow2-graph-prompt](./flow2-graph-prompt/) | 把任意 pipeline 描述转成可直接粘贴到 Gemini 的流程图生成 Prompt——NotebookLM 风格 | 🔥 Battle-tested |

---

## 即将发布

| Skill | 功能 |
|-------|------|
| multi-fallback-websearch | 抓取任何反爬 URL——六层降级策略：curl → Jina Reader → Scrapling → Tavily Extract → Wayback Machine → Tavily Search |
| verify-deep-research | 验证 AI 生成研究报告中的每条 URL 引用，逐一核查原文，评估来源权威性，输出修正后的文档 |
| npu-env-setup | 交互式配置 NPU 执行环境——支持本地、SSH 远程及 Docker 容器内部署 |
| notebook-llm-pipeline | 自动化 NotebookLM 工作流：通过 MCP 添加资料、查询笔记本、生成音频或学习材料 |
| industry-pipeline | 端到端研究目标公司：行业图谱、竞争格局、岗位匹配分析 |

---

## 安装方法

每个 Skill 是一个 `SKILL.md` 文件，复制到 Claude 的 skills 目录即可：

```bash
# macOS / Linux
cp flow2-graph-prompt/SKILL.md ~/.claude/skills/flow2-graph-prompt/SKILL.md
```

然后在 Claude Code 中直接调用：

```
/flow2-graph-prompt
```

---

## 状态说明

| 标签 | 含义 |
|------|------|
| 🔥 Battle-tested | 在真实工作流中反复使用，经过验证 |
| 🌱 New | 刚发布，欢迎反馈 |

---

## 计划

- [ ] 为每个 Skill 补充脱敏示例数据及运行结果截图

## 贡献

发现问题或有改进建议？欢迎提 Issue 或 PR。

---

## 我日常在用的 Skills

- [obra/superpowers](https://github.com/obra/superpowers) — 开发工作流 Skills：TDD、系统化调试、git worktrees、代码审查。我开始写自己的 Skills 的直接原因。
- [mattpocock/skills](https://github.com/mattpocock/skills) — Matt 直接从 `.claude` 目录开放的个人 Skills，是我见过最真实的个人 Skill 仓库参考。
