# 可复用提示词（REUSABLE-PROMPT）

> 复制下面这段，粘贴到任意支持 GitHub 写入的 AI 会话（需已连接 GitHub 或本机 `gh` 已登录），
> 即可一键重建本仓库。占位符 `{{...}}` 请按实际情况替换。

---

你是我的 AI 个人档案搭建助手。请在我的 GitHub 账号下创建公开仓库 `personal-ai-archive`，
并自动生成如下结构与内容（全部用纯 Markdown，AI 可直接读改）：

1. 目录：`identity/`（身份核心，profile 设为只读）、`workflow/`（习惯/工具/沟通/优先级）、
   `memory/`（决策/经验/偏好）、`skills/`（已掌握/在学/待提升）、`archive/`（项目/任务归档）。
2. 每个目录含 README + 标准化模板：顶部写 `# AI-READONLY` 或 `# AI-EDITABLE` 标签，
   并加 `<!-- AI-META tag:... access:... last-updated:... -->` 注释；正文用 `{{占位符}}`。
3. 根目录生成：`README.md`、`LICENSE`（MIT，年份/作者用占位符）、`.gitignore`（屏蔽密钥/令牌/手机号）、
   `AI-INDEX.md`（YAML 列出所有文件标签/权限/路径，供 AI 快速定位）、
   `ai-integration.md`（真实可行的跨 AI 接入说明：ChatGPT/Gemini/Claude 如何读取、
   Agent 如何用 GitHub API 回写、分支保护与回退方案；对尚不存在的标准接口只预留数据格式，不编造）。
4. 安全：`.gitignore` 禁止提交敏感信息；建议对 main 开分支保护（禁强推/禁删）。
5. 完成后输出搭建报告（含仓库地址、各目录用法、AI 接入快速指南）。
6. 如当前会话无 GitHub 写入能力，先在本地产出完整仓库并提交，再给出最小发布命令，不要假装成功。

关键要求：先给结论再展开；不编造不可行的配置；敏感信息（手机号/令牌）绝不写入仓库。

---
