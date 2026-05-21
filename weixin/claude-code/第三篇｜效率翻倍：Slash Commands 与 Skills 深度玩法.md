# 第三篇｜效率翻倍：Slash Commands 与 Skills 深度玩法

> **系列导读**：前两篇解决了"能用"和"记住你项目"的问题。这一篇进入真正的效率层——很多人用了六个月 Claude Code，依然只知道 `/clear` 和 `/compact`。本篇带你系统盘点那些被严重低估的内置命令，并手把手教你把自己的工作流变成一键指令。

---

## 一个让人意外的数字

Claude Code 目前内置了超过 60 个斜杠命令和捆绑 Skill。

绝大多数开发者常用的，不超过 5 个。

这不是因为其他命令没用，而是因为它们藏得够深——没有一个集中的"命令广场"，没有入门教程提到它们，很多甚至在官方文档里只有一行描述。

这篇文章的目标就是把这些被遗忘的命令捞出来，告诉你它们分别在什么时候能救你的命。

---

## 一、先理解命令的分类：三种不同的东西

很多人被 Claude Code 的命令体系搞晕，是因为它混合了三种不同性质的东西，却都用 `/` 触发：

**内置命令（Built-in Commands）**：直接控制会话本身的命令，比如 `/clear`、`/compact`、`/model`、`/fork`。这些是 Claude Code 程序本身的功能，执行的是确定性操作，不消耗 AI 推理。

**捆绑 Skills（Bundled Skills）**：Anthropic 随 CLI 预装的 AI 驱动工作流，比如 `/review`、`/debug`、`/simplify`、`/batch`、`/loop`。这些本质上是预写好的"技能包"，调用后 Claude 会按照特定的专家思维框架来执行任务。

**自定义命令 / Skills（Custom）**：你自己创建的，存放在 `.claude/skills/` 或 `.claude/commands/` 目录里，团队共享或个人私用。这是本篇后半段的重点。

理解这三层之后，再看命令就不会迷失了。

---

## 二、被你忽视的内置命令精选

### `/fork` —— 给当前会话开个分叉

这是使用率最低、价值最高的内置命令之一。

当你做到一半，想试一个风险较大的方案，但又不想毁掉现在的进度时，用 `/fork`：

```
/fork
```

这会在当前状态创建一个会话的副本。你可以在副本里大胆实验——如果实验成功，继续；如果失败，副本直接丢弃，主会话完好无损。

**对应的 git 操作**：这就是会话级别的 `git branch`，只是针对的是 Claude 的对话状态，而不是代码状态。

用 `/fork` 之后，你可以用 `--resume` 加会话 ID 恢复到任意分叉点，然后 `--fork-session` 再开一个新分支。多个方案并行探索，保留最好的那个。

### `/btw` —— 插嘴问一句，不打断主线

这是 2026 年 3 月才加入的命令，发布时那条推文获得了 220 万次浏览，因为它解决了一个无处不在的痛点：

你让 Claude 在做一个复杂任务，它正在执行，你突然想起来要问它一个问题——但你不想打断正在进行的任务，也不想让这个问题的内容污染主线上下文。

```
/btw 刚才你提到的 auth middleware，它现在在哪个文件里？
```

`/btw` 的特点：**不保存到上下文、不消耗工具调用、只从已有的对话记忆里回答**。回答完毕后按空格或 Enter 关掉，主线继续。

配合 `Ctrl+B`（把当前任务推到后台运行）使用效果最好：把耗时任务放后台，用 `/btw` 在等待过程中处理小问题，用 `Ctrl+T` 查看后台任务进度。这套组合把等待时间利用起来了。

### `/rewind` —— 反悔专用，代码和对话可以分开回滚

```
/rewind
```

这个命令让你把会话回滚到某个历史检查点，有两种模式：

- **代码 + 对话全部回滚**：回到那个时间点，一切重来
- **只回滚代码，保留对话**：这个是关键——你可以在保留讨论记录的前提下撤销所有文件改动

实际场景：Claude 做了一轮激进重构，你看完结果觉得思路对但实现有问题，想让它用不同方式重来。选"只回滚代码"，对话里的上下文还在，直接说"按这个思路但换一种实现方式"。

VS Code 扩展里的 Checkpoint 功能本质上也是这个机制的图形界面版。

### `/effort` —— 调节 Claude 的"用力程度"

这个命令鲜为人知，但能显著影响响应质量和消耗：

```
/effort low        # 快速、节省 token，适合简单任务
/effort normal     # 默认级别
/effort high       # 更深入的推理，适合复杂问题
/effort xhigh      # 最高推理强度，Opus 4.7 引入
```

配合 `/model` 一起用，形成"二维成本控制"：Haiku + low effort 做简单文件操作，Opus + xhigh 做架构分析。用对了，同样的工作量账单可以差出好几倍。

### `/batch` —— 批量任务分发，一句话拆出多个并行 Agent

```
/batch 把 src/components/ 下的所有类组件重构为函数式组件
```

这个命令会：
1. 扫描代码库，把工作拆分成 5～30 个独立单元
2. 展示给你一份拆分计划
3. 经你确认后，为每个单元分别启动一个后台 Agent 在隔离的 git worktree 里执行
4. 每个 Agent 完成后开 PR，你逐一审查

这是不需要手写任何 Agent 编排代码，就能实现并行任务分发的最简方式。适合大范围的机械性重构任务。

### `/loop` —— 让 Claude 循环执行，直到条件满足

```
/loop 5m 检查 staging 环境的 deploy 是否完成
```

以固定间隔重复执行一个任务，直到你手动停止或会话结束。每次迭代都有独立的上下文，不会累积膨胀。

实际用途：部署后持续监控状态、长时间跑测试后每隔几分钟检查结果、等待某个 API 返回特定状态。

### `/fork` + `/security-review` 的组合用法

上线前的标准动作：

```
/fork
/security-review
```

先 fork 会话（确保主线不被污染），再运行 `/security-review`。它会扫描当前分支的 git diff，检查注入漏洞、认证问题、密钥硬编码、数据暴露等安全隐患，输出带文件名和行号的结构化报告。

修复完之后，把 fork 丢掉，回到主线继续。

---

## 三、三个值得深入学习的捆绑 Skills

### `/debug` —— 不只是"帮我看看 bug"

捆绑的 `/debug` Skill 不是让你把错误贴进去然后等 Claude 猜——它有一套固定的诊断方法论：

**复现** → **隔离** → **提出假设** → **验证** → **修复**

每个步骤都有对应的工具调用：先读相关文件，再看报错栈，再看测试，再提出至少两个可能的原因并分别验证。

直接说"帮我 debug 这个错误"，Claude 可能直接猜一个原因然后改代码。用 `/debug`，它会系统地走完诊断流程，找出**真正的根因**，而不是第一个看起来像的原因。

### `/simplify` —— 扫描复杂度，不是格式化代码

很多人以为 `/simplify` 是美化代码格式用的，其实完全不是。

它做的是：扫描最近改动的代码，找出**重复逻辑、过度抽象、不必要的复杂度**，然后进行简化重构。

一个判断标准：三行相似代码可以接受，不需要抽象；同样的逻辑出现四次以上，才考虑提取。过度抽象往往比重复代码更难维护。`/simplify` 的判断基准接近这个经验值。

### `/review` —— 可以被你自定义覆盖的 Code Review

内置的 `/review` 已经很好用，但真正的高手玩法是**用自定义版本覆盖它**：

创建 `.claude/skills/review/SKILL.md`，在里面写上你们团队特有的审查标准——检查是否使用了项目里的封装中间件、是否符合团队 API 设计规范、是否有特定的安全要求……

这样同一个 `/review` 命令，在你的项目里执行的是你的标准，而不是 Claude 默认的通用标准。

---

## 四、Skills 是什么：比命令更聪明的触发方式

理解了内置命令之后，我们来讲 Skills——这是 Claude Code 里最强大、最被低估的扩展机制。

**命令 vs Skills 的核心区别**：

命令等你输入 `/xxx` 才执行。Skills 可以**由 Claude 自己判断触发**——当你描述的场景匹配了某个 Skill 的触发条件，Claude 会主动加载并使用它，你甚至不用知道这个 Skill 的存在。

举个例子：你安装了一个"安全审查" Skill，描述里写着"当用户提到要上线、准备提 PR、或讨论安全相关话题时触发"。下次你说"这个功能差不多了，我们准备提 PR 吧"，Claude 就会自动运行安全检查，不需要你手动调用 `/security-review`。

这就是 Skills 的核心价值：**把你的工作流变成 Claude 的本能反应**，而不是你每次都要记得喊一声的命令。

---

## 五、Skills 的文件结构

每个 Skill 住在一个独立的目录里：

```
.claude/skills/
└── my-workflow/
    ├── SKILL.md          ← 必须，Skill 的大脑
    ├── template.md       ← 可选，模板文件
    ├── examples/         ← 可选，示例
    └── scripts/          ← 可选，脚本文件
```

`SKILL.md` 分两部分：

```markdown
---
name: pr-prep
description: 准备提交 PR 时使用。当用户说"准备 PR"、"提 PR"、"代码写完了"等时触发，对代码做全面的 PR 前检查。
allowed-tools: Read, Bash, Grep
disable-model-invocation: false
---

# PR 前检查清单

按以下顺序执行检查：

## 1. 代码质量
运行 `pnpm lint` 并修复所有警告。
检查有没有 console.log、TODO 注释遗留在代码里。

## 2. 测试
运行 `pnpm test` 确保全部通过。
如果新增了功能但没有对应测试，提醒用户补上。

## 3. 安全
检查是否有硬编码的密钥或敏感信息。
验证所有用户输入都经过了 src/lib/sanitize.ts 里的处理。

## 4. 输出
把检查结果汇总成 PR description 草稿，包括：改了什么、为什么改、测试说明。
```

**Frontmatter 关键字段解释**：

- `name`：Skill 的内部名称，也是斜杠命令名（`/pr-prep`）
- `description`：**这是触发器，不是说明文档**。Claude 用这个字段做模糊匹配来判断何时激活 Skill。写得越精准，触发越可靠
- `allowed-tools`：这个 Skill 可以使用哪些工具，做权限控制
- `disable-model-invocation: true`：设置为 true 后 Claude 不会自动触发，只能手动 `/xxx` 调用

**最重要的坑**：`description` 字段不是给人看的描述，它是给 Claude 看的触发信号。写成"这个 Skill 用于 PR 准备"是无效的——太模糊。要写成"当用户说准备提 PR、代码差不多了、要上线了这类话语时触发"，让 Claude 知道具体什么场景下该用它。

---

## 六、三个可以直接复制使用的 Skills 模板

### Skill 1：每日站会摘要

```markdown
---
name: standup
description: 生成今日站会内容。当用户说"写站会"、"今天做了什么"、"daily"时触发。
allowed-tools: Bash
---

根据今天的 git 提交记录生成站会摘要：

1. 运行 `git log --since="24 hours ago" --author="$(git config user.name)" --oneline`
2. 把每条提交整理成人话，去掉技术细节
3. 按以下格式输出：

**昨天完成**：
- [从 commit 提炼]

**今天计划**：
- [根据最近的 PR 或 issue 推断，或留空让用户填写]

**是否有阻塞**：无（如有请手动补充）

语言风格：简洁直接，适合团队内部同步，不超过 5 条。
```

创建路径：`.claude/skills/standup/SKILL.md`

### Skill 2：智能提交（commit + 推断类型 + 写中文信息）

```markdown
---
name: smart-commit
description: 生成并执行 git 提交。当用户说"提交"、"commit"、"帮我提一下"时触发。
allowed-tools: Bash, Read
disable-model-invocation: false
---

执行提交流程：

1. 运行 `git diff --staged`，如果暂存区为空，先运行 `git add -p` 让用户选择要暂存的内容（或直接 `git add .` 如果用户说"全部提交"）
2. 分析改动，确定提交类型：
   - feat：新功能
   - fix：Bug 修复
   - refactor：重构（不改变行为）
   - docs：文档
   - test：测试
   - chore：配置/依赖/构建

3. 生成中文提交信息，格式：`类型: 简短描述（不超过 50 字）`

4. 如果改动复杂，在提交信息 body 里补充一句"为什么这样改"

5. 执行 `git commit -m "生成的信息"`

6. 告诉用户提交成功，显示 commit hash
```

创建路径：`.claude/skills/smart-commit/SKILL.md`

### Skill 3：功能完成后的全流程检查（改代码 → 跑测试 → 提交一条龙）

这是最能体现 Skills 威力的例子——把一个多步流程打包成一个触发：

```markdown
---
name: done
description: 功能开发完成后的收尾流程。当用户说"做完了"、"写完了"、"功能完成"时触发。
allowed-tools: Bash, Read, Write
---

按顺序执行以下收尾步骤：

## Step 1：静态检查
```bash
pnpm lint
pnpm typecheck
```
如果有错误，先修复，再继续。

## Step 2：运行测试
```bash
pnpm test --run
```
如果测试失败，分析原因，修复后重新跑，直到全部通过。

## Step 3：检查遗留物
- 搜索代码里的 console.log、debugger、TODO、FIXME
- 检查有没有硬编码的测试数据或临时变量名（如 `tmp`、`test123`）

## Step 4：生成提交
按照 smart-commit 的规范，分析改动，生成提交信息并执行提交。

## Step 5：汇报
输出一份简短的完成报告：
- 改动了哪些文件
- 通过了哪些测试
- 提交 hash
- 是否有需要人工关注的地方
```

创建路径：`.claude/skills/done/SKILL.md`

之后你只需要说一句"写完了"，Claude 会自动走完 lint → typecheck → test → commit 整个流程，你去喝咖啡等结果就行。

---

## 七、个人 Skills vs 团队 Skills：两个存放位置

**项目级（团队共享）**：
```
your-project/
└── .claude/
    └── skills/
        └── your-skill/
            └── SKILL.md
```
提交到代码库，所有团队成员共享。适合：项目专属的审查规范、部署流程、PR 模板生成等。

**全局级（个人私用）**：
```
~/.claude/skills/
└── your-skill/
    └── SKILL.md
```
不提交任何代码库，只在你自己的机器上生效。适合：个人工作习惯、私人模板、跨项目通用的小工具。

两者可以共存，同名的情况下项目级优先。

---

## 八、用 Skill Creator 生成 Skill，比自己写更快

如果你不确定怎么写 SKILL.md，有个更简单的路：让 Claude Code 帮你写。

```
/plugin install skill-creator@anthropic-agent-skills
```

安装后，描述你想自动化的工作流：

> "我想创建一个 Skill，每次我说'帮我写测试'的时候，Claude 会先分析目标函数的边界条件，然后用 Vitest 写覆盖正常路径、边界情况、错误情况的测试，文件放在同目录的 `__tests__/` 下。"

Skill Creator 会通过交互式问答，生成完整的目录结构和 SKILL.md 文件，还会自动测试 description 的触发准确率，告诉你触发词需不需要调整。

---

## 九、一个完整的 Skills 工作流实战

假设你在开发一个 API 功能，下面是 Skills 生效后的实际体验：

你打开会话，说："我要给 `/api/invoices` 加一个批量删除的接口，软删除，要记录操作日志。"

→ Claude 读取 CLAUDE.md，知道技术栈和规范
→ 进入探索，读取现有的 invoice 相关代码
→ 提出方案，等你确认
→ 开始实现

实现过程中你说："顺便确认一下，我们的日志写入是用 `src/lib/logger.ts` 里的那个对吧？"

→ `/btw src/lib/logger.ts 的接口是什么？` 快速查询，不污染主线

实现完成，你说："写完了。"

→ 触发 `done` Skill：自动跑 lint → typecheck → test → 生成提交信息 → 提交

你说："准备提 PR 了。"

→ 触发 `pr-prep` Skill：安全检查 → 生成 PR description 草稿

整个过程你没有手动调用过任何 Skill，也没有重复解释过任何规范。Claude 自己判断何时该做什么，你只是在对话。

---

## 本篇小结

这一篇你掌握了：

- 命令体系的三层结构：内置命令（确定性操作）、捆绑 Skills（AI 工作流）、自定义 Skills（你的专属指令）
- 被低估的内置命令：`/fork`（会话分叉）、`/btw`（插嘴不打断）、`/rewind`（代码和对话分别回滚）、`/effort`（调节推理强度）、`/batch`（并行任务分发）、`/loop`（定时循环执行）
- Skills 和命令的本质差别：Skills 可以由 Claude 自动判断触发，而不需要手动调用
- SKILL.md 的核心是 frontmatter 里的 `description`——它是触发器，不是说明文档
- 三个可直接复制的 Skill 模板：站会摘要、智能提交、功能完成一条龙
- 个人 Skills 放 `~/.claude/skills/`，团队 Skills 放 `.claude/skills/`

---

**下一篇预告**：你现在能让 Claude 响应你的命令，能让它自动触发工作流。但它还是被动的——等你说了什么，它才做什么。

如果你想让它在你**没有说任何话的时候也能自动执行**——比如文件保存后自动运行 lint，编译完成后自动检查类型，Claude 本身完成任务后自动写日志——那就需要第四篇的内容：

**Hooks + MCP，让 Claude Code 成为真正的自动化流水线。**

---

*《Claude Code 从零到高手》系列 | 第三篇（共五篇）*