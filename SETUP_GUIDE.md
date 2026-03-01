# 详细配置指南

本文档详细介绍如何配置和定制你的 OpenClaw 环境。

## 目录

1. [本地部署](#本地部署)
2. [安装 Skills](#安装-skills)
3. [配置 Agent Reach](#配置-agent-reach)
4. [模型配置](#模型配置)
5. [网络代理](#网络代理)
6. [定时任务](#定时任务)
7. [Token 成本优化](#token-成本优化)
8. [安全加固](#安全加固)

---

## 本地部署

### 环境要求
- macOS / Linux / Windows (WSL2)
- Node.js >= 22.0.0
- 2GB+ 内存
- 10GB+ 磁盘空间

### 安装步骤

```bash
# 方式一：一键脚本（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方式二：npm 全局安装
npm install -g openclaw@latest

# 运行配置向导
openclaw onboard --install-daemon
```

配置向导会引导你完成：
- 选择 AI 模型提供商（Claude、OpenAI、Gemini 等）
- 配置 API Key
- 选择消息渠道（Telegram、飞书等）

### 验证安装

```bash
# 检查 Gateway 状态
openclaw gateway status

# 启动 Gateway
openclaw gateway start

# 打开控制面板
openclaw dashboard

# 健康检查
openclaw doctor
```

### 故障排除

```bash
# Gateway 无法启动
openclaw gateway logs
openclaw gateway restart

# 检查端口占用
lsof -i :18789
```

---

## 安装 Skills

Skills 可以通过两种方式安装：

### 方式一：ClawHub（推荐，但近期有限制）

```bash
# 列出可用 Skills
clawhub list

# 安装 Skill
clawhub install skill-name

# 更新 Skill
clawhub update skill-name
```

> ⚠️ **注意**：近期 ClawHub 有 rate limit 限制，如果遇到安装失败，请使用手动安装方式。

### 方式二：手动安装（git clone）

如果 ClawHub 安装失败，可以通过 git clone 手动安装到 `~/.openclaw/workspace/skills/` 目录：

### 核心 Skills

```bash
# 创建 skills 目录
mkdir -p ~/.openclaw/workspace/skills

# Agent Reach - 让 AI 能够访问互联网（Twitter/X、Reddit、YouTube、GitHub、Bilibili、小红书、抖音等）
git clone https://github.com/Panniantong/agent-reach.git \
  ~/.openclaw/workspace/skills/agent-reach

# 持续改进系统
git clone https://github.com/pskoett/self-improving-agent.git \
  ~/.openclaw/workspace/skills/self-improving-agent

# 主动式 Agent 架构
git clone https://github.com/halthelobster/proactive-agent.git \
  ~/.openclaw/workspace/skills/proactive-agent
```

### 其他推荐 Skills

```bash
# AI 图片生成（基于 Gemini）
git clone https://github.com/openclaw/nano-banana-pro.git \
  ~/.openclaw/workspace/skills/nano-banana-pro

# PDF/文档转 Markdown
git clone https://github.com/openclaw/markdown-converter.git \
  ~/.openclaw/workspace/skills/markdown-converter

# 手绘风格流程图
git clone https://github.com/openclaw/excalidraw.git \
  ~/.openclaw/workspace/skills/excalidraw

# 去除 AI 文本痕迹
git clone https://github.com/openclaw/de-ai-ify.git \
  ~/.openclaw/workspace/skills/de-ai-ify

# 中文人性化处理
git clone https://github.com/openclaw/humanizer-zh.git \
  ~/.openclaw/workspace/skills/humanizer-zh

# 快速提醒
git clone https://github.com/openclaw/quick-reminders.git \
  ~/.openclaw/workspace/skills/quick-reminders
```

---

## 配置 Agent Reach

Agent Reach 是让 OpenClaw 拥有"眼睛"的关键技能，可以直接访问互联网上的各大平台。

### 快速配置

复制这句话给你的 AI Agent：

```
帮我安装 Agent Reach：https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

Agent 会自己完成剩下的所有事情。

### 安全模式

🛡️ 担心安全？使用安全模式——不会自动装系统包，只告诉你需要什么：

```
帮我安装 Agent Reach（安全模式）：https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

安装时使用 `--safe` 参数

### 手动安装（备选）

如果上述方式无法使用，可以手动安装：

```bash
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
agent-reach install --env=auto
agent-reach doctor
```

### 配置代理

```bash
agent-reach configure proxy http://127.0.0.1:7890
```

### Cookie 导入

所有需要登录的平台（Twitter、小红书等）：

1. 在浏览器登录对应平台
2. 安装 [Cookie-Editor](https://chromewebstore.google.com/detail/cookie-editor/hlkenndednhfkekhgcdicdfddnkalmdm) Chrome 插件
3. 点击插件 → Export → **Header String**
4. 把导出的字符串发给 Agent

或使用命令行自动提取：
```bash
agent-reach configure --from-browser chrome
```

### 各平台使用示例

安装完成后，直接调用上游工具：

```bash
# Twitter/X
xreach search "query" --json -n 10

# YouTube
yt-dlp --dump-json "https://www.youtube.com/watch?v=xxx"

# Bilibili
yt-dlp --dump-json "https://www.bilibili.com/video/BVxxx"

# Reddit
curl -s "https://www.reddit.com/r/python/hot.json?limit=10" \
  -H "User-Agent: agent-reach/1.0"

# 小红书
mcporter call 'xiaohongshu.search_feeds(keyword: "query")'

# 抖音
mcporter call 'douyin.parse_douyin_video_info(share_link: "https://v.douyin.com/xxx/")'

# GitHub
gh search repos "query" --sort stars --limit 10

# 任意网页
curl -s "https://r.jina.ai/URL" -H "Accept: text/markdown"
```

---

## 模型配置

### 编辑 Gateway 配置

配置文件位置：`~/.openclaw/openclaw.json`

### 推荐配置

```json
{
  "models": {
    "default": "kimi-coding/k2p5",
    "aliases": {
      "complex": "custom-www-right-codes/claude-opus-4-6",
      "fast": "openai-codex/gpt-5.2",
      "cheap": "minimax/m20"
    }
  },
  "fallback": {
    "enabled": true,
    "chain": [
      "custom-www-right-codes/claude-opus-4-6",
      "openai-codex/gpt-5.3-codex",
      "google-gemini-cli/gemini-3-pro-preview"
    ],
    "backoff": {
      "initial": "1m",
      "max": "1h",
      "multiplier": 5
    }
  }
}
```

### 模型选择建议

| 场景 | 推荐模型 | 理由 |
|------|---------|------|
| 复杂编程任务 | Claude Opus 4.6 | Agentic 能力最强 |
| 一般对话 | GPT-5.2 | 速度快，额度多 |
| 成本敏感 | Gemini Flash | 价格便宜 |
| 中文任务 | Kimi K2.5 | 中文理解好 |

---

## 网络代理

### 中国大陆用户必备

在你的 `~/.zshrc` 或 `~/.bashrc` 中添加：

```bash
# 通用代理（适用于大部分工具）
export ALL_PROXY=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export http_proxy=http://127.0.0.1:7890
export all_proxy=socks5://127.0.0.1:7890

# 根据你的代理软件调整端口
# Clash 默认: 7890
# Surge 默认: 6152
```

### 特殊工具注意

| 工具 | 代理变量 | 说明 |
|------|---------|------|
| Node.js/Python | `ALL_PROXY` | 通用 |
| Go 工具 | `HTTP_PROXY` + `HTTPS_PROXY` | Go 不认 `ALL_PROXY`！ |
| yt-dlp | `ALL_PROXY` | 支持 |
| curl/wget | `https_proxy` | 标准 |

### 测试代理

```bash
# 测试是否生效
curl -I https://www.google.com
```

---

## 定时任务

### 使用 Cron

```bash
# 列出所有任务
openclaw cron list

# 添加每日任务
openclaw cron add --name "morning-brief" \
  --schedule "0 8 * * *" \
  --command "morning-briefing"
```

### 推荐任务配置

| 任务 | Cron 表达式 | 说明 |
|------|------------|------|
| nightly-build | `0 3 * * *` | 凌晨 3 点系统维护 |
| morning-brief | `0 8 * * *` | 早 8 点简报 |
| paper-digest | `0 9 * * *` | 早 9 点论文推送 |
| munger-observer | `0 20 * * *` | 晚 8 点芒格观察 |

---

## Token 成本优化

⚠️ **警告**：OpenClaw 的 Token 消耗可能远超预期！有用户报告一晚"待机"花费 18.75 美金。

### 成本构成

| 来源 | 占比 | 说明 |
|------|------|------|
| 上下文累积 | 40-50% | 会话历史无限增长 |
| 工具输出存储 | 20-30% | 大型 JSON/日志持久化 |
| 系统提示词 | 10-15% | 复杂提示词重复传输 |
| 心跳任务 | 5-10% | 后台进程配置不当 |

### 优化策略

#### 1. 会话重置（节省 40-60%）
```json
{
  "agent": {
    "sessionReset": "after-task",
    "maxContextTokens": 50000
  }
}
```

#### 2. 智能模型路由（节省 50-80%）
- 日常任务：Haiku 或 Gemini Flash
- 复杂推理：Sonnet/Opus

#### 3. 上下文窗口限制（节省 20-40%）
将默认 400K 缩减到 50-100K tokens。

### 综合效果

实测通过组合优化：
- **优化前**: 150 美金/月
- **优化后**: 35 美金/月
- **年节省**: 1,380 美金

---

## 安全加固

⚠️ **安全警告**：生产环境部署前务必执行安全加固。

### 快速安全检查

```bash
# 运行安全审计
openclaw security audit --deep

# 自动修复
openclaw security audit --fix
```

### 关键安全措施

#### 1. 启用认证
```json
{
  "gateway": {
    "auth": "token"
  }
}
```

#### 2. 配置 DM 策略
```json
{
  "channels": {
    "telegram": {
      "dmPolicy": "pairing"
    }
  }
}
```

策略选项：
- `pairing`（默认）: 未知发送者收到限时配对码
- `allowlist`: 完全阻止未知发送者
- `open`: 允许任何人（不推荐）
- `disabled`: 忽略所有入站 DM

#### 3. 文件系统隔离
```json
{
  "security": {
    "filesystem": {
      "workspaceOnly": true
    }
  }
}
```

#### 4. API 支出限制
在供应商侧（OpenAI、Anthropic 等）设置硬性支出限制。

### 已知安全风险

- **CVE-2026-25253**: WebSocket 劫持漏洞（已在 v2026.1.29 修复）
- **暴露网关**: 923 个实例以零认证模式暴露在公网
- **恶意扩展**: 假冒 "ClawdBot Agent" VS Code 扩展

**建议**：始终保持 OpenClaw 更新到最新版本。

---

## 更多资源

- [OpenClaw 文档](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [ClawHub](https://clawhub.com)
- [OpenClaw 安全最佳实践](https://docs.openclaw.ai/security)
