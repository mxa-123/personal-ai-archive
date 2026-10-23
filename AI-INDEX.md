# AI-INDEX — 机器可读索引

> 本文件供 AI 快速定位档案内容。每个条目包含：标签、访问权限、相对路径、用途。
> AI 首次接入时，先读本文件，再按需读取对应 Markdown。

```yaml
repo: personal-ai-archive
owner: "{{你的 GitHub 用户名}}"
default_branch: main
purpose: 跨 AI / 跨设备复用的个人记忆系统
files:
  - tag: identity/profile
    access: readonly
    path: identity/profile.md
    desc: 身份核心信息（姓名、称呼、所在地）
  - tag: identity/career
    access: editable
    path: identity/career.md
    desc: 职业与核心标签
  - tag: identity/goals
    access: editable
    path: identity/goals.md
    desc: 人生目标与价值观
  - tag: workflow/habits
    access: editable
    path: workflow/habits.md
    desc: 工作与学习习惯
  - tag: workflow/tools
    access: editable
    path: workflow/tools.md
    desc: 常用工具链与环境
  - tag: workflow/communication
    access: editable
    path: workflow/communication.md
    desc: 沟通风格偏好
  - tag: workflow/priorities
    access: editable
    path: workflow/priorities.md
    desc: 任务优先级与决策规则
  - tag: memory/decisions
    access: editable
    path: memory/decisions.md
    desc: 重要决策记录
  - tag: memory/experiences
    access: editable
    path: memory/experiences.md
    desc: 历史经验沉淀
  - tag: memory/preferences
    access: editable
    path: memory/preferences.md
    desc: 个人偏好沉淀
  - tag: skills/mastered
    access: editable
    path: skills/mastered.md
    desc: 已掌握技能
  - tag: skills/learning
    access: editable
    path: skills/learning.md
    desc: 学习计划
  - tag: skills/to-improve
    access: editable
    path: skills/to-improve.md
    desc: 待提升能力清单
  - tag: archive/projects
    access: editable
    path: archive/projects.md
    desc: 历史项目归档
  - tag: archive/completed-tasks
    access: editable
    path: archive/completed-tasks.md
    desc: 已完成任务归档
rules:
  - 含 AI-READONLY 的文件只读取，不修改不删除
  - 含 AI-EDITABLE 的文件可在末尾追加，并生成提交记录
  - 禁止写入任何密钥 / 令牌 / 手机号 / 身份证号
  - 每次 AI 写入必须附带清晰 commit message 与日期
```
