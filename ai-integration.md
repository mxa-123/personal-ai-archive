# AI 接入配置（ai-integration.md）

本文件说明如何把 `personal-ai-archive` 接入各类 AI 平台，以及 AI 如何**自动读取 / 回写**档案。

> ⚠️ 先说清楚事实边界（避免误导）：
> - GitHub 仓库本身**不能**反向"配置"ChatGPT / Gemini / Claude 去读它。
>   读取动作发生在**各家 AI 平台侧**，本仓库只是被读取的**数据源**。
>   下面的方案都是"在各平台侧如何接入这个数据源"，不是 GitHub 侧一键开关。
> - "Meta Muse / OpenAI Dots 等 Agent 自动同步"目前**没有标准公开接口**。
>   本节给出的是**通用数据格式 + 可选 Webhook 方案**，等这类 Agent 开放记忆接口即可直接对接，
>   我不会编造尚不存在的能力。

---

## 1. 通用原理

```
AI 平台 / Agent
   │  (1) 读取
   ▼
GitHub 仓库 personal-ai-archive  ──►  RAW 文件 / REST API
   ▲
   │  (2) 回写（完成任务后）
   │
AI 通过 GitHub API 提交改动（commit / PR）
```

所有文件都是纯 Markdown + 顶部 `AI-META` 注释，无需格式转换，AI 可直接解析。

---

## 2. 各平台接入方式（现实可行）

### 2.1 ChatGPT
| 方式 | 做法 | 适合 |
|------|------|------|
| **Custom GPT + Actions** | 在 GPT 配置里加一个 Action，调用 GitHub REST API（`GET /repos/{owner}/personal-ai-archive/contents/...`），并在 Instructions 里写"先读 AI-INDEX.md" | 长期复用、可自动拉取最新 |
| **Projects 上传** | 把仓库文件打包下载，作为 Project 知识库上传 | 一次性、无需 API |
| **手动粘贴** | 直接把 `AI-INDEX.md` + 相关文件内容贴进对话 | 临时 |

推荐 Custom GPT Instructions 模板：
```
你是我的人工智能助手。每次对话开始时，先通过 GitHub API 读取
https://raw.githubusercontent.com/{owner}/personal-ai-archive/main/AI-INDEX.md，
然后按需读取其中列出的文件，理解我的身份、习惯与偏好后再回答。
遇到 AI-EDITABLE 文件中有价值的新经验，按本仓库约定追加并生成提交。
```

### 2.2 Gemini（Google）
- **Gemini Advanced - 已保存信息 / 上传文件**：把 `AI-INDEX.md` 及各文件作为附件上传，或在"已保存信息"里写一段"先读取以下仓库内容"。
- **API / Vertex AI**：在系统提示里注入 `AI-INDEX.md` 内容，或用工具调用 GitHub API 拉取最新版。
- 说明：Gemini 没有原生的"GitHub 自动同步"开关，需借助上传或自建工具。

### 2.3 Claude（Anthropic）
- **Projects + 知识库**：把仓库文件加入 Project 的 Knowledge，Claude 会自动参考。
- **Claude API + MCP**：使用官方 `github` MCP server，让 Claude 直接读 / 写仓库文件。
- **手动**：粘贴 `AI-INDEX.md` 到对话开头。

### 2.4 任意 Agent / 自研脚本（通用）
通过 GitHub REST API 即可读写，任何能发 HTTP 请求的程序都能接入：
- 读取：`GET https://api.github.com/repos/{owner}/personal-ai-archive/contents/{path}`
- 原始内容：`https://raw.githubusercontent.com/{owner}/personal-ai-archive/main/{path}`
- 回写：见第 4 节。

### 2.5 DSH（DeepSeek Harness，本地桌面端）
- 独立提示词见 [`dsh-prompt.md`](./dsh-prompt.md)：复制其中代码块到 DSH 的「系统提示 / 角色设定」或首条消息即可接入。
- DSH 跑在同一台 Windows 主机上，**优先读取本地仓库路径** `E:\WorkBuddy\2026-10-23-21-40-32\personal-ai-archive`；无文件权限时退回 GitHub 原始 URL。
- 若 DSH 无自动写回能力，让它把建议追加内容以 Markdown 片段返回，由本人或 WorkBuddy 合并提交。

---

## 3. 面向"记忆型 Agent"的数据格式（Meta Muse / OpenAI Dots 等）

这些产品目前没有公开的标准记忆接口。为未来对接，本仓库提供**两种可机器消费的导出格式**，
当某 Agent 支持"记忆导入 / Webhook 推送"时，直接消费即可。

### 3.1 轻量 JSON 快照（建议 Agent 拉取）
Agent 可定期调用下面脚本生成 `snapshot.json`（放在仓库根或本地），
内容即 `AI-INDEX.md` 中列出的所有文件文本：

```bash
# 伪代码：拼装快照
for f in $(grep 'path:' AI-INDEX.md | awk '{print $2}'); do
  echo "读取 $f 并写入 snapshot.json"
done
```

### 3.2 推送接口约定（若 Agent 支持 Webhook）
若 Agent 提供接收记忆的 Webhook，按如下结构推送：
```json
{
  "source": "personal-ai-archive",
  "owner": "{{你的 GitHub 用户名}}",
  "updated_at": "2026-10-23T21:40:00+08:00",
  "identity": { "name": "...", "role": "..." },
  "preferences": ["...", "..."],
  "recent_experiences": [{ "date": "...", "title": "...", "conclusion": "..." }]
}
```
> 仅推送**非敏感**字段（身份基础信息、偏好、经验结论），绝不推送密钥 / 联系方式等。

---

## 4. 自动回写规则（AI 完成任务后写入档案）

### 4.1 写入原则
- 只在 `AI-EDITABLE` 文件**末尾追加**新条目，不覆盖历史。
- 每条带 `YYYY-MM-DD` 日期。
- 禁止写入密钥、令牌、手机号、身份证号（见 `.gitignore` 与第 6 节）。
- 每次写入必须有清晰的 commit message，例如：
  `memory: 追加 2026-10-23 经验——Ollama 下载限速结论`

### 4.2 通过 GitHub API 更新一个文件（示例）
```http
PUT https://api.github.com/repos/{owner}/personal-ai-archive/contents/memory/experiences.md
Authorization: Bearer <YOUR_GITHUB_TOKEN>
Content-Type: application/json

{
  "message": "memory: 追加经验条目",
  "sha": "<该文件当前的 sha，首次需先 GET 获取>",
  "content": "<追加后的完整文件内容，Base64 编码>",
  "branch": "main"
}
```
> 若开启了分支保护（要求 PR），则 AI 应写到新分支再创建 PR，由本人合并。

### 4.3 推荐自动化流程（Agent 工作流）
1. 任务完成 → 判断产出是否值得沉淀（经验 / 偏好 / 决策）。
2. 读取目标文件当前内容 + `sha`。
3. 在末尾追加条目，Base64 编码后 `PUT`（或提 PR）。
4. 在 `archive/completed-tasks.md` 记录本次任务产出。

---

## 5. 版本保护与安全（GitHub 侧）

### 5.1 分支保护（Settings → Branches → Branch protection rules）
建议对 `main` 配置：
- ✅ Require a pull request before merging（AI 改动走 PR，本人审核）
- ✅ Block force pushes（禁止强推，防止历史被抹掉）
- ✅ Block branch deletion（禁止误删分支）
- ⚠️ Require status checks / 多人 review：单人使用可不开，避免卡住自己

> 折中方案：若你希望 AI 能直接提交、又防误删，可只开
> "Block force pushes + Block deletion"，关闭 PR 强制要求。

### 5.2 一键回退到任意版本
- 网页：进入文件 → History → 选中某次提交 → Revert。
- 命令行：
  ```bash
  git log --oneline            # 找到目标提交 hash
  git revert <hash>            # 生成反向提交（推荐，保留历史）
  # 或仅恢复单个文件：
  git checkout <hash> -- memory/experiences.md
  ```
- 所有改动天然留痕，不存在"AI 偷偷删光"的风险（只要有提交记录）。

### 5.3 防泄露
- `.gitignore` 已屏蔽 `.env`、`*.key`、`token.txt` 等。
- `identity/profile.md` 标注 `AI-READONLY`，AI 不写不删。
- 公开仓库请**务必**用占位符，不要把真实手机号 / 令牌提交上去。

---

## 6. 自检清单（接入前确认）
- [ ] 所有 `{{占位符}}` 已填写并删除花括号
- [ ] `identity/profile.md` 无敏感信息
- [ ] 已开启 `main` 分支保护（至少禁止强推 / 删除）
- [ ] 已为 AI 生成**最小权限** GitHub Token（`repo` 范围，可读写本仓库）
- [ ] 在常用 AI 平台粘贴了 `AI-INDEX.md` 或配置了读取 Action
