# 第一篇｜初体验：装好 Claude Code，跑起来再说

> **系列说明**：这是《Claude Code 从零到高手》系列第一篇，面向完全没用过 Claude Code 的开发者。目标只有一个：让你在 15 分钟内装好、登录、跑起第一个真实任务，并且感受到"哇，这东西不一样"的那个时刻。

---

## 你可能对 Claude Code 有一个误解

很多人第一次听说 Claude Code，会以为它是"Claude 版的 GitHub Copilot"——补全代码、给建议、在编辑器里弹出提示。

不是的。两者的差距，大概是计算器和会计师的差距。

Copilot 在你的光标旁边补全一行代码。Claude Code 读懂你的整个项目，自己决定要动哪些文件、怎么改、改完跑什么命令验证，然后把结果交给你审查。

用一句话描述它：**Claude Code 是一个会自己思考、自己干活的编程 Agent，不是一个聪明的代码补全工具。**

理解这个差别，你就知道为什么它能让经验丰富的工程师的日均代码产出翻倍，而不只是少打几个字。

---

## 第一步：安装与登录（5 分钟）

Claude Code 的本体是一个命令行工具（CLI），编辑器里的扩展只是它的图形界面。所以不管你用什么编辑器，**都要先装 CLI**。

### 前置要求

- 一个终端（macOS/Linux 原生支持，Windows 推荐 WSL2 或 PowerShell）

### 安装命令

**macOS / Linux / WSL：**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell：**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD：**

```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

装完后验证：

```bash
claude --version
# 输出类似：claude-code 2.x.x
```

### 登录 Claude Code

Claude Code 目前主流有两种接入方式。

### 方式一：官方账号登录（推荐）

直接启动：

```bash
claude
```

第一次启动时会自动打开浏览器，要求你登录 Anthropic / Claude.ai 账号并授权。

> Claude Code 需要 Claude Pro（$20/月）及以上订阅才能使用。如果你是高频开发者，建议使用 Max 计划，它拥有更高的调用额度以及 Opus 模型访问权限。

---

### 方式二：通过 CC Switch 使用第三方 API

如果你已经有 OpenRouter、OpenAI、Gemini、DeepSeek 等平台的 API Key，可以通过 **CC Switch** 接管 Claude Code 的 Provider 配置，无需订阅 Claude Pro。

CC Switch 是目前最流行的 Claude Code 配置管理工具之一，支持：

* Claude Code Provider 热切换
* OpenRouter / Gemini / OpenAI / DeepSeek 等多供应商
* MCP Server 管理
* Skills 管理
* 本地代理与自动故障转移
* Claude Code / Codex / Gemini CLI 统一配置管理


#### 安装 CC Switch

Windows / macOS / Linux 都支持。

**桌面应用安装：**

访问官网下载对应平台的安装包。

相关链接：

- [CC Switch GitHub](https://github.com/farion1231/cc-switch)
- [CC Switch 官网](https://ccswitch.io/)

---

#### 启动 CC Switch

安装完成后直接运行：


首次启动后：

1. 选择 Claude Code
2. 添加 Provider
3. 填入对应 API Key
4. 选择默认模型
5. 一键应用配置

随后直接运行：

```bash
claude
```

Claude Code 就会自动通过你配置的第三方 API 工作。

---

#### 常见 Provider

CC Switch 内置了大量 Provider 预设，包括：

* OpenRouter
* OpenAI
* Gemini
* DeepSeek
* Groq
* Azure OpenAI
* Anthropic 官方
* 各类国内中转 API


## 第二步：三条路进入 Claude Code

装好之后，你有三种方式使用 Claude Code。根据你的工作习惯选一种，不必三种都试。

### 路线 A：纯命令行（最简洁）

打开终端，进入你的项目目录，直接运行：

```bash
cd ~/your-project
claude
```

你会看到一个交互式界面，可以开始输入任务了。这是最轻量的方式，也是最多高手使用的方式，因为命令行模式下所有功能都可以访问，没有任何阉割。

> **退出方式**：按 `Ctrl+D` 或输入 `/exit` 即可退出 Claude Code。

### 路线 B：VS Code 扩展（最友好）

在 VS Code 扩展市场搜索 **Claude Code**，安装官方扩展。安装后侧边栏会出现一个⚡图标，点击它就能打开 Claude Code 面板。

扩展相比纯 CLI 额外提供了：

- **可视化 Diff**：每次 Claude 修改文件，你能在面板里直接看到对比，一键接受或拒绝，不用手动 git diff
- **Checkpoint 回滚**：任何时刻都能把文件状态退回到之前的检查点，相当于有了时光机
- **@文件引用**：在对话里 @ 一个文件，Claude 会优先关注它

键盘快捷键：`Cmd+Esc`（Mac）/ `Ctrl+Esc`（Windows）在编辑器和 Claude 面板之间切换焦点。如果快捷键被系统占用，也可以点击侧边栏的 ⚡ 图标手动切换。

### 路线 C：在 Cursor 里使用

Cursor 本质上是基于 VS Code 的，所以 Claude Code VS Code 扩展可以直接装在 Cursor 里。搜索安装方式和 VS Code 一样。

**需要注意**：Cursor 自己也有 AI Agent 功能（用的是 Cursor 的模型），Claude Code 扩展是独立于它的另一套系统，两者互不干扰，可以共存。Claude Code 扩展适合做整体性的代码库任务，Cursor 原生 Agent 适合做光标位置的快速编辑。

---

## 第三步：跑你的第一个任务

现在进入你的项目目录，启动 Claude Code，给它布置第一个任务。

新手最常见的问题是"不知道怎么提问"。其实 Claude Code 对自然语言的理解能力相当强，你不需要什么特殊格式。但有一个坏习惯需要从一开始就避免：

❌ **不要这样提问**："帮我优化一下代码"

✅ **要这样提问**："src/utils/auth.ts 里的 `validateToken` 函数现在每次请求都会重新解析 JWT，帮我加上内存缓存，缓存有效期跟 token 过期时间保持一致"

越具体，结果越可预测。Claude Code 会自己去读文件、决定怎么改，但它需要知道你的目标是什么。

---

## 第四步：读懂模式切换，这是最关键的新手知识

Claude Code 有几种工作模式，理解它们能让你少踩 90% 的坑。

### Plan 模式 vs Auto 模式

**Plan 模式**是"先计划，再执行"。Claude 会先读你的代码，生成一份详细的行动计划，然后**停下来等你审批**，只有你点头之后才动文件。

**Auto 模式**是"边想边做"。Claude 会一边分析一边直接改，低风险操作自动执行，只有遇到它不确定的高风险操作才会停下来询问你。

**如何切换**：在命令行界面里，按 `Shift + Tab` 可以在模式之间循环切换，界面左下角会显示当前模式。

**新手建议**：任何时候你对任务范围不确定，或者要改的文件超过 2-3 个，优先使用 Plan 模式。你只需要多花 30 秒看一眼计划，就能避免"改完才发现方向错了"的情况。

一个经过验证的工作流：**先用 Plan 模式确认思路，满意后切换到 Auto 模式执行**。

### 为什么 Plan 模式不慢

新手经常觉得 Plan 模式"多了一步，太慢"，然后一直用 Auto。这个认知有个盲区：

Claude Code 在执行过程中可能会误解你的意图——"帮我重构这个模块"可能导致它动了你根本没打算改的文件。Auto 模式下，你发现这个问题时文件已经改了；Plan 模式下，你在改动发生前就能发现。**修改一个计划需要 5 秒，回滚一段代码需要 5 分钟。**

---

## 第五步：四个必须掌握的斜杠命令

Claude Code 有 50+ 个斜杠命令，但新手阶段只需要记四个，它们能覆盖你 80% 的使用场景。

### `/init` — 项目初始化

第一次在一个项目里用 Claude Code，先跑这个：

```
/init
```

Claude 会扫描你的整个代码库，生成一份 `CLAUDE.md` 文件，里面包含它对这个项目的理解：用了什么框架、目录结构是什么、有什么规范需要遵守。

这份文件会在每次会话开始时自动加载，相当于给 Claude 配备了一份永久备忘录。（CLAUDE.md 是第二篇的核心主题，这里先知道它的存在就好。）

### `/clear` — 清空上下文，重新开始

```
/clear
```

当你的一个任务做完，准备开始下一个完全不同的任务时，运行 `/clear`。

这会清空当前会话的所有历史记录，让 Claude 用干净的上下文处理新任务。**不清空的后果**：上一个任务的代码细节会继续占用上下文窗口，既浪费 token，又可能干扰新任务的判断。

经验法则：**每次换任务就 `/clear`，不要让不同任务的上下文混在一起。**

### `/compact` — 压缩上下文，保留记忆

```
/compact
```

当一个任务做了很长时间，上下文快满了但你还不想结束会话，用 `/compact`。

它会把历史对话压缩成摘要，保留重要的上下文信息，同时大幅减少 token 占用。和 `/clear` 的区别：`/clear` 是全部清空，`/compact` 是保留精华、去掉冗余。

你还可以告诉它重点保留什么：

```
/compact 保留所有关于数据库 schema 的讨论和已确定的 API 设计
```

经验法则：**上下文用量超过 80% 时，用 `/compact`；切换到不相关的新任务时，用 `/clear`。**

### `/cost` — 查看本次会话花了多少

```
/cost
```

这个命令显示当前会话累计消耗的 token 数量和估算费用。用完就知道心里有数，也能帮你养成及时 `/compact` 或 `/clear` 的好习惯。

---

## 实战演示：一个完整的新手任务流程

假设你是一名后端开发者，你的 Express.js 项目里有一个接口响应很慢，你想让 Claude Code 帮你排查。

**第一步**：进入项目，初始化（第一次用才需要）

```bash
cd ~/my-api-project
claude
```

进入 Claude Code 交互界面后，输入：

```
/init
```

**第二步**：切换到 Plan 模式，描述问题

```
[Shift+Tab 切到 Plan 模式]

GET /api/users 这个接口响应时间超过 2 秒。入口在 src/routes/users.ts，
数据库查询逻辑在 src/services/userService.ts。
帮我找出性能瓶颈并给出优化方案，先不要动代码。
```

**第三步**：看 Claude 的分析计划，确认方向

Claude 会读取相关文件，告诉你它发现了什么问题，打算怎么改。你看完计划，觉得方向对，回复"按这个方案执行"。

**第四步**：切换到 Auto 模式，让它执行

```
[Shift+Tab 切到 Auto 模式]

方案看起来没问题，开始执行。
```

**第五步**：在 VS Code 扩展里查看 Diff（如果你用扩展的话）

Claude 修改文件后，扩展面板里会展示具体改了什么。你逐一确认，满意就接受，不满意就拒绝并告诉它哪里需要调整。

**第六步**：任务完成，清空上下文

```
/clear
```

这一套流程走下来，你会发现 Claude Code 不是在帮你打字，而是在帮你思考和执行。你的角色是**决策者和审查者**，不是操作员。

---

## 新手必知的三个坑

**坑一：忘记清空上下文就开始新任务**

症状：Claude 的回答越来越奇怪，开始把两个无关任务混在一起分析。
解决：任何时候换任务，先 `/clear`。

**坑二：任务描述太模糊**

症状：Claude 改了一堆你没想到的地方，或者改的方向完全不对。
解决：描述任务时说清楚"在哪个文件、改什么函数、达到什么目标"。不确定范围时先用 Plan 模式。

**坑三：以为接受 Diff 就完事了**

症状：测试失败或者出现新 Bug，才发现之前没仔细看改了什么。
解决：Claude 是 AI，不是神。每次 Diff 至少花 30 秒看一眼主要改动，不要无脑点接受。Claude Code 本身也鼓励这个习惯——Plan 模式的存在就是为了让你有机会在改动发生前做判断。

---

## 小结

这一篇你学会了：

- Claude Code 是 Agent，不是补全工具，理解这个认知是用好它的起点
- 安装 CLI 是一切的基础，VS Code 扩展和 Cursor 都是它的上层界面
- Plan 模式先想后做，Auto 模式边做边问，新手多用 Plan
- 四个核心命令：`/init` 初始化、`/clear` 清空、`/compact` 压缩、`/cost` 查费用
- 任务描述要具体，Diff 要看，上下文要管理

---

**下一篇预告**：你已经能用 Claude Code 干活了。但你有没有发现一个问题——每次开新会话，Claude 对你的项目又"忘了个干净"？它不知道你们公司的代码规范，不知道你不喜欢用 `any` 类型，不知道这个项目的数据库是 PostgreSQL 而不是 MySQL。

第二篇的主题就是解决这个问题：**CLAUDE.md — 让 Claude 永远记住你的项目**。

---

*《Claude Code 从零到高手》系列 | 第一篇（共五篇）*