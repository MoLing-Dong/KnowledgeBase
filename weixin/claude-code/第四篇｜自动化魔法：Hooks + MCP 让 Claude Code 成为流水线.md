# 第四篇｜自动化魔法：Hooks + MCP 让 Claude Code 成为流水线

> **系列导读**：前三篇让你能用好 Claude Code，能让它记住项目，能把工作流变成命令。但目前为止，所有的行动都是你触发的——你说了，它才做。这一篇教你让 Claude Code 在**你没有说任何话的时候，也能自动运转**。

---

## 一个认知上的跨越

前三篇，你和 Claude Code 的关系是这样的：

**你提问 → Claude 响应 → 你提问 → Claude 响应**

这很有用。但还有一个层次：

**事件发生 → Claude Code 自动执行 → 你只需要看结果**

文件被保存？自动跑格式化。Claude 完成一轮回复？自动检查测试是否通过。Git diff 有改动？自动触发安全扫描。上下文快满了？自动保存关键信息再压缩。

实现这个层次的，是两套机制：**Hooks**（钩子）和 **MCP**（模型上下文协议）。

它们解决的是不同维度的问题：Hooks 控制 Claude Code 内部的行为——在特定生命周期节点自动执行你的脚本；MCP 打通外部世界——让 Claude Code 能读写 GitHub、Notion、数据库等外部系统。

把两者结合起来，Claude Code 就从一个"会对话的工具"，变成了一条真正的自动化流水线。

---

## 第一部分：Hooks — 给 Claude Code 的生命周期装上"触发器"

### Hooks 是什么

Claude Code 内部有一套生命周期事件系统。每次它准备做某件事（写文件、执行命令）、做完某件事、开始一个会话、结束一个会话……这些时刻都是"事件"。

Hooks 让你在这些事件上挂载自己的脚本，当事件触发时，你的脚本自动运行。

**关键区别**：CLAUDE.md 里的规则是"请求"，Claude 可能遵守也可能忘记；Hooks 是"确定性执行"，只要事件发生，脚本一定跑，结果可预测，不依赖 AI 的判断。

提示 → 70% 遵守。Hook → 100% 执行。这就是为什么 Hooks 对工程质量管控如此重要。

### 核心事件速查表

目前 Claude Code 支持超过 12 个生命周期事件，最常用的有：

| 事件               | 触发时机                    | 典型用途                             |
| ------------------ | --------------------------- | ------------------------------------ |
| `PreToolUse`       | Claude **即将**调用工具前   | 拦截危险操作、保护敏感文件、权限校验 |
| `PostToolUse`      | Claude **完成**工具调用后   | 自动格式化、跑测试、记录日志         |
| `SessionStart`     | 新会话开始时                | 注入上下文、加载环境变量             |
| `Stop`             | Claude 完成一轮回复后       | 验证任务完整性、发送桌面通知         |
| `PreCompact`       | 上下文压缩**前**            | 备份重要信息、保存会话快照           |
| `Notification`     | Claude 等待用户输入时       | 发送系统通知、防止你错过重要提示     |
| `UserPromptSubmit` | 你发送消息后、Claude 处理前 | 自动注入上下文、校验提问格式         |

**`PreToolUse` 是唯一能阻断操作的事件**——退出码 `2` 代表拦截，Claude 会收到你写在 stderr 里的说明并调整计划。其他事件的脚本只能提供反馈，不能阻止已发生的操作。

### Hooks 的三种触发类型

同一个事件可以有三种不同的处理器：

**`command`（Shell 命令）**：最常用，直接运行你的 bash 脚本或命令。通过 stdin 接收 JSON 格式的上下文数据，通过 stdout 返回结果，exit code 控制行为。

**`prompt`（AI 提示）**：把事件上下文发给 Claude 模型，让 AI 来判断要不要放行、给出什么反馈。适合需要语义理解的场景（比如"判断这条 git 提交信息是否符合规范"）。

**`agent`（子 Agent）**：启动一个带有文件读取、搜索等工具权限的子 Agent 来做深度验证。适合需要扫描多个文件才能做判断的复杂校验。

### Hooks 的配置位置

Hooks 配置在 `settings.json` 的 `hooks` 字段：

```json
// .claude/settings.json（提交到代码库，团队共享）
{
  "hooks": {
    "PostToolUse": [...],
    "PreToolUse": [...]
  }
}
```

也可以放在 `~/.claude/settings.json`（全局生效，不提交）或 `.claude/settings.local.json`（本地私有，不提交）。

---

### 实用 Hook 配方集

下面是六个可以直接复制使用的 Hook，覆盖最常见的自动化场景。

#### 配方一：文件保存后自动运行 Prettier

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=$(cat | jq -r '.tool_input.file_path // empty') && [ -n \"$FILE\" ] && [[ \"$FILE\" =~ \\.(js|ts|jsx|tsx|css|json)$ ]] && npx prettier --write \"$FILE\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

每次 Claude 写入或编辑 JS/TS/CSS/JSON 文件后，Prettier 自动格式化。你看到的 diff 永远是格式化后的干净代码，不需要手动跑一遍。

#### 配方二：保护敏感文件，拒绝 AI 修改

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "FILE=$(cat | jq -r '.tool_input.file_path // empty') && if echo \"$FILE\" | grep -qE '(\\.env|\\.lock|secrets\\.yaml|credentials|id_rsa|\\.pem|\\.key)'; then echo \"BLOCKED: 受保护文件，禁止 AI 修改: $FILE\" >&2; exit 2; fi"
          }
        ]
      }
    ]
  }
}
```

**退出码 2 = 拦截操作**。Claude 会收到你写在 stderr 的说明（"受保护文件，禁止 AI 修改"），并自动调整计划，不会再尝试修改这些文件。

这个 Hook 和 `--dangerously-skip-permissions` 配合很重要：官方文档明确说，**PreToolUse Hook 的拦截在 bypassPermissions 模式下依然生效**，即使跳过了所有权限提示，这个安全门也不会被绕过。

#### 配方三：Claude 完成回复后自动跑测试

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "INPUT=$(cat); ACTIVE=$(echo $INPUT | jq -r '.stop_hook_active'); [ \"$ACTIVE\" = \"true\" ] && exit 0; cd \"$PWD\" && npm test --silent 2>&1 | tail -5 || echo 'Tests failed' >&2; exit 2"
          }
        ]
      }
    ]
  }
}
```

每次 Claude 完成一轮工作后，自动跑测试。如果测试失败（exit 2），Claude 会收到测试输出作为反馈，并**自动继续工作去修复失败的测试**——不需要你手动说"测试失败了，去修"。

注意 `stop_hook_active` 的检查：这是防止 Stop Hook 触发后 Claude 再次 Stop 造成无限循环的保护机制。

#### 配方四：会话开始时自动注入当前 Git 分支信息

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "BRANCH=$(git branch --show-current 2>/dev/null || echo 'unknown'); LAST_COMMIT=$(git log -1 --oneline 2>/dev/null || echo 'no commits'); echo \"{\\\"additionalContext\\\": \\\"当前分支: $BRANCH\\\\n最近提交: $LAST_COMMIT\\\"}\""
          }
        ]
      }
    ]
  }
}
```

SessionStart Hook 的 stdout 会直接注入 Claude 的上下文。这样每次开新会话，Claude 第一件事就知道你在哪个分支、最近做了什么，不需要你再介绍。

#### 配方五：压缩前保存关键信息

```json
{
  "hooks": {
    "PreCompact": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo '{\"additionalContext\": \"压缩前保存上下文。请在压缩摘要里保留：1) 当前任务目标 2) 已做的关键决策 3) 待处理的问题列表 4) 重要的文件路径\"}'"
          }
        ]
      }
    ]
  }
}
```

上下文压缩（`/compact`）有时会丢失重要的中间决策和任务状态。PreCompact Hook 在压缩前给 Claude 一个提醒，告诉它压缩摘要里必须保留什么，确保关键信息不会在压缩中丢失。

#### 配方六：Claude 等待输入时发桌面通知（macOS）

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude Code 需要你的确认\" with title \"Claude Code\" sound name \"Glass\"'",
            "async": true
          }
        ]
      }
    ]
  }
}
```

`async: true` 表示异步运行，不阻塞 Claude 的主流程。Claude 遇到需要你确认的操作停下来等待时，macOS 会弹出通知声音，你不用一直盯着终端。

Linux 版把 `osascript` 替换为 `notify-send`，Windows 版使用 PowerShell 的 `New-BurntToastNotification`。

---

### Hooks 快速入门：三步走

**第一步**：在项目目录创建 `.claude/hooks/` 目录，把脚本放进去，赋予执行权限：

```bash
mkdir -p .claude/hooks
chmod +x .claude/hooks/post-edit.sh
```

**第二步**：在 `.claude/settings.json` 里配置 Hook：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/post-edit.sh"
          }
        ]
      }
    ]
  }
}
```

**第三步**：用 `/hooks` 命令打开交互界面验证配置是否正确加载，然后触发一次操作看 Hook 是否生效。

**新手建议**：从这三个 Hook 开始，它们覆盖面最广、效果最直接——PostToolUse 自动格式化、PreToolUse 保护敏感文件、Stop 发送桌面通知。先跑通这三个，再根据实际痛点扩展。

---

## 第二部分：MCP — 给 Claude Code 接上外部世界

### MCP 是什么

MCP（Model Context Protocol）是 Anthropic 开源的一套标准协议，让 AI 能够和外部工具、系统进行结构化通信。

没有 MCP 时，Claude Code 只能在你的本地文件系统里工作——读文件、写文件、跑命令，仅此而已。

接入 MCP 服务器之后，Claude Code 就能：

- 查询你的 GitHub Issues，直接在对话里创建 PR
- 读取 Notion 里的需求文档，不需要你复制粘贴
- 连接数据库，直接查询数据来辅助调试
- 查看 Sentry 的最新报错，让 Claude 直接去修
- 访问 Linear 的任务列表，根据任务优先级决定做什么

**工作原理**：MCP 服务器是一个运行在你本地的进程（或远程 URL），它暴露一套工具给 Claude。Claude 需要查 GitHub 时，它把请求发给 GitHub MCP 服务器，服务器用你的凭证去调 GitHub API，把结果返回给 Claude。**你的 API Key 留在本地，不经过 Anthropic 的服务器。**

### 三种配置范围：本地、项目、全局

```bash
# 本地（只有你，只在当前项目）—— 默认
claude mcp add --transport http stripe https://mcp.stripe.com

# 项目级（提交到 .mcp.json，整个团队共享）
claude mcp add --transport http sentry --scope project https://mcp.sentry.dev/mcp

# 全局（你个人，所有项目都能用）
claude mcp add --transport http notion --scope user https://mcp.notion.com/mcp
```

优先级：本地 > 项目级 > 全局。团队可以共享基础 MCP 配置（`.mcp.json` 提交到代码库），个人可以在本地层级覆盖或添加自己的服务器，两层互不干扰。

### 实战：接入四个最有价值的 MCP 服务器

#### GitHub MCP——让 Claude 直接管理你的代码仓库

```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/v1
# 然后在 /mcp 里走一遍 OAuth 授权流程
```

接入后你可以直接对 Claude 说：

> "查看本仓库最近 7 天新增的 Issue，按优先级排序，给我一个今天应该处理哪个的建议。"

> "基于当前分支的改动，帮我写 PR 描述，然后通过 gh 创建 PR，target branch 是 develop。"

> "看一下 Issue #234 的详情，然后在当前分支上实现它。"

Claude 会调用 GitHub MCP 的工具来完成这些操作，不需要你在浏览器和终端之间来回切换。

#### Sentry MCP——让 Claude 看到生产环境的报错

```bash
claude mcp add --transport http sentry --scope project https://mcp.sentry.dev/mcp
```

接入后，当生产环境出问题时，工作流变成这样：

> "看一下 Sentry 里最近 2 小时的新报错，找出频率最高的那个，定位到代码里的具体位置，告诉我原因并给出修复方案。"

Claude 直接读取真实的报错堆栈和环境信息，不需要你复制粘贴 Sentry 截图。

这是 MCP 最能体现价值的场景之一：**把"看问题、定位、修复"的循环压缩到一个对话里完成**。

#### 数据库 MCP——调试时直接查数据

以 PostgreSQL 为例（本地开发数据库）：

```bash
claude mcp add postgres \
  --command "npx -y @modelcontextprotocol/server-postgres" \
  -- postgresql://localhost/your_dev_db
```

接入后，调试数据相关问题时：

> "查一下 users 表里注册时间在上周、但从未登录的用户有多少，再看看他们的 email 后缀分布。"

> "这个 API 响应很慢，帮我分析一下 invoices 表的查询计划，是否需要加索引。"

**安全注意**：只接入开发或测试数据库，永远不要把生产数据库的连接串配置进去。

#### Notion MCP——把需求文档直接接进上下文

```bash
claude mcp add --transport http notion --scope user https://mcp.notion.com/mcp
# /mcp 里完成 OAuth 授权
```

接入后，开始做新功能前：

> "读一下 Notion 里'Q2 产品需求'这个页面，找到关于用户导出功能的部分，然后根据需求帮我拆解技术实现方案。"

Claude 直接读 Notion，不需要你把需求复制进来。这在多人协作项目里特别有价值——产品更新了需求文档，下次 Claude 会话里就能读到最新版本。

### 团队共享 MCP 配置：`.mcp.json`

把 `.mcp.json` 提交到项目根目录，团队所有成员克隆后自动获得同样的 MCP 配置：

```json
{
  "mcpServers": {
    "sentry": {
      "type": "http",
      "url": "https://mcp.sentry.dev/mcp"
    },
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/v1"
    },
    "postgres-dev": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DEV_DATABASE_URL}"
      }
    }
  }
}
```

注意 `${DEV_DATABASE_URL}` 这个语法——MCP 配置支持环境变量展开，敏感的连接串不需要硬编码进配置文件，每个团队成员在自己的 `.env` 里设置即可。

### MCP 的一个重要代价：上下文 Token

每启用一个 MCP 服务器，它的工具定义就会占用上下文窗口的 token。服务器越多，每次会话的基础 token 消耗越高。

实践原则：**按需启用，不用的时候关掉**。用 `/mcp` 命令查看当前启用了哪些服务器，禁用不需要的，是完全免费的 token 节省。

---

## 第三部分：Hooks + MCP 的组合拳

Hooks 和 MCP 单独都很有用，真正有意思的是把它们组合起来。

**场景：PR 自动检查流水线**

你在一个功能分支上工作，每次 Claude 完成一轮代码修改后，自动触发：

1. （PostToolUse Hook）运行 lint + typecheck，把结果写进上下文
2. （Stop Hook）运行测试，失败了自动告知 Claude
3. 你说"好了，提 PR 吧"
4. Claude 通过 GitHub MCP 查看 PR 模板，根据改动生成 PR 描述，创建 PR
5. （Notification Hook）PR 创建成功后发桌面通知给你

整个流程里，你只做了两件事：说"做这个功能"和说"提 PR 吧"。中间所有的质量检查、修复、提交，全是自动发生的。

**场景：生产 Bug 快速响应**

Sentry 报警来了，你打开 Claude Code：

> "Sentry 刚报了一个新错误，看一下，定位原因，在当前分支修复，跑测试，提 PR。"

Claude 通过 Sentry MCP 读取报错详情 → 在代码库里定位根因 → 写修复 → （Stop Hook 触发）自动跑测试 → 测试通过 → 通过 GitHub MCP 创建修复 PR → 通知你审查。

从报警到 PR 创建，整个闭环可以在你不离开椅子的情况下完成。

---

## 本篇小结

这一篇你掌握了 Claude Code 自动化体系的两个核心机制：

**Hooks**：
- 12+ 个生命周期事件，覆盖从会话开始到压缩再到工具调用的每个节点
- PreToolUse 是唯一能拦截操作的事件，exit code 2 = 阻断，且在任何权限模式下都生效
- 三种处理器类型：shell 命令、AI 提示、子 Agent
- 从三个起步：PostToolUse 自动格式化、PreToolUse 保护敏感文件、Notification 桌面通知

**MCP**：
- 三个配置范围：本地（私有）→ 项目（团队共享，提交 .mcp.json）→ 全局（跨项目个人）
- 四个最有价值的服务器：GitHub（代码管理）、Sentry（生产报错）、数据库（调试查询）、Notion（需求同步）
- 记住代价：每个启用的 MCP 服务器都消耗 token，按需启用

**两者组合**，可以构建出"你说目标，流水线自动跑"的开发体验。

---

**下一篇预告**：你已经掌握了从单人使用到自动化流水线的全套工具。最后一篇，我们进入真正的顶级玩法——**多 Agent 并行执行、Headless 无人值守、CI/CD 集成，以及让账单不失控的成本控制**。这是"一个人用 Claude Code 做出团队产出"的终极方案。

---

*《Claude Code 从零到高手》系列 | 第四篇（共五篇）*