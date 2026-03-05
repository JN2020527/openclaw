# MyClaw Backup 使用指南

> 本文档介绍如何使用 myclaw-backup 技能备份和恢复 OpenClaw 实例的完整数据。

---

## 目录

- [1. 是什么](#1-是什么)
  - [1.1 概念与定位](#11-概念与定位)
  - [1.2 核心功能](#12-核心功能)
  - [1.3 备份内容](#13-备份内容)
- [2. 为什么](#2-为什么)
  - [2.1 价值](#21-价值)
  - [2.2 使用场景](#22-使用场景)
  - [2.3 解决的问题](#23-解决的问题)
- [3. 怎么用](#3-怎么用)
  - [3.1 依赖检查](#31-依赖检查)
  - [3.2 创建备份](#32-创建备份)
  - [3.3 恢复备份](#33-恢复备份)
  - [3.4 HTTP 服务器管理](#34-http-服务器管理)
  - [3.5 迁移到新服务器](#35-迁移到新服务器)
  - [3.6 定时自动备份](#36-定时自动备份)
- [4. 安全注意事项](#4-安全注意事项)
- [5. 常见问题](#5-常见问题)

---

## 1. 是什么

### 1.1 概念与定位

**myclaw-backup** 是 [MyClaw.ai](https://myclaw.ai) 开放技能生态的一部分，专门用于 OpenClaw AI 助手平台的**完整数据备份与恢复**。

它将 OpenClaw 实例的所有关键数据打包成单个 `.tar.gz` 归档文件，并可在任意 OpenClaw 实例上恢复。内置 HTTP 服务器支持浏览器端管理，无需云存储即可完成下载/上传/恢复操作。

### 1.2 核心功能

| 功能 | 说明 |
|------|------|
| **完整备份** | 将 `~/.openclaw/` 目录打包为加密归档 |
| **一键恢复** | 从归档文件恢复所有配置和数据 |
| **HTTP 服务** | 提供浏览器界面进行备份管理 |
| **定时备份** | 通过系统 cron 实现自动周期备份 |
| **服务器迁移** | 支持跨服务器完整迁移，无需重新配对 |

### 1.3 备份内容

**包含：**
- 工作区文件（MEMORY.md、技能、代理文件）
- `openclaw.json`（机器人令牌 + API 密钥）
- 凭证文件
- 频道配对状态
- 代理配置与会话历史
- 设备信息
- 身份标识
- 定时任务
- Guardian 脚本

**排除：**
- 日志文件
- 二进制媒体
- `node_modules`
- Canvas 系统文件

---

## 2. 为什么

### 2.1 价值

1. **数据安全**：防止意外删除、硬件故障、配置错误导致的数据丢失
2. **快速恢复**：几分钟内恢复完整工作环境，无需重新配置
3. **无缝迁移**：更换服务器时保持所有频道连接，无需重新配对
4. **版本控制**：保留多个时间点的备份，可回滚到任意状态

### 2.2 使用场景

| 场景 | 说明 |
|------|------|
| **灾难恢复** | 服务器崩溃后快速恢复 |
| **服务器升级** | 迁移到更高配置的机器 |
| **环境复制** | 在新实例上复刻现有配置 |
| **定期保护** | 每日/每周自动备份 |
| **变更前快照** | 重大修改前创建安全点 |

### 2.3 解决的问题

- **配置复杂**：OpenClaw 包含大量配置文件、令牌、凭证，手动备份容易遗漏
- **频道重连困难**：迁移后通常需要重新配对所有频道（Discord、Telegram 等）
- **凭证管理混乱**：API 密钥散落在多个文件中，难以统一备份
- **缺乏可视化工具**：命令行操作对非技术用户不友好

---

## 3. 怎么用

### 3.1 依赖检查

使用前确保已安装以下工具（OpenClaw 实例通常已预装）：

```bash
# 检查依赖
which node rsync tar python3 openclaw
```

### 3.2 创建备份

**基本用法：**

```bash
# 创建备份到默认目录 /tmp/openclaw-backups/
bash scripts/backup.sh

# 指定输出目录
bash scripts/backup.sh /path/to/backup/dir
```

**输出示例：**

```
/tmp/openclaw-backups/openclaw-backup_20240315_143022.tar.gz (chmod 600)
```

> **注意**：备份文件权限为 `600`（仅所有者可读写），包含敏感凭证，请妥善保管。

### 3.3 恢复备份

> ⚠️ **重要**：恢复前**必须**先执行 `--dry-run` 预览变更！

**步骤 1：预览变更（安全检查）**

```bash
bash scripts/restore.sh openclaw-backup_20240315_143022.tar.gz --dry-run
```

**步骤 2：确认后执行恢复**

```bash
bash scripts/restore.sh openclaw-backup_20240315_143022.tar.gz
```

**恢复行为说明（v1.6+）：**
- 默认保留新服务器的 `gateway.auth.token`，避免迁移后出现 "gateway token mismatch" 错误
- 如需完整灾难恢复（同服务器），使用 `--overwrite-gateway-token` 参数

```bash
# 完整灾难恢复（覆盖 gateway token）
bash scripts/restore.sh openclaw-backup_20240315_143022.tar.gz --overwrite-gateway-token
```

**恢复前自动快照：**
- 恢复脚本会先创建当前状态的快照，防止意外覆盖

### 3.4 HTTP 服务器管理

HTTP 服务器提供浏览器界面，支持：
- 创建备份
- 下载 `.tar.gz` 文件
- 上传备份文件
- 预览恢复（dry-run）
- 执行恢复

**启动服务器：**

```bash
# 生成随机令牌并启动
bash scripts/serve.sh start --token $(openssl rand -hex 16) --port 7373

# 输出示例：
# http://localhost:7373/?token=<generated-token>
```

> ⚠️ **安全警告**：
> - 令牌是**必需的**，服务器拒绝无令牌启动
> - 切勿在公共网络上分享 URL，除非配置了反向代理 + TLS
> - 远程访问只能下载/上传文件，无法触发执行操作

**停止服务器：**

```bash
bash scripts/serve.sh stop
```

**查看服务器状态：**

```bash
bash scripts/serve.sh status
```

**HTTP API 端点：**

| 端点 | 方法 | 远程访问 | 说明 |
|------|------|----------|------|
| `/health` | GET | ✅ 无需令牌 | 健康检查 |
| `/backups` | GET | ✅ 需要令牌 | 列出备份文件 |
| `/download/:filename` | GET | ✅ 需要令牌 | 下载归档 |
| `/upload` | POST | ✅ 需要令牌 | 上传归档（字段名：`backup`） |
| `/backup` | POST | ❌ 仅本地 | 创建备份 |
| `/restore/:filename` | POST | ❌ 仅本地 | 恢复；加 `?dry_run=1` 预览 |

### 3.5 迁移到新服务器

**旧服务器操作：**

```bash
# 1. 启动 HTTP 服务器
bash scripts/serve.sh start --token MYTOKEN --port 7373
```

**新服务器操作：**

```bash
# 1. 安装 OpenClaw（如未安装）

# 2. 下载备份文件
curl -O "http://OLD_IP:7373/download/openclaw-backup_20240315_143022.tar.gz?token=MYTOKEN"

# 3. 预览变更
bash scripts/restore.sh openclaw-backup_20240315_143022.tar.gz --dry-run

# 4. 执行恢复
bash scripts/restore.sh openclaw-backup_20240315_143022.tar.gz

# 所有频道自动重连，无需重新配对！
```

### 3.6 定时自动备份

**使用 OpenClaw cron 配置每日凌晨 3 点自动备份：**

```json
{
  "name": "daily-openclaw-backup",
  "schedule": { "kind": "cron", "expr": "0 3 * * *", "tz": "UTC" },
  "payload": {
    "kind": "agentTurn",
    "message": "Run a backup using the myclaw-backup skill. Output dir: /tmp/openclaw-backups",
    "timeoutSeconds": 120
  },
  "sessionTarget": "isolated"
}
```

**使用系统 cron（替代方案）：**

```bash
# 每日备份
bash scripts/schedule.sh --interval daily

# 每周备份
bash scripts/schedule.sh --interval weekly

# 每小时备份
bash scripts/schedule.sh --interval hourly

# 禁用定时备份
bash scripts/schedule.sh --disable
```

---

## 4. 安全注意事项

### 4.1 信任边界

此技能处理**高度敏感数据**：
- 机器人令牌（Discord、Telegram、Slack 等）
- API 密钥（OpenAI、Anthropic、ElevenLabs 等）
- 频道凭证
- 会话历史

### 4.2 访问控制

| 端点 | 远程访问（需令牌） | 仅本地 |
|------|-------------------|--------|
| `GET /health` | ✅ 无需令牌 | — |
| `GET /backups` | ✅ | — |
| `GET /download/:file` | ✅ | — |
| `POST /upload` | ✅ | — |
| `POST /backup` | ❌ | ✅ |
| `POST /restore` | ❌ | ✅ |

### 4.3 最佳实践

1. **永远不要**在没有 `--token` 的情况下启动 HTTP 服务器
2. **永远不要**在公共网络上暴露 HTTP 服务器（除非配置 TLS）
3. **始终**在恢复前执行 `--dry-run`
4. **妥善保管**备份归档 — 它们包含所有凭证
5. **定期验证**备份文件的完整性

### 4.4 脚本行为说明

| 脚本 | 网络访问 | 说明 |
|------|----------|------|
| `backup.sh` | ❌ 无 | 读取 `~/.openclaw/`，写入 `chmod 600` 归档 |
| `restore.sh` | ❌ 无 | 覆盖 `~/.openclaw/`，需输入 `yes` 确认 |
| `serve.sh` / `server.js` | ✅ 有 | 启动本地 HTTP 服务器，令牌必需 |
| `schedule.sh` | ❌ 无 | 修改系统 crontab |

---

## 5. 常见问题

### Q1: 恢复后显示 "gateway token mismatch" 错误？

**原因**：Gateway token 与备份中的不匹配。

**解决方案**：默认情况下 `restore.sh` 会保留新服务器的 token。如果仍有问题，检查 Control UI / Dashboard 配置。

### Q2: 备份文件太大怎么办？

**可能原因**：包含了大量媒体文件或日志。

**解决方案**：备份脚本已自动排除日志和媒体，如果仍然过大，检查 `~/.openclaw/` 目录内容。

### Q3: 如何只备份特定内容？

当前版本暂不支持选择性备份。如需此功能，可手动编辑 `scripts/backup.sh` 中的排除规则。

### Q4: HTTP 服务器无法访问？

**检查清单**：
1. 确认服务器已启动：`bash scripts/serve.sh status`
2. 确认防火墙允许指定端口
3. 确认 URL 包含正确的令牌参数

### Q5: 迁移后频道需要重新配对吗？

**不需要**。恢复后所有频道自动重连，包括 Discord、Telegram、Slack 等。

---

## 附录：脚本速查表

| 命令 | 说明 |
|------|------|
| `bash scripts/backup.sh [output-dir]` | 创建备份 |
| `bash scripts/restore.sh <archive> --dry-run` | 预览恢复 |
| `bash scripts/restore.sh <archive>` | 执行恢复 |
| `bash scripts/serve.sh start --token TOKEN [--port 7373]` | 启动 HTTP 服务器 |
| `bash scripts/serve.sh stop` | 停止服务器 |
| `bash scripts/serve.sh status` | 查看服务器状态 |
| `bash scripts/schedule.sh --interval daily` | 设置每日备份 |
| `bash scripts/schedule.sh --disable` | 禁用定时备份 |

---

> 📚 **延伸阅读**：详细备份内容列表见 `references/what-gets-saved.md`
