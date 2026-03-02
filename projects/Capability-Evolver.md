---
title: Capability Evolver 自我进化引擎
date: 2026-03-02
tags: [ai, self-evolution, gep, openclaw]
source: https://github.com/autogame-17/evolver
version: 1.21.1
---

# Capability Evolver 自我进化引擎

## 概述

Capability Evolver 是一个 GEP 协议约束的自我进化引擎，分析运行历史 → 识别改进点 → 按协议进化。

## 安装位置

```
/root/.openclaw/workspace/skills/capability-evolver/
```

## 工作流程

```
扫描日志 → 提取信号 → 选择 Gene → 生成 GEP 提示 → 执行代理应用修复 → 固化知识
```

## 核心概念

| 概念 | 作用 |
|------|------|
| **Gene（基因）** | 进化策略模板，定义「遇到什么信号 → 怎么处理」 |
| **Capsule（胶囊）** | 成功案例记录，避免重复推理 |
| **EvolutionEvent** | 进化事件日志，可追溯 |
| **GEP Protocol** | 协议约束输出格式 |
| **PersonalityState** | 人格状态（严谨度、创造力等） |

## 定时任务

- **调度**：每 6 小时
- **模式**：`--review`（需确认才修改）
- **ID**：`a918e4c2-4cf2-48b8-bbd2-6e9c4b03cbc2`

## 与记忆系统的关系

| 维度 | 说明 |
|------|------|
| 我们的 memory/*.md | 只读，不修改 |
| 独立存储 | `memory/evolution/` |
| 关系 | 互补，不冲突 |

## 首次运行结果

- 识别信号：Rate limit exceeded, user_feature_request
- 选择 Gene：`gene_gep_repair_from_errors`
- 修改文件：0（问题在外部，无需本地修复）
- 状态：成功

## 相关链接

- [[OpenFang-Hands设计原则]]
- [GitHub 仓库](https://github.com/autogame-17/evolver)
