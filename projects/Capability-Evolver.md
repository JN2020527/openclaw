---
title: Capability Evolver 自我进化引擎
date: 2026-03-02
tags:
  - ai/evolution
  - gep
  - openclaw
  - meta-skill
aliases:
  - 进化系统
  - Evolver
source: https://github.com/autogame-17/evolver
version: 1.21.1
---

# Capability Evolver 自我进化引擎

> [!info] 定位
> GEP 协议约束的 [[自我进化]] 引擎。分析运行历史 → 识别改进点 → 按协议进化。

---

## 安装位置

```bash
/root/.openclaw/workspace/skills/capability-evolver/
```

---

## 工作流程

```
扫描日志 → 提取信号 → 选择 Gene → 生成 GEP 提示 → 执行代理应用修复 → 固化知识
```

---

## 核心概念

| 概念 | 作用 |
|------|------|
| **Gene（基因）** | 进化策略模板，定义「遇到什么信号 → 怎么处理」 |
| **Capsule（胶囊）** | 成功案例记录，避免重复推理 |
| **EvolutionEvent** | 进化事件日志，可追溯 |
| **GEP Protocol** | 协议约束输出格式 |
| **PersonalityState** | 人格状态（严谨度、创造力等） |

---

## 配置

| 配置项 | 值 | 说明 |
|--------|-----|------|
| `EVOLVE_STRATEGY` | `innovate` | 80% 创新，15% 优化，5% 修复 |
| `EVOLVE_LOAD_MAX` | `3.0` | 负载阈值 |
| `EVOLVE_ALLOW_SELF_MODIFY` | `false` | 禁止修改自身代码 |

---

## 定时任务

| 项目 | 值 |
|------|-----|
| **调度** | 每 6 小时 |
| **模式** | 自动执行（已去掉 --review） |
| **ID** | `9861b2a1-7cd1-4e7e-ac6e-a5961369de18` |

---

## 与 [[xiaoclaw-memory-三层架构]] 的关系

| 维度 | 说明 |
|------|------|
| `memory/*.md` | 只读，不修改 |
| 独立存储 | `memory/evolution/` |
| 关系 | 互补，不冲突 |

---

## 运行状态

| 指标 | 值 |
|------|-----|
| **已运行周期** | 8+ |
| **平均分数** | 0.85 |
| **文件修改** | 0（保守模式） |

---

## 相关链接

- [[OpenFang-Hands设计原则]]
- [[人格优化技能]]
- [GitHub 仓库](https://github.com/autogame-17/evolver)

---

#evolution #openclaw #meta
