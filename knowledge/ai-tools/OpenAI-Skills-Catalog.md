---
title: OpenAI 官方 Skills 仓库
date: 2026-03-05
tags:
  - openai
  - codex
  - skills
  - ai/agent
aliases:
  - openai/skills
  - Codex Skills
source: https://github.com/openai/skills
---

# OpenAI 官方 Skills 仓库

> [!info] 概述
> OpenAI 官方维护的 Codex 技能目录，包含系统技能、精选技能和实验性技能。

---

## 仓库信息

| 项目 | 值 |
|------|-----|
| **链接** | https://github.com/openai/skills |
| **描述** | Skills Catalog for Codex |
| **Stars** | ⭐ 10.8k |
| **Forks** | 603 |
| **更新** | 16 小时前（2026-03-04） |
| **语言** | Python (83.9%) |

---

## 技能分类

| 目录 | 说明 |
|------|------|
| `.system` | 系统技能（Codex 自动安装） |
| `.curated` | 精选技能（官方推荐） |
| `.experimental` | 实验性技能（可能不稳定） |

---

## 安装方式

### 安装精选技能

```bash
$skill-installer gh-address-comments
```

### 安装实验性技能

```bash
# 指定技能名称
$skill-installer install the create-plan skill from the .experimental folder

# 或使用 GitHub URL
$skill-installer install https://github.com/openai/skills/tree/main/skills/.experimental/create-plan
```

---

## 相关文档

| 资源 | 链接 |
|------|------|
| **使用技能** | https://developers.openai.com/codex/skills |
| **创建自定义技能** | https://developers.openai.com/codex/skills/create-skill |
| **Agent Skills 开放标准** | https://agentskills.io |

---

## 相关链接

- [[openai-codex]]
- [OpenAI Codex CLI](https://github.com/openai/codex)

---

#openai #codex #skills #ai
