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
openskills install mr-pounds/z-skills
```
3. Sync to AGENTS.md
NOTE: You must have a pre-existing AGENTS.md file for sync to update.
```bash
openskills sync
```
Done! Your agent now has skills with the same <available_skills> format as Claude Code.

## Skills

| 类型         | Skill                      | 备注                           |
| ------------ | -------------------------- | ------------------------------ |
| Debug&Test   | playwright-testing         | 前端自动化测试                 |
| Debug&Test   | systematic-debugging       | 通用找bug                      |
| Debug&Test   | tdd-workflow               | 测试驱动开发                   |
| Refactor     | code-review                | 代码审查                       |
| Refactor     | code-simplifier            | 代码优化                       |
| Design       | anthropic-brand-guidelines | 品牌设计，包含一些预设风格     |
| Design       | canvas-design              | 插画绘制                       |
| Design       | theme-factory              | 预设几种设计风格主题           |
| Frontend     | frontend-design            | 通用前端设计                   |
| Frontend     | react-best-practices       | React                          |
| obsidian     | obsidian-bases             | 用不太上，备着                 |
| obsidian     | obsidian-markdown          | 用不太上，备着                 |
| Research     | deep-research              | 简易版Deep Research            |
| Second-Brain | digital-brain-skill        | 大而杂，要用的话，有一堆要修改 |
| Git          | using-git-worktrees        | 通用                           |
| Plan         | brainstorming              | 头脑风暴                       |
| Plan         | writing-plans              | planner                        |
| File         | docx                       | docx                           |
| File         | drawio                     | drawio                         |
| File         | pdf                        | pdf                            |
| File         | pptx                       | pptx                           |
| File         | xlsx                       | xlsx                           |
| Database     | postgres                   | postgres                       |


## 创建 Skill

使用 `skill-creator` 技能。

## Rules

参考：https://cursor.directory/
