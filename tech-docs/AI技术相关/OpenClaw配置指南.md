# OpenClaw 通过 Antigravity Tools 连接 API 配置指南

## 概述

本文记录了如何将 **OpenClaw**（本地 AI 助手平台）配置为通过 **Antigravity Tools** 反向代理
连接 Claude 模型的完整过程。

OpenClaw 是一个可在本地设备运行的 AI 助手，提供 Web UI（默认 `http://127.0.0.1:18789/chat`）、
WhatsApp 集成、定时任务等功能。

## 前置条件

| 工具 | 说明 |
|------|------|
| OpenClaw | `brew install openclaw` 或 `npm install -g openclaw`，已完成 `openclaw onboard` |
| Antigravity Tools | 已安装并启动 API 代理服务（端口 8045） |
| Antigravity API Key | 格式类似 `sk-dcb871a0cdc547fcac7d368d4a91af9e` |

## 核心问题

OpenClaw **不支持** `ANTHROPIC_BASE_URL` 环境变量来重定向 Anthropic API 请求。
即使在 `openclaw.json` 的 `env` 中设置了该变量，OpenClaw 内部的 SDK 也不会使用它，
API 调用会直接失败并返回 "Connection error."。

**解决方案**：使用 OpenClaw 的**自定义 Provider**（`models.providers`）功能，
将 Antigravity 注册为一个 OpenAI 兼容的自定义 provider。

## 配置步骤

### 1. 备份原始配置

```bash
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak.antigravity
```

### 2. 修改 `~/.openclaw/openclaw.json`

需要修改三个部分：`env`、`auth`、`agents.defaults`，并新增 `models` 部分。

#### 2.1 在 `env` 中添加 API Key 环境变量

```json
{
  "env": {
    "ALL_PROXY": "socks5h://127.0.0.1:7891",
    "NO_PROXY": "127.0.0.1,localhost,::1",
    "all_proxy": "socks5h://127.0.0.1:7891",
    "no_proxy": "127.0.0.1,localhost,::1",
    "NODE_OPTIONS": "--require /Users/macxuzhi/.openclaw/proxy-bootstrap.cjs",
    "ANTIGRAVITY_API_KEY": "sk-dcb871a0cdc547fcac7d368d4a91af9e"
  }
}
```

> **注意**：`NO_PROXY` 必须包含 `127.0.0.1`，确保对 Antigravity（`127.0.0.1:8045`）的请求
> 不经过外部代理。

#### 2.2 修改 `auth` 部分

```json
{
  "auth": {
    "profiles": {
      "antigravity:default": {
        "provider": "antigravity",
        "mode": "api_key"
      }
    }
  }
}
```

#### 2.3 修改 `agents.defaults` 中的模型配置

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "antigravity/claude-sonnet-4-5"
      },
      "models": {
        "antigravity/claude-sonnet-4-5": {
          "alias": "sonnet"
        }
      }
    }
  }
}
```

#### 2.4 新增 `models.providers` 自定义 Provider（关键步骤）

在 `openclaw.json` 中添加 `models` 顶层字段：

```json
{
  "models": {
    "providers": {
      "antigravity": {
        "baseUrl": "http://127.0.0.1:8045/v1",
        "apiKey": "${ANTIGRAVITY_API_KEY}",
        "api": "openai-completions",
        "models": [
          {
            "id": "claude-sonnet-4-5",
            "name": "Claude Sonnet 4.5 (Antigravity)",
            "reasoning": false,
            "input": ["text", "image"],
            "contextWindow": 195000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

**参数说明**：

| 参数 | 说明 |
|------|------|
| `baseUrl` | Antigravity 的 OpenAI 兼容端点（注意末尾 `/v1`） |
| `apiKey` | 引用 `env` 中定义的 `ANTIGRAVITY_API_KEY` |
| `api` | 使用 `openai-completions` 格式（OpenAI Chat Completions 兼容） |
| `models` | 注册可用的模型列表 |

### 3. 修改 Auth Profiles

编辑 `~/.openclaw/agents/main/agent/auth-profiles.json`：

```json
{
  "version": 1,
  "profiles": {
    "antigravity:default": {
      "type": "api_key",
      "provider": "antigravity",
      "key": "sk-dcb871a0cdc547fcac7d368d4a91af9e"
    }
  },
  "lastGood": {
    "antigravity": "antigravity:default"
  },
  "usageStats": {
    "antigravity:default": {
      "lastUsed": 0,
      "errorCount": 0
    }
  }
}
```

### 4. 重启 Gateway

```bash
openclaw gateway restart
```

### 5. 验证配置

```bash
# 检查模型状态
openclaw models status
```

预期输出应显示：
```
Default       : antigravity/claude-sonnet-4-5
...
- antigravity effective=profiles:... | antigravity:default=sk-dcb87...4a91af9e
```

### 6. 测试 API 调用

```bash
# 终端中需要绕过系统代理
http_proxy="" https_proxy="" all_proxy="" ALL_PROXY="" \
openclaw agent --session-id "test:verify" --message "say hello" --json
```

预期输出中应包含 `"status": "ok"` 和实际的文字回复。

## 使用方式

### Web UI（推荐）

直接在浏览器打开：

```
http://127.0.0.1:18789/chat
```

Web UI 不受终端代理影响，可以直接使用。

### 终端命令

```bash
http_proxy="" https_proxy="" all_proxy="" ALL_PROXY="" \
openclaw agent --session-id "agent:main:main" --message "你的消息"
```

## 踩坑记录

### 问题 1：设置 `ANTHROPIC_BASE_URL` 无效

**症状**：在 `openclaw.json` 的 `env` 中设置了 `ANTHROPIC_BASE_URL`，但 agent 返回
"Connection error."，usage 全为 0。

**根因**：OpenClaw（截至 2026.2.1 版本）不支持通过 `ANTHROPIC_BASE_URL` 重定向 Anthropic
API 请求。其内部的 SDK 初始化不读取该环境变量。

**解决**：改用 `models.providers` 自定义 provider 方式，将 Antigravity 注册为
OpenAI 兼容 provider。

### 问题 2：`openai/claude-sonnet-4-5` 模型未知

**症状**：`Error: Unknown model: openai/claude-sonnet-4-5`

**根因**：OpenClaw 的 OpenAI provider 只识别标准的 OpenAI 模型名（如 `gpt-5.1-codex`），
不认识 Claude 模型名。

**解决**：不能使用内置的 `openai` provider，需要创建自定义 provider `antigravity`，
并在 `models` 数组中显式注册 `claude-sonnet-4-5` 模型。

### 问题 3：CLI 命令显示 "Connection error."

**症状**：终端运行 `openclaw agent` 时显示 "Connection error."。

**根因**：终端的系统代理（Clash `http://127.0.0.1:7890`）拦截了 CLI 到 Gateway
（`127.0.0.1:18789`）的 WebSocket 连接。

**解决**：运行命令时手动清空代理变量：
```bash
http_proxy="" https_proxy="" all_proxy="" ALL_PROXY="" openclaw agent ...
```
或直接使用 Web UI（不受终端代理影响）。

## 完整配置文件参考

<details>
<summary>点击展开 ~/.openclaw/openclaw.json 完整配置</summary>

```json
{
  "meta": {
    "lastTouchedVersion": "2026.2.1",
    "lastTouchedAt": "2026-02-05T02:42:47.092Z"
  },
  "env": {
    "ALL_PROXY": "socks5h://127.0.0.1:7891",
    "NO_PROXY": "127.0.0.1,localhost,::1",
    "all_proxy": "socks5h://127.0.0.1:7891",
    "no_proxy": "127.0.0.1,localhost,::1",
    "NODE_OPTIONS": "--require /Users/macxuzhi/.openclaw/proxy-bootstrap.cjs",
    "ANTIGRAVITY_API_KEY": "sk-dcb871a0cdc547fcac7d368d4a91af9e"
  },
  "auth": {
    "profiles": {
      "antigravity:default": {
        "provider": "antigravity",
        "mode": "api_key"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "antigravity/claude-sonnet-4-5"
      },
      "models": {
        "antigravity/claude-sonnet-4-5": {
          "alias": "sonnet"
        }
      },
      "workspace": "/Users/macxuzhi/.openclaw/workspace",
      "compaction": {
        "mode": "safeguard"
      },
      "maxConcurrent": 4,
      "subagents": {
        "maxConcurrent": 8
      }
    }
  },
  "models": {
    "providers": {
      "antigravity": {
        "baseUrl": "http://127.0.0.1:8045/v1",
        "apiKey": "${ANTIGRAVITY_API_KEY}",
        "api": "openai-completions",
        "models": [
          {
            "id": "claude-sonnet-4-5",
            "name": "Claude Sonnet 4.5 (Antigravity)",
            "reasoning": false,
            "input": ["text", "image"],
            "contextWindow": 195000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback"
  }
}
```

</details>

## 架构图

```
┌──────────────────────────────────────────────────────────────┐
│  浏览器 / 终端                                                 │
│  ┌────────────────────┐                                      │
│  │ Web UI (chat页面)   │ ◄── http://127.0.0.1:18789/chat     │
│  │ 或 openclaw agent   │                                     │
│  └─────────┬──────────┘                                      │
│            │ WebSocket                                       │
│            ▼                                                 │
│  ┌────────────────────┐                                      │
│  │  OpenClaw Gateway   │  (LaunchAgent 守护进程)               │
│  │  127.0.0.1:18789   │                                      │
│  └─────────┬──────────┘                                      │
│            │ HTTP (OpenAI Chat Completions 格式)              │
│            ▼                                                 │
│  ┌────────────────────┐                                      │
│  │ Antigravity Tools  │  (自定义 Provider: antigravity)       │
│  │  127.0.0.1:8045    │                                      │
│  └─────────┬──────────┘                                      │
│            │                                                 │
└────────────┼─────────────────────────────────────────────────┘
             │
             ▼
  ┌─────────────────────┐
  │  Google / Anthropic  │
  │    Cloud Services    │
  └─────────────────────┘
```

## 更新日期

2026-02-07
