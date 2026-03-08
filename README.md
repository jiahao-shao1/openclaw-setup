**English** | [中文](README.zh.md)

# OpenClaw Personal Configuration

> **Replace all digital labor with OpenClaw — automate every workflow**

## Core Philosophy

OpenClaw is not just an AI assistant — it's an **automation operating system**:

- 🤖 **Eliminate repetitive work** — Let AI handle all mechanical digital tasks
- 🔄 **Workflow automation** — End-to-end automation from data collection to processing to output
- 🧠 **Memory system** — Persistent context that lets AI truly "remember" your preferences and history
- 🛠️ **Tool orchestration** — Connect all the tools and services you need through Skills

**The goal of this configuration**: Free yourself from tedious digital labor and focus on work that truly requires human creativity.

## Overview

This is my personal OpenClaw configuration collection, organized as a template for easy forking and customization.

## Quick Start

### 1. Install OpenClaw

```bash
# Option 1: One-line install script (recommended)
curl -fsSL https://openclaw.ai/install.sh | bash

# Option 2: npm global install
npm install -g openclaw@latest
```

### 2. Run the Setup Wizard

```bash
openclaw onboard --install-daemon
```

The setup wizard will automatically:
- Create the workspace directory and configuration files
- Guide you through selecting an AI model provider (Claude, OpenAI, Gemini, etc.)
- Configure your API key
- Choose a messaging channel (Telegram, Lark/Feishu, etc.)
- Generate AGENTS.md, SOUL.md, USER.md, and other files

### 3. Fork This Repository (Optional)

If you want to use my configuration as a starting point:

```bash
git clone https://github.com/YOUR_USERNAME/openclaw-setup.git
cd openclaw-setup

# Copy the config files you need into your workspace
cp AGENTS.md SOUL.md HEARTBEAT.md ~/.openclaw/workspace/
```

### 4. Configure Telegram

#### 1. Create a Telegram Bot

1. Find [@BotFather](https://t.me/BotFather) on Telegram
2. Send `/newbot` to create a new bot
3. Follow the prompts to set the bot name and username
4. Get the Bot Token (format: `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`)

#### 2. Configure OpenClaw

Edit `~/.openclaw/openclaw.json`:

```json
{
  "channels": {
    "telegram": {
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["+86138xxxxxxxx"],  // Your phone number (optional)
      "groups": {
        "*": {
          "requireMention": true  // Require @bot mention in groups to respond
        }
      }
    }
  }
}
```

#### 3. Get Group IDs

After creating a group, you'll need the group ID for cron job notifications:

**Method 1: Check OpenClaw logs (easiest)**
```bash
# Send any message in the group while @mentioning the bot
# OpenClaw will display the chat_id in its logs
# Group ID format: telegram:-1001234567890 (negative number)
```

**Method 2: Use the Telegram Bot API**
```bash
# 1. Send a message in the group first (any content)
# 2. Then call the API
curl "https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates"

# 3. Find result[0].message.chat.id in the returned JSON
# Group IDs are negative numbers, format: -1001234567890
```

**Method 3: Via Telegram Desktop (fastest)**
```bash
# 1. Send a message in the group
# 2. Right-click the message → "Copy Message Link"
# 3. Link format: https://t.me/c/194xxxx987/11/13
# 4. Extract the middle number (194xxxx987), prepend -100
# 5. Final group ID: -100194xxxx987
```

#### 4. Group Permission Configuration

**Recommended**: Create separate groups for different types of messages

```json
{
  "channels": {
    "telegram": {
      "groups": {
        "*": {
          "requireMention": true,  // Require @mention by default
          "allowFrom": ["+86138xxxxxxxx"]  // Only allow specific users
        },
        "-1001234567890": {  // Specific group ID
          "requireMention": false,  // No @mention required in this group
          "allowFrom": ["*"]  // Allow everyone
        }
      }
    }
  }
}
```

**Example group setup** (based on my configuration):

- **agent config** — Agent configuration (nightly-build, system maintenance)
- **reading group** — Research, papers, industry news
- **self-improvement** — Personal growth, Munger observations

#### 5. Security Recommendations

- ✅ Set `allowFrom` to restrict allowed users
- ✅ Enable `requireMention: true` in groups
- ✅ Regularly review the bot's group memberships
- ✅ Avoid using sensitive features in public groups

### Verify Installation

```bash
# Check Gateway status
openclaw gateway status

# Start Gateway
openclaw gateway start

# Health check
openclaw doctor
```

## Installing Skills

Skills can be installed in two ways:

### Option 1: ClawHub (Recommended, but currently rate-limited)

```bash
# List available Skills
clawhub list

# Install a Skill
clawhub install skill-name
```

> ⚠️ **Note**: ClawHub is currently experiencing rate limits. If installation fails, use the manual installation method instead.

### Option 2: Manual Installation (git clone)

If ClawHub installation fails, you can manually install Skills via git clone into `~/.openclaw/skills/` (Shared Skills, accessible by multiple OpenClaw instances):

| Skill | Description | ClawHub Command | Manual Install |
|-------|-------------|-----------------|----------------|
| **agent-reach** ⭐ | Internet access (Twitter/X, YouTube, Bilibili, Xiaohongshu, Douyin, etc.) | `clawhub install agent-reach` | [Panniantong/agent-reach](https://github.com/Panniantong/agent-reach) |
| **self-improving-agent** | Continuous improvement system — logs learnings, errors, and corrections | `clawhub install self-improving-agent` | [pskoett/self-improving-agent](https://github.com/pskoett/self-improving-agent) |
| **proactive-agent** | Proactive agent architecture | `clawhub install proactive-agent` | [halthelobster/proactive-agent](https://github.com/halthelobster/proactive-agent) |
| **nano-banana-pro** | AI image generation (Gemini-based) | `clawhub install nano-banana-pro` | [openclaw/nano-banana-pro](https://github.com/openclaw/nano-banana-pro) |
| **markdown-converter** | Convert PDFs/documents to Markdown | `clawhub install markdown-converter` | [openclaw/markdown-converter](https://github.com/openclaw/markdown-converter) |
| **tavily-search** | Deep web search (academic papers, technical content) | `clawhub install tavily-search` | [openclaw/tavily-search](https://github.com/openclaw/tavily-search) |
| **munger-observer** | Munger Observer (daily mental model reviews) | `clawhub install munger-observer` | [jiahao-shao1/openclaw-skill-munger-observer](https://github.com/jiahao-shao1/openclaw-skill-munger-observer) |
| **github** | GitHub operations (`gh` CLI — issues, PRs, runs, API) | `clawhub install steipete/github` | [steipete/clawdhub](https://github.com/openclaw/skills/tree/main/skills/steipete/clawdhub) |
| **notion-lifeos** ⭐ | Memory System for Human — Notion LifeOS PARA system management | `clawhub install notion-lifeos` | [jiahao-shao1/openclaw-skill-notion-lifeos](https://github.com/jiahao-shao1/openclaw-skill-notion-lifeos) |

### Manual Installation Examples

```bash
# Create shared skills directory (recommended, shareable across instances)
mkdir -p ~/.openclaw/skills

# Install core Skills (recommended to install all)
git clone https://github.com/Panniantong/agent-reach.git ~/.openclaw/skills/agent-reach
git clone https://github.com/pskoett/self-improving-agent.git ~/.openclaw/skills/self-improving-agent
git clone https://github.com/halthelobster/proactive-agent.git ~/.openclaw/skills/proactive-agent

# Install additional Skills (as needed)
git clone https://github.com/openclaw/nano-banana-pro.git ~/.openclaw/skills/nano-banana-pro
git clone https://github.com/openclaw/markdown-converter.git ~/.openclaw/skills/markdown-converter
```

## Configuring Agent Reach ⭐

**Agent Reach** is the key skill that gives OpenClaw "eyes" — direct access to major internet platforms.

### Quick Setup

Copy this message to your AI Agent (Claude Code, OpenClaw, Cursor, etc.):

```
Help me install Agent Reach: https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

That's it. The agent will handle the rest.

### Safe Mode

🛡️ Concerned about security? Use safe mode — it won't automatically install system packages, just tells you what's needed:

```
Help me install Agent Reach (safe mode): https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

Use the `--safe` flag during installation.

## Key Features

### 1. Workflow Automation Examples

This configuration demonstrates how to use OpenClaw to replace everyday digital labor:

**Automated Information Gathering**
- 📰 Daily AI industry news digest (auto-fetch, summarize, and push)
- 📄 Daily paper digest (auto-filter and interpret new arXiv papers)
- 🔔 Social media monitoring (Twitter/X, Xiaohongshu, Bilibili, etc.)

**Automated Content Processing**
- 📝 Meeting notes auto-organized into Notion
- 🗂️ Automatic file categorization and archiving
- 🔍 Automatic information extraction and structuring

**Automated Output Generation**
- 📊 Auto-generated weekly/monthly reports
- 💬 Context-aware auto-replies
- 📧 Auto-generated email drafts

**Automated Memory System**
- 🧠 Auto-recorded daily logs
- 📚 Auto-updated knowledge base
- 🔄 Auto-maintained context

### 2. Model Strategy

**Smart routing for cost optimization**
- **Complex tasks**: Claude Opus 4.6 (strongest for agentic work)
- **General tasks**: GPT-5.2 (generous quota)
- **Fallback chain**: Three-tier failover with exponential backoff

### 2. Network Configuration
Optimized for mainland China:
- ALL_PROXY unified proxy settings
- Special handling for Go tools (HTTP_PROXY)
- Skills automatically use proxy

### 3. Security Hardening
- Filesystem isolation (workspaceOnly)
- Restricted high-risk tools (pairing policy)
- Skill installation review

### 4. Scheduled Tasks

**Let AI work proactively, not just reactively**

- nightly-build: System maintenance at 3 AM
- morning-briefing: Daily briefing at 8 AM
- daily-paper-digest: Paper digest at 9 AM
- munger-observer: Munger observations at 8 PM

**Core principle**: AI should act like a real assistant — proactively completing recurring tasks instead of waiting to be asked.

## Automation Philosophy

```
~/.openclaw/
├── workspace/
│   ├── AGENTS.md              # ← Provided by this repo
│   ├── SOUL.md                # ← Provided by this repo
│   ├── HEARTBEAT.md           # ← Provided by this repo
│   ├── USER.md                # ← Created by you (private)
│   ├── TOOLS.md               # ← Created by you (private)
│   ├── MEMORY.md              # ← Auto-maintained
│   └── memory/
│       ├── YYYY-MM-DD.md      # Daily logs
│       ├── topics/            # Topic knowledge base
│       ├── projects/          # Research projects
│       └── reading-group/     # Reading group shared notes
└── skills/                    # ← Shared Skills (recommended)
    ├── agent-reach/           # Internet access (essential) ⭐
    ├── self-improving-agent/
    ├── proactive-agent/
    └── ...
```

> **Tip**: Skills can be installed in two locations:
> - `~/.openclaw/skills/` — **Shared Skills** (recommended), accessible by multiple OpenClaw instances
> - `~/.openclaw/workspace/skills/` — **Workspace Skills**, only available to the current instance, suitable for personalized configurations

## ⚠️ Important Notes

### Token Cost Management

OpenClaw's token consumption can exceed expectations! Optimization tips:

- **Session resets**: Reset after completing tasks to prevent context bloat (saves 40-60%)
- **Model routing**: Use Haiku/Gemini Flash for simple tasks, Sonnet/Opus for complex ones (saves 50-80%)
- **Context limits**: Reduce the default 400K to 50-100K tokens (saves 20-40%)

Real-world testing shows combined optimizations can reduce costs from **$150/month to $35/month**.

### Security Hardening

Essential for production environments:

```bash
# Run security audit
openclaw security audit --deep

# Auto-fix issues
openclaw security audit --fix
```

Key security measures:
- ✅ Enable token authentication (`auth: "token"`)
- ✅ Set DM policy to `pairing` or `allowlist`
- ✅ Run security audits regularly
- ✅ Set API spending limits (provider-side)

## Privacy

This repository **never contains** any private information:
- ❌ Real names or email addresses
- ❌ Telegram group IDs
- ❌ API Keys
- ❌ Personal schedules or goals

These are excluded via `.gitignore` and configured only in local `USER.md` and `TOOLS.md` files.

## TODO

### Memory System Enhancements
- [ ] **Conversation import tool** — Import conversation history from Gemini, ChatGPT, and Claude into the OpenClaw memory system
  - Auto-parse conversation formats (JSON/Markdown)
  - Categorize and store into `memory/topics/` or `memory/reading-group/`
  - Preserve timestamps and context metadata
  - Support batch import and incremental updates

## Links

- [OpenClaw Docs](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [ClawHub](https://clawhub.com)
- [Agent Skills Specification](https://agentskills.io/specification)

## License

MIT — Free to use, modify, and distribute
