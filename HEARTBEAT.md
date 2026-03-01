# HEARTBEAT.md - 定时检查任务

_定义你的 Agent 的例行工作_

## 每次心跳检查

- [ ] 检查 `memory/` 目录下今天的日记文件是否存在，不存在则创建
- [ ] 如果有待安装的 Skills，尝试安装一个
- [ ] Git 同步：在 workspace 执行 `git add -A && git commit -m "auto: heartbeat sync" && git push`（无变更则跳过）

## 每日检查（轮换）

- [ ] **天气** - 查看本地今天和明天的天气
- [ ] **日历/提醒** - 检查是否有即将到期的任务
- [ ] **邮件** - 检查是否有紧急未读邮件

## 记忆维护（每 3-5 次心跳）

- [ ] 读取最近几天的 `memory/YYYY-MM-DD.md`
- [ ] 把重要信息提炼到 `MEMORY.md`
- [ ] 清理 `MEMORY.md` 中过时的内容

**原则：** 日志是原始笔记，`MEMORY.md` 是精华

## 记忆归档（每 3-5 次心跳）

将每日日志内容按主题自动整理：

| 内容类型 | 目标位置 | 说明 |
|---------|---------|------|
| 论文阅读笔记 | `reading-group/papers/` | 可分享 |
| AI 行业动态 | `reading-group/news/` | 可分享 |
| 研究兴趣/方向 | `reading-group/topics/` | 可分享 |
| 研究项目详情 | `memory/projects/` | 私密 |
| 个人生活记录 | `memory/personal/` | 私密 |
| 技术配置 | `memory/topics/` | 私密 |

## 主动改进（有空时）

- [ ] 找一个小摩擦点改进（整理文件、写文档、优化工作流）
- [ ] 只做可逆的改动，不主动发邮件/删东西
- [ ] 记录做了什么到当天的 memory 日志

## 待安装 Skills 队列

在此列出想安装的 Skills，每次心跳尝试安装一个：

- [ ] skill-name-1
- [ ] skill-name-2

## 状态追踪

**`memory/heartbeat-state.json`:**
```json
{
  "lastChecks": {
    "email": 1703275200,
    "calendar": 1703260800,
    "weather": null
  },
  "installedSkills": [],
  "pendingTasks": []
}
```

---

*根据你的需求自定义此文件*
