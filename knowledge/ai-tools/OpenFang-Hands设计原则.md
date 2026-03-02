---
title: OpenFang Hands 设计原则
date: 2026-03-02
tags: [ai, agent, skill-design, openfang]
source: https://github.com/RightNow-AI/openfang
---

# OpenFang Hands 设计原则

## 概述

OpenFang 是一个开源的 Agent 操作系统（Rust 实现），7 天斩获 7.5k stars。其 Hands 设计模式值得借鉴。

## 核心架构

| 层级 | 文件 | 作用 |
|------|------|------|
| Hands | `HAND.toml` + `SKILL.md` | 自主运行的代理，有调度、状态、仪表盘 |
| Skills | `SKILL.md` | 领域知识注入，被动调用 |

## 可借鉴的设计

### 1. Settings 可配置化

配置集中管理，用户可调参，不用改代码。

**我们的实现**：SKILL.md 头部配置表格

```markdown
## 配置

| 配置项 | 值 |
|--------|-----|
| feishu_app_token | xxx |
| stars_threshold | 100 |
```

### 2. System Prompt 分阶段

Phase 0/1/2/3... 每阶段有明确目标和停止条件。

**示例**：
- Phase 0 — 初始化
- Phase 1 — 数据获取
- Phase 2 — 生成摘要
- Phase 3 — 投递
- Phase 4 — 等待确认
- Phase 5 — 写入

### 3. 配置与逻辑分离

- 配置：声明式（表格/TOML）
- 逻辑：命令式（SKILL.md 流程）

**好处**：改配置不用改逻辑。

## 已应用

- `skills/github-trending/SKILL.md` (V3.0)
- `skills/github-watch/SKILL.md`

## 参考

- [OpenFang GitHub](https://github.com/RightNow-AI/openfang)
- [Capability Evolver](../projects/Capability-Evolver.md)
