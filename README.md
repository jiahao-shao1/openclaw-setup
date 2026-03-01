# OpenClaw 个性化配置

> 一套完整的 OpenClaw 配置模板，包含 Agent 工作流、身份定义和定时任务

## 简介

这是我在使用的 OpenClaw 配置集合，整理成模板方便大家 fork 和定制。

## 快速开始

### 1. Fork 本仓库

```bash
git clone https://github.com/YOUR_USERNAME/openclaw-setup.git
cd openclaw-setup
```

### 2. 复制配置文件

```bash
# 创建 OpenClaw workspace（如果不存在）
mkdir -p ~/.openclaw/workspace/skills

# 复制公开配置
cp AGENTS.md SOUL.md HEARTBEAT.md ~/.openclaw/workspace/

# 创建私密文件（从示例复制）
cp examples/USER.md.example ~/.openclaw/workspace/USER.md
cp examples/TOOLS.md.example ~/.openclaw/workspace/TOOLS.md
```

### 3. 填写个人信息

编辑以下文件，填入你的信息：

**`~/.openclaw/workspace/USER.md`** - 你的个人信息：
- 姓名、邮箱
- Telegram 群组 ID
- 个人目标与兴趣

**`~/.openclaw/workspace/TOOLS.md`** - 工具配置：
- API Keys（Gemini、OpenAI 等）
- 代理设置
- 设备信息

## 文件说明

| 文件 | 用途 | 隐私级别 |
|------|------|---------|
| `AGENTS.md` | Agent 工作流与协作规则 | 🟢 公开 |
| `SOUL.md` | Agent 身份、个性与行为准则 | 🟢 公开 |
| `HEARTBEAT.md` | 定时任务清单 | 🟢 公开 |
| `USER.md` | 用户个人信息 | 🔴 私密 |
| `TOOLS.md` | API Keys 与工具配置 | 🔴 私密 |

## 本地部署

### 环境要求
- macOS / Linux / Windows (WSL2)
- Node.js >= 22.0.0
- 2GB+ 内存

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

# 健康检查
openclaw doctor
```

## 安装 Skills

Skills 可以通过两种方式安装：

### 方式一：ClawHub（推荐，但近期有限制）

```bash
# 列出可用 Skills
clawhub list

# 安装 Skill
clawhub install skill-name
```

> ⚠️ **注意**：近期 ClawHub 有 rate limit 限制，如果遇到安装失败，请使用手动安装方式。

### 方式二：手动安装（git clone）

如果 ClawHub 安装失败，可以通过 git clone 手动安装：

| Skill | 功能 | ClawHub 命令 | 手动安装链接 |
|-------|------|-------------|-------------|
| **agent-reach** ⭐ | 访问互联网（Twitter/X、YouTube、Bilibili、小红书、抖音等） | `clawhub install agent-reach` | [Panniantong/agent-reach](https://github.com/Panniantong/agent-reach) |
| **self-improving-agent** | 持续改进系统，记录学习/错误/修正 | `clawhub install self-improving-agent` | [pskoett/self-improving-agent](https://github.com/pskoett/self-improving-agent) |
| **proactive-agent** | 主动式 Agent 架构 | `clawhub install proactive-agent` | [halthelobster/proactive-agent](https://github.com/halthelobster/proactive-agent) |
| **nano-banana-pro** | AI 图片生成（基于 Gemini） | `clawhub install nano-banana-pro` | [openclaw/nano-banana-pro](https://github.com/openclaw/nano-banana-pro) |
| **markdown-converter** | PDF/文档转 Markdown | `clawhub install markdown-converter` | [openclaw/markdown-converter](https://github.com/openclaw/markdown-converter) |
| **excalidraw** | 手绘风格流程图 | `clawhub install excalidraw` | [openclaw/excalidraw](https://github.com/openclaw/excalidraw) |
| **de-ai-ify** | 去除 AI 文本痕迹 | `clawhub install de-ai-ify` | [openclaw/de-ai-ify](https://github.com/openclaw/de-ai-ify) |
| **humanizer-zh** | 中文人性化处理 | `clawhub install humanizer-zh` | [openclaw/humanizer-zh](https://github.com/openclaw/humanizer-zh) |
| **quick-reminders** | 快速提醒 | `clawhub install quick-reminders` | [openclaw/quick-reminders](https://github.com/openclaw/quick-reminders) |
| **github** | GitHub 操作（`gh` CLI，支持 issue/pr/run/api） | `clawhub install steipete/github` | [steipete/clawdhub](https://github.com/openclaw/skills/tree/main/skills/steipete/clawdhub) |

### 手动安装命令示例

```bash
# 创建 skills 目录
mkdir -p ~/.openclaw/workspace/skills

# 安装核心 Skills（推荐全部安装）
git clone https://github.com/Panniantong/agent-reach.git ~/.openclaw/workspace/skills/agent-reach
git clone https://github.com/pskoett/self-improving-agent.git ~/.openclaw/workspace/skills/self-improving-agent
git clone https://github.com/halthelobster/proactive-agent.git ~/.openclaw/workspace/skills/proactive-agent

# 安装其他 Skills（按需选择）
git clone https://github.com/openclaw/nano-banana-pro.git ~/.openclaw/workspace/skills/nano-banana-pro
git clone https://github.com/openclaw/markdown-converter.git ~/.openclaw/workspace/skills/markdown-converter
```

## 配置 Agent Reach ⭐

**Agent Reach** 是让 OpenClaw 拥有"眼睛"的关键技能，可以直接访问互联网上的各大平台。

### 快速配置

复制这句话给你的 AI Agent（Claude Code、OpenClaw、Cursor 等）：

```
帮我安装 Agent Reach：https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

就这一步。Agent 会自己完成剩下的所有事情。

### 安全模式

🛡️ 担心安全？使用安全模式——不会自动装系统包，只告诉你需要什么：

```
帮我安装 Agent Reach（安全模式）：https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

安装时使用 `--safe` 参数

## 核心特性

### 1. 模型策略
- **复杂任务**: Claude Opus 4.6（Agentic 最强）
- **一般任务**: GPT-5.2（额度多）
- **Fallback 链**: 三层容灾 + 指数退避

### 2. 网络配置
针对中国大陆优化：
- ALL_PROXY 统一代理设置
- Go 工具特殊处理（HTTP_PROXY）
- Skills 自动使用代理

### 3. 安全加固
- 文件系统隔离（workspaceOnly）
- 高危工具限制（pairing 策略）
- 技能安装审查

### 4. 定时任务
- nightly-build: 凌晨 3 点系统维护
- morning-briefing: 早 8 点简报
- daily-paper-digest: 早 9 点论文推送
- munger-observer: 晚 8 点芒格观察

## Workspace 结构

```
~/.openclaw/workspace/
├── AGENTS.md              # ← 本仓库提供
├── SOUL.md                # ← 本仓库提供
├── HEARTBEAT.md           # ← 本仓库提供
├── USER.md                # ← 你自己创建（私密）
├── TOOLS.md               # ← 你自己创建（私密）
├── MEMORY.md              # ← 自动维护
├── memory/
│   ├── YYYY-MM-DD.md      # 每日日志
│   ├── topics/            # 主题知识库
│   ├── projects/          # 研究项目
│   └── reading-group/     # 阅读小组共享
└── skills/                # 本地 Skills
    ├── agent-reach/       # 互联网访问（关键）⭐
    ├── self-improving-agent/
    ├── proactive-agent/
    └── ...
```

## ⚠️ 重要提示

### Token 成本控制

OpenClaw 的 Token 消耗可能超出预期！优化建议：

- **会话重置**: 任务完成后重置，防止上下文膨胀（节省 40-60%）
- **模型路由**: 简单任务用 Haiku/Gemini Flash，复杂任务用 Sonnet/Opus（节省 50-80%）
- **上下文限制**: 将默认 400K 缩减到 50-100K tokens（节省 20-40%）

实测通过组合优化可从 **150 美金/月降至 35 美金/月**。

### 安全加固

生产环境务必执行：

```bash
# 运行安全审计
openclaw security audit --deep

# 自动修复
openclaw security audit --fix
```

关键安全措施：
- ✅ 启用 Token 认证（`auth: "token"`）
- ✅ 配置 DM 策略为 `pairing` 或 `allowlist`
- ✅ 定期运行安全审计
- ✅ 设置 API 支出限制（供应商侧）

## 隐私保护

本仓库**绝不含**任何私密信息：
- ❌ 真实姓名、邮箱
- ❌ Telegram 群组 ID
- ❌ API Keys
- ❌ 个人日程与目标

这些内容通过 `.gitignore` 排除，仅在本地 `USER.md` 和 `TOOLS.md` 中配置。

## 参考链接

- [OpenClaw 文档](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [ClawHub](https://clawhub.com)
- [Agent Skills 规范](https://agentskills.io/specification)

## License

MIT — 自由使用、修改和分发
