# personal-ai-archive

跨 AI、跨设备通用的**个人记忆系统**。一次搭建，全平台复用。

> 设计目标：任何支持读取 GitHub 仓库的 AI（ChatGPT / Gemini / Claude / 各类 Agent）
> 都能直接拉取本仓库，理解"你是谁、怎么协作、有哪些经验"，无需每次重新介绍自己。

## 仓库结构
```
personal-ai-archive/
├── identity/    # 个人身份、职业、人生目标（只读核心 + 可编辑）
├── workflow/    # 工作习惯、工具、沟通风格、优先级规则
├── memory/      # 决策记录、经验沉淀、个人偏好
├── skills/      # 已掌握技能、学习计划、待提升能力
├── archive/     # 历史项目、已完成任务归档
├── ai-integration.md  # 跨 AI 接入配置（重点读这个）
├── AI-INDEX.md        # AI 快速定位索引（机器可读）
├── LICENSE
└── .gitignore
```

## 三类 AI 标签
每个 Markdown 文件顶部都有标签，AI 据此决定能否修改：

| 标签 | 含义 | AI 行为 |
|------|------|---------|
| `AI-READONLY` | 只读核心信息 | 仅读取，不写不删 |
| `AI-EDITABLE` | 可编辑内容 | 可在末尾追加 / 更新，并生成提交 |
| `AI-META` 注释 | 机器元数据 | 解析 `tag / access / last-updated` |

## 快速开始（本人）
1. 填写所有 `{{占位符}}`，删除花括号。
2. 把 `identity/profile.md` 等只读文件确认无误。
3. 提交并推送到 GitHub（见文末或 `ai-integration.md`）。
4. 在任意 AI 平台粘贴仓库地址，让它先读 `AI-INDEX.md`。

## 安全须知
- 本仓库**禁止**写入任何密钥、令牌、手机号、身份证号等敏感信息。
- 建议对 `main` 分支开启保护（禁止强推、禁止删除），所有 AI 改动走 PR 或受控提交。
- 任何改动都会留下 Git 提交记录，可随时回退到任意历史版本。

详见 [`ai-integration.md`](./ai-integration.md)。
