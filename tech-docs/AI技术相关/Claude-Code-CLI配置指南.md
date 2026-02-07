# Claude Code CLI 通过 Antigravity Tools 连接 API 配置指南

## 概述

本文记录了如何通过 **Antigravity Tools**（本地 API 反向代理）让 **Claude Code CLI** 连接到 Claude 模型的完整配置过程。

Antigravity Tools 将 Google/Anthropic 的 Web 会话转换为标准 API 端点，运行在 `http://127.0.0.1:8045`。

## 前置条件

| 工具 | 说明 |
|------|------|
| Antigravity Tools | 已安装并启动 API 代理服务（端口 8045） |
| Claude Code CLI | 已通过 `npm install -g @anthropic-ai/claude-code` 或其他方式安装 |
| macOS 终端 | zsh shell |

## 配置步骤

### 1. 获取 Antigravity API Key

Antigravity 启动后，API Key 可在以下位置找到：

```bash
cat ~/.antigravity_tools/gui_config.json | grep api_key
```

示例输出中的 `proxy.api_key` 字段即为所需的 Key，格式类似：`sk-dcb871a0cdc547fcac7d368d4a91af9e`。

### 2. 确认 Antigravity 代理端口可用

```bash
curl -s --noproxy '*' http://127.0.0.1:8045/v1/models \
  -H "x-api-key: <你的API_KEY>"
```

正常应返回可用模型列表的 JSON。

### 3. 查看可用模型

通过上一步的 `/v1/models` 端点查看可用模型列表，当前可用的 Claude 模型包括：

- `claude-sonnet-4-5` — Claude Sonnet 4.5（推荐，稳定可用）
- `claude-sonnet-4-5-thinking` — 带扩展思维链的 Sonnet 4.5
- `claude-haiku-4` — 轻量快速模型
- `claude-haiku-4-5-20251001` — Haiku 4.5

> **注意**：Opus 系列模型（如 `claude-opus-4`、`claude-opus-4-5-*`）已被 Anthropic 下线，
> 会返回 "Claude Opus 4.5 is no longer available" 的提示。

### 4. 配置 `~/.zshrc` 环境变量函数

由于 Claude Code CLI 和 Claude Desktop App 使用不同的认证方式（CLI 用 API Key，Desktop App 用 OAuth），
**不建议**将 Antigravity 配置写入 `~/.claude/settings.json`，否则会影响 Desktop App 的正常使用。

推荐方案：在 `~/.zshrc` 中创建开关函数，仅在终端中按需启用。

```bash
# ============ Antigravity Tools 配置 ============
antigravity_on() {
  export ANTHROPIC_API_KEY="sk-dcb871a0cdc547fcac7d368d4a91af9e"
  export ANTHROPIC_BASE_URL="http://127.0.0.1:8045"
  export ANTHROPIC_MODEL="claude-sonnet-4-5"
  export http_proxy=""
  export https_proxy=""
  export all_proxy=""
  echo "Antigravity 模式已开启 (claude-sonnet-4-5 via 127.0.0.1:8045)"
}

antigravity_off() {
  unset ANTHROPIC_API_KEY ANTHROPIC_BASE_URL ANTHROPIC_MODEL
  # 如果你有 proxy_on 函数，取消注释下一行以恢复代理
  # proxy_on
  echo "Antigravity 模式已关闭，恢复正常"
}
```

添加后执行：

```bash
source ~/.zshrc
```

### 5. 清除 OAuth 冲突（如有）

如果之前登录过 claude.ai，Keychain 中会残留 OAuth token，与 API Key 冲突。需要清除：

```bash
security delete-generic-password -s "Claude Safe Storage" 2>/dev/null
security delete-generic-password -s "Claude Key" 2>/dev/null
security delete-generic-password -s "Claude Code-credentials" 2>/dev/null
```

同时确保 `~/.claude/settings.json` 中**不包含** `ANTHROPIC_API_KEY` 和 `ANTHROPIC_BASE_URL`：

```json
{
  "includeCoAuthoredBy": false
}
```

### 6. 使用方式

```bash
# 开启 Antigravity 模式
antigravity_on

# 启动 Claude Code
claude

# 使用完毕后关闭（恢复系统代理等）
antigravity_off
```

## 踩坑记录

### 问题 1：502 无响应（Contemplating... 卡住）

**症状**：Claude Code 停在 "Contemplating..."，返回 `502 status code (no body)`。

**根因**：系统全局代理（如 Clash `http://127.0.0.1:7890`）拦截了发往本地 `127.0.0.1:8045` 的请求。

**解决**：在 `antigravity_on` 函数中将 `http_proxy`、`https_proxy`、`all_proxy` 设为空字符串，
确保本地请求不走外部代理。

### 问题 2：Auth conflict（API Key 与 OAuth 冲突）

**症状**：
```
⚠ Auth conflict: Both a token (claude.ai) and an API key (ANTHROPIC_API_KEY) are set.
```

**根因**：macOS Keychain 中残留了之前 claude.ai 登录的 OAuth token。

**解决**：删除 Keychain 中的 `Claude Safe Storage`、`Claude Key`、`Claude Code-credentials` 条目。

### 问题 3：模型不存在（404）

**症状**：返回 `404 Requested entity was not found`。

**根因**：请求的模型名称在 Antigravity 上不可用。

**解决**：通过 `/v1/models` 端点查询可用模型列表，使用正确的模型 ID。

### 问题 4：Claude Desktop App Cowork/Code 模式报 403

**症状**：Desktop App 的 Chat 模式正常，但 Code/Cowork 模式报 `Failed to authenticate. API Error: 403`。

**根因**：`~/.claude/settings.json` 中的 Antigravity 环境变量影响了 Desktop App 启动的子进程，
导致它用 Antigravity API Key 去请求 Anthropic 官方服务器。

**解决**：将 Antigravity 配置从 `~/.claude/settings.json` 移到 `~/.zshrc` 的函数中，
确保只影响手动开启的终端会话。

## 架构图

```
┌─────────────────────────────────────────────────────────┐
│  终端（zsh）                                              │
│  ┌──────────────┐    antigravity_on()                   │
│  │ Claude Code  │ ──── ANTHROPIC_API_KEY ────┐          │
│  │    CLI       │ ──── ANTHROPIC_BASE_URL ───┤          │
│  └──────────────┘                            │          │
│                                              ▼          │
│                                  ┌───────────────────┐  │
│                                  │ Antigravity Tools │  │
│                                  │  127.0.0.1:8045   │  │
│                                  └────────┬──────────┘  │
│                                           │             │
└───────────────────────────────────────────┼─────────────┘
                                            │
                                            ▼
                                ┌─────────────────────┐
                                │  Google / Anthropic  │
                                │    Cloud Services    │
                                └─────────────────────┘
```

## 更新日期

2026-02-07
