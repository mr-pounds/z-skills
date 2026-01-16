# Z-skills

给我自己用的 skills 包

## 使用方式

[Openskills](https://github.com/numman-ali/openskills)+Trae（白嫖模型 API）。

### openskills
1. Install
```bash
npm i -g openskills
```
2. Install Skills

```bash
# Install from Anthropic's marketplace (interactive selection, default: project)
openskills install anthropics/skills

# Or install from any GitHub repo
openskills install your-org/custom-skills
```
3. Sync to AGENTS.md
NOTE: You must have a pre-existing AGENTS.md file for sync to update.
```bash
openskills sync
```
Done! Your agent now has skills with the same <available_skills> format as Claude Code.

## Skills 源

从以下源中筛选而来，包含大部分常用的内容：
- [Claude skills](https://github.com/anthropics/skills/tree/main)：官方，推荐
- [awesome-agent-skills](https://github.com/heilcheng/awesome-agent-skills/blob/main/README.zh-CN.md)：内容比较全，但 skill 少
- [awesome-claude-skills](https://github.com/BehiSecc/awesome-claude-skills/)：索引，质量一般
- [superpowers](https://github.com/obra/superpowers)：开发相关的 Skill
- [planning-with-files](https://github.com/OthmanAdi/planning-with-files)：意义不是很大
- [notebooklm-skill](https://github.com/PleasePrompto/notebooklm-skill)：使用 NotebookLM 的，用不太上。同类的还有 [notebooklm-py](https://github.com/teng-lin/notebooklm-py)
- [claude-code-infrastructure-showcase](https://github.com/diet103/claude-code-infrastructure-showcase)：使用 Claude Code 开发 TS 的模板项目，包含了给 AI 的各类工具。
- [agent-skills](https://github.com/vercel-labs/agent-skills/tree/main/skills)：vercel 实践的 skill 产物，不多
- [x-article-publisher-skill](https://github.com/wshuyi/x-article-publisher-skill)：直给，用不太上
- [drawio-skills](https://github.com/bahayonghang/drawio-skills)
- [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
- [obsidian-skills](https://github.com/kepano/obsidian-skills)
- [awesome-copilot](https://github.com/github/awesome-copilot)


## Agent 相关

Agent 开发相关的 Skills 全部删除了，届时从下面库安装即可。

- [Agent-Skills-for-Context-Engineering](https://github.com/muratcankoylan/Agent-Skills-for-Context-Engineering)：Agent 上下文工程相关 Skill