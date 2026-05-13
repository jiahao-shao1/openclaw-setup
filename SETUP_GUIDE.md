**English** | [中文](SETUP_GUIDE.zh.md)

# Detailed Setup Guide

This document covers how to configure and customize your OpenClaw environment.

## Table of Contents

1. [Local Deployment](#local-deployment)
2. [Installing Skills](#installing-skills)
3. [Configuring Agent Reach](#configuring-agent-reach)
4. [Model Configuration](#model-configuration)
5. [Network Proxy](#network-proxy)
6. [Scheduled Tasks](#scheduled-tasks)
7. [Token Cost Optimization](#token-cost-optimization)
8. [Security Hardening](#security-hardening)

---

## Local Deployment

### Requirements
- macOS / Linux / Windows (WSL2)
- Node.js >= 22.0.0
- 2GB+ RAM
- 10GB+ disk space

### Installation

```bash
# Option 1: One-line install script (recommended)
curl -fsSL https://openclaw.ai/install.sh | bash

# Option 2: npm global install
npm install -g openclaw@latest

# Run the setup wizard
openclaw onboard --install-daemon
```

The setup wizard will walk you through:
- Choosing an AI model provider (Claude, OpenAI, Gemini, etc.)
- Configuring your API key
- Selecting a messaging channel (Telegram, Lark, etc.)

### Verifying the Installation

```bash
# Check Gateway status
openclaw gateway status

# Start the Gateway
openclaw gateway start

# Open the dashboard
openclaw dashboard

# Run a health check
openclaw doctor
```

### Troubleshooting

```bash
# If the Gateway fails to start
openclaw gateway logs
openclaw gateway restart

# Check for port conflicts
lsof -i :18789
```

---

## Installing Skills

Skills can be installed in two ways:

### Option 1: ClawHub (recommended, but currently rate-limited)

```bash
# List available Skills
clawhub list

# Install a Skill
clawhub install skill-name

# Update a Skill
clawhub update skill-name
```

> ⚠️ **Note**: ClawHub is currently subject to rate limits. If installation fails, use the manual installation method instead.

### Option 2: Manual Installation (git clone)

If ClawHub installation fails, you can manually clone Skills into `~/.openclaw/skills/` (Shared Skills, accessible by multiple OpenClaw instances):

### Core Skills

```bash
# Create the shared skills directory (recommended, shareable across instances)
mkdir -p ~/.openclaw/skills

# Agent Reach - Gives your AI access to the internet (Twitter/X, Reddit, YouTube, GitHub, Bilibili, Xiaohongshu, Douyin, etc.)
git clone https://github.com/Panniantong/agent-reach.git \
  ~/.openclaw/skills/agent-reach

# Self-improving agent system
git clone https://github.com/pskoett/self-improving-agent.git \
  ~/.openclaw/skills/self-improving-agent

# Proactive agent architecture
git clone https://github.com/halthelobster/proactive-agent.git \
  ~/.openclaw/skills/proactive-agent
```

### Other Recommended Skills

```bash
# AI image generation (powered by Gemini)
git clone https://github.com/openclaw/nano-banana-pro.git \
  ~/.openclaw/skills/nano-banana-pro

# PDF/document to Markdown converter
git clone https://github.com/openclaw/markdown-converter.git \
  ~/.openclaw/skills/markdown-converter

# Hand-drawn style diagrams
git clone https://github.com/openclaw/excalidraw.git \
  ~/.openclaw/skills/excalidraw

# TweetClaw X/Twitter automation plugin
openclaw plugins install @xquik/tweetclaw

# Remove AI-generated text patterns
git clone https://github.com/openclaw/de-ai-ify.git \
  ~/.openclaw/skills/de-ai-ify

# Chinese text humanizer
git clone https://github.com/openclaw/humanizer-zh.git \
  ~/.openclaw/skills/humanizer-zh

# Quick reminders
git clone https://github.com/openclaw/quick-reminders.git \
  ~/.openclaw/skills/quick-reminders

# GitHub operations (gh CLI)
git clone https://github.com/openclaw/skills.git \
  ~/.openclaw/skills/steipete-github
cd ~/.openclaw/skills/steipete-github
git sparse-checkout init
git sparse-checkout set skills/steipete/clawdhub
```

---

## Configuring Agent Reach

Agent Reach is the key skill that gives OpenClaw "eyes" — direct access to major platforms across the internet.

### Quick Setup

Copy this message and send it to your AI Agent:

```
Help me install Agent Reach: https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

The Agent will handle everything from there.

### Safe Mode

🛡️ Concerned about security? Use safe mode — it won't automatically install system packages, and will only tell you what's needed:

```
Help me install Agent Reach (safe mode): https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

Use the `--safe` flag during installation.

### Manual Installation (fallback)

If the above methods don't work, you can install manually:

```bash
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
agent-reach install --env=auto
agent-reach doctor
```

### Configuring a Proxy

```bash
agent-reach configure proxy http://127.0.0.1:7890
```

### Importing Cookies

For any platform that requires login (Twitter, Xiaohongshu, etc.):

1. Log into the platform in your browser
2. Install the [Cookie-Editor](https://chromewebstore.google.com/detail/cookie-editor/hlkenndednhfkekhgcdicdfddnkalmdm) Chrome extension
3. Click the extension → Export → **Header String**
4. Send the exported string to your Agent

Or extract cookies automatically via the command line:
```bash
agent-reach configure --from-browser chrome
```

### Platform Usage Examples

Once installed, call the upstream tools directly:

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

# Xiaohongshu
mcporter call 'xiaohongshu.search_feeds(keyword: "query")'

# Douyin
mcporter call 'douyin.parse_douyin_video_info(share_link: "https://v.douyin.com/xxx/")'

# GitHub
gh search repos "query" --sort stars --limit 10

# Any webpage
curl -s "https://r.jina.ai/URL" -H "Accept: text/markdown"
```

---

## Model Configuration

### Editing the Gateway Config

Config file location: `~/.openclaw/openclaw.json`

### Recommended Configuration

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

### Model Selection Guide

| Use Case | Recommended Model | Reason |
|----------|------------------|--------|
| Complex coding tasks | Claude Opus 4.6 | Best agentic capabilities |
| General conversation | GPT-5.2 | Fast, generous quota |
| Cost-sensitive | Gemini Flash | Very affordable |
| Chinese-language tasks | Kimi K2.5 | Strong Chinese comprehension |

---

## Network Proxy

### Essential for Users in Mainland China

Add the following to your `~/.zshrc` or `~/.bashrc`:

```bash
# General proxy (works with most tools)
export ALL_PROXY=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
export http_proxy=http://127.0.0.1:7890
export all_proxy=socks5://127.0.0.1:7890

# Adjust the port to match your proxy software
# Clash default: 7890
# Surge default: 6152
```

### Tool-Specific Notes

| Tool | Proxy Variable | Notes |
|------|---------------|-------|
| Node.js/Python | `ALL_PROXY` | Universal |
| Go tools | `HTTP_PROXY` + `HTTPS_PROXY` | Go does not recognize `ALL_PROXY`! |
| yt-dlp | `ALL_PROXY` | Supported |
| curl/wget | `https_proxy` | Standard |

### Testing the Proxy

```bash
# Verify it's working
curl -I https://www.google.com
```

---

## Scheduled Tasks

### Using Cron

```bash
# List all tasks
openclaw cron list

# Add a daily task
openclaw cron add --name "morning-brief" \
  --schedule "0 8 * * *" \
  --command "morning-briefing"
```

### Recommended Task Configuration

| Task | Cron Expression | Description |
|------|----------------|-------------|
| nightly-build | `0 3 * * *` | System maintenance at 3 AM |
| morning-brief | `0 8 * * *` | Morning briefing at 8 AM |
| paper-digest | `0 9 * * *` | Paper digest at 9 AM |
| munger-observer | `0 20 * * *` | Munger observer at 8 PM |

---

## Token Cost Optimization

⚠️ **Warning**: OpenClaw's token consumption can far exceed expectations! Users have reported spending $18.75 in a single overnight "idle" session.

### Cost Breakdown

| Source | Share | Description |
|--------|-------|-------------|
| Context accumulation | 40-50% | Unbounded session history growth |
| Tool output storage | 20-30% | Large JSON/log persistence |
| System prompts | 10-15% | Complex prompts sent repeatedly |
| Heartbeat tasks | 5-10% | Misconfigured background processes |

### Optimization Strategies

#### 1. Session Reset (saves 40-60%)
```json
{
  "agent": {
    "sessionReset": "after-task",
    "maxContextTokens": 50000
  }
}
```

#### 2. Smart Model Routing (saves 50-80%)
- Routine tasks: Haiku or Gemini Flash
- Complex reasoning: Sonnet/Opus

#### 3. Context Window Limits (saves 20-40%)
Reduce the default 400K to 50-100K tokens.

### Combined Results

Real-world results from combined optimizations:
- **Before**: $150/month
- **After**: $35/month
- **Annual savings**: $1,380

---

## Security Hardening

⚠️ **Security Warning**: Always harden your setup before deploying to production.

### Quick Security Check

```bash
# Run a deep security audit
openclaw security audit --deep

# Auto-fix issues
openclaw security audit --fix
```

### Key Security Measures

#### 1. Enable Authentication
```json
{
  "gateway": {
    "auth": "token"
  }
}
```

#### 2. Configure DM Policy
```json
{
  "channels": {
    "telegram": {
      "dmPolicy": "pairing"
    }
  }
}
```

Policy options:
- `pairing` (default): Unknown senders receive a time-limited pairing code
- `allowlist`: Block unknown senders entirely
- `open`: Allow anyone (not recommended)
- `disabled`: Ignore all inbound DMs

#### 3. Filesystem Isolation
```json
{
  "security": {
    "filesystem": {
      "workspaceOnly": true
    }
  }
}
```

#### 4. API Spending Limits
Set hard spending limits on the provider side (OpenAI, Anthropic, etc.).

### Known Security Risks

- **CVE-2026-25253**: WebSocket hijacking vulnerability (fixed in v2026.1.29)
- **Exposed gateways**: 923 instances found publicly exposed with zero authentication
- **Malicious extensions**: Fake "ClawdBot Agent" VS Code extension

**Recommendation**: Always keep OpenClaw updated to the latest version.

---

## Additional Resources

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [ClawHub](https://clawhub.com)
- [OpenClaw Security Best Practices](https://docs.openclaw.ai/security)
