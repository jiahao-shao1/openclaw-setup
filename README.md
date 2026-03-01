# OpenClaw 个性化配置

> **用 OpenClaw 替代所有电子劳动，自动化所有工作流**

## 核心理念

OpenClaw 不只是一个 AI 助手，而是一个**自动化操作系统**：

- 🤖 **替代重复劳动** — 让 AI 处理所有机械性的电子工作
- 🔄 **工作流自动化** — 从信息收集、处理到输出，全流程自动化
- 🧠 **记忆系统** — 持久化上下文，让 AI 真正"记住"你的偏好和历史
- 🛠️ **工具编排** — 通过 Skills 连接所有你需要的工具和服务

**这套配置的目标**：让你从琐碎的电子劳动中解放出来，专注于真正需要人类创造力的工作。

## 简介

这是我在使用的 OpenClaw 配置集合，整理成模板方便大家 fork 和定制。

## 快速开始

### 1. 安装 OpenClaw

```bash
# 方式一：一键脚本（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方式二：npm 全局安装
npm install -g openclaw@latest
```

### 2. 运行配置向导

```bash
openclaw onboard --install-daemon
```

配置向导会自动：
- 创建 workspace 目录和配置文件
- 引导你选择 AI 模型提供商（Claude、OpenAI、Gemini 等）
- 配置 API Key
- 选择消息渠道（Telegram、飞书等）
- 生成 AGENTS.md、SOUL.md、USER.md 等文件

### 3. Fork 本仓库（可选）

如果你想使用我的配置作为模板：

```bash
git clone https://github.com/YOUR_USERNAME/openclaw-setup.git
cd openclaw-setup

# 复制你需要的配置文件到 workspace
cp AGENTS.md SOUL.md HEARTBEAT.md ~/.openclaw/workspace/
```

### 4. 配置 Telegram

#### 1. 创建 Telegram Bot

1. 在 Telegram 中找到 [@BotFather](https://t.me/BotFather)
2. 发送 `/newbot` 创建新 bot
3. 按提示设置 bot 名称和 username
4. 获取 Bot Token（格式：`123456789:ABCdefGHIjklMNOpqrsTUVwxyz`）

#### 2. 配置 OpenClaw

编辑 `~/.openclaw/openclaw.json`：

```json
{
  "channels": {
    "telegram": {
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["+86138xxxxxxxx"],  // 你的手机号（可选）
      "groups": {
        "*": {
          "requireMention": true  // 群组中需要 @bot 才会响应
        }
      }
    }
  }
}
```

#### 3. 获取群组 ID

创建群组后，需要获取群组 ID 用于 cron 任务推送：

**方法 1：通过 OpenClaw 日志查看（最简单）**
```bash
# 在群组中 @bot 发送任意消息
# OpenClaw 会在日志中显示 chat_id
# 群组 ID 格式：telegram:-1001234567890（负数）
```

**方法 2：使用 Telegram Bot API**
```bash
# 1. 先在群组中发送一条消息（任意内容）
# 2. 然后调用 API
curl "https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates"

# 3. 在返回的 JSON 中找到 result[0].message.chat.id
# 群组 ID 是负数，格式：-1001234567890
```

**方法 3：通过 Telegram 桌面版（最快）**
```bash
# 1. 在群组中发送一条消息
# 2. 右键点击消息 → "Copy Message Link"
# 3. 链接格式：https://t.me/c/194xxxx987/11/13
# 4. 提取中间的数字（194xxxx987），加上 -100 前缀
# 5. 最终群组 ID：-100194xxxx987
```

#### 4. 群组权限配置

**推荐配置**：为不同类型的消息创建不同群组

```json
{
  "channels": {
    "telegram": {
      "groups": {
        "*": {
          "requireMention": true,  // 默认需要 @mention
          "allowFrom": ["+86138xxxxxxxx"]  // 只允许特定用户
        },
        "-1001234567890": {  // 特定群组 ID
          "requireMention": false,  // 该群组不需要 @mention
          "allowFrom": ["*"]  // 允许所有人
        }
      }
    }
  }
}
```

**群组分工示例**（参考我的配置）：

- **agent config** — Agent 配置相关（nightly-build、系统维护）
- **reading group** — 科研、论文、行业动态
- **self-improvement** — 自我提升、芒格观察

#### 5. 安全建议

- ✅ 设置 `allowFrom` 限制允许的用户
- ✅ 群组中启用 `requireMention: true`
- ✅ 定期检查 bot 的群组列表
- ✅ 不要在公开群组中使用敏感功能

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

如果 ClawHub 安装失败，可以通过 git clone 手动安装到 `~/.openclaw/skills/` 目录（Shared Skills，可被多个 OpenClaw 实例共享）：

| Skill | 功能 | ClawHub 命令 | 手动安装链接 |
|-------|------|-------------|-------------|
| **agent-reach** ⭐ | 访问互联网（Twitter/X、YouTube、Bilibili、小红书、抖音等） | `clawhub install agent-reach` | [Panniantong/agent-reach](https://github.com/Panniantong/agent-reach) |
| **self-improving-agent** | 持续改进系统，记录学习/错误/修正 | `clawhub install self-improving-agent` | [pskoett/self-improving-agent](https://github.com/pskoett/self-improving-agent) |
| **proactive-agent** | 主动式 Agent 架构 | `clawhub install proactive-agent` | [halthelobster/proactive-agent](https://github.com/halthelobster/proactive-agent) |
| **nano-banana-pro** | AI 图片生成（基于 Gemini） | `clawhub install nano-banana-pro` | [openclaw/nano-banana-pro](https://github.com/openclaw/nano-banana-pro) |
| **markdown-converter** | PDF/文档转 Markdown | `clawhub install markdown-converter` | [openclaw/markdown-converter](https://github.com/openclaw/markdown-converter) |
| **tavily-search** | 深度网络搜索（学术论文、技术内容） | `clawhub install tavily-search` | [openclaw/tavily-search](https://github.com/openclaw/tavily-search) |
| **munger-observer** | 芒格观察（每日思维模型复盘） | `clawhub install munger-observer` | [jiahao-shao1/openclaw-skill-munger-observer](https://github.com/jiahao-shao1/openclaw-skill-munger-observer) |
| **github** | GitHub 操作（`gh` CLI，支持 issue/pr/run/api） | `clawhub install steipete/github` | [steipete/clawdhub](https://github.com/openclaw/skills/tree/main/skills/steipete/clawdhub) |
| **notion-lifeos** ⭐ | Memory System for Human — Notion LifeOS PARA 系统管理 | `clawhub install notion-lifeos` | [jiahao-shao1/openclaw-skill-notion-lifeos](https://github.com/jiahao-shao1/openclaw-skill-notion-lifeos) |

### 手动安装命令示例

```bash
# 创建 shared skills 目录（推荐，可被多个实例共享）
mkdir -p ~/.openclaw/skills

# 安装核心 Skills（推荐全部安装）
git clone https://github.com/Panniantong/agent-reach.git ~/.openclaw/skills/agent-reach
git clone https://github.com/pskoett/self-improving-agent.git ~/.openclaw/skills/self-improving-agent
git clone https://github.com/halthelobster/proactive-agent.git ~/.openclaw/skills/proactive-agent

# 安装其他 Skills（按需选择）
git clone https://github.com/openclaw/nano-banana-pro.git ~/.openclaw/skills/nano-banana-pro
git clone https://github.com/openclaw/markdown-converter.git ~/.openclaw/skills/markdown-converter
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

### 1. 自动化工作流示例

这套配置展示了如何用 OpenClaw 替代日常电子劳动：

**信息收集自动化**
- 📰 每日 AI 行业新闻摘要（自动抓取、总结、推送）
- 📄 每日论文 Digest（arXiv 新论文自动筛选和解读）
- 🔔 社交媒体监控（Twitter/X、小红书、Bilibili 等）

**内容处理自动化**
- 📝 会议记录自动整理到 Notion
- 🗂️ 文件自动分类和归档
- 🔍 信息自动提取和结构化

**输出生成自动化**
- 📊 周报/月报自动生成
- 💬 消息自动回复（基于上下文）
- 📧 邮件草稿自动生成

**记忆系统自动化**
- 🧠 每日日志自动记录
- 📚 知识库自动更新
- 🔄 上下文自动维护

### 2. 模型策略

**智能路由，成本优化**
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

**让 AI 主动工作，而不是被动响应**

- nightly-build: 凌晨 3 点系统维护
- morning-briefing: 早 8 点简报
- daily-paper-digest: 早 9 点论文推送
- munger-observer: 晚 8 点芒格观察

**核心思想**：AI 应该像一个真正的助手，主动完成周期性工作，而不是等你来问。

## 自动化哲学

```
~/.openclaw/
├── workspace/
│   ├── AGENTS.md              # ← 本仓库提供
│   ├── SOUL.md                # ← 本仓库提供
│   ├── HEARTBEAT.md           # ← 本仓库提供
│   ├── USER.md                # ← 你自己创建（私密）
│   ├── TOOLS.md               # ← 你自己创建（私密）
│   ├── MEMORY.md              # ← 自动维护
│   └── memory/
│       ├── YYYY-MM-DD.md      # 每日日志
│       ├── topics/            # 主题知识库
│       ├── projects/          # 研究项目
│       └── reading-group/     # 阅读小组共享
└── skills/                    # ← Shared Skills（推荐）
    ├── agent-reach/           # 互联网访问（关键）⭐
    ├── self-improving-agent/
    ├── proactive-agent/
    └── ...
```

> **提示**：Skills 可以安装在两个位置：
> - `~/.openclaw/skills/` - **Shared Skills**（推荐），可被多个 OpenClaw 实例共享
> - `~/.openclaw/workspace/skills/` - **Workspace Skills**，仅当前实例可用，适合存放个人化配置

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
