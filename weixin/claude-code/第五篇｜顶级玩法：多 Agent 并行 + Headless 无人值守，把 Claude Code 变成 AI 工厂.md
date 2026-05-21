# 第五篇｜顶级玩法：多 Agent 并行 + Headless 无人值守，把 Claude Code 变成 AI 工厂

> **系列收尾**：前四篇让你掌握了 Claude Code 的全套基础——装好用起来、让它记住项目、把工作流变成命令、接上外部系统并自动触发。这最后一篇，是真正拉开差距的地方。绝大多数人到第四篇就停下来了；继续读完这篇的人，会明白"一个人用 Claude Code 做出团队产出"是什么意思。

---

## 你的时间，才是真正的瓶颈

用好 Claude Code 之后，你会发现一个新的瓶颈：

不是 Claude 不够聪明，也不是功能不够用——而是你在**等**。

等它写完这个模块，再等它写下一个。等它分析完这段代码，再等它提出修复方案。等一个 Agent 的任务跑完，才能布置下一个任务。

这是线性的工作模式。你在和一个极其聪明的工程师结对，但你们还是一前一后地干活。

**多 Agent 并行**打破这个线性——让多个 Claude 实例同时运行，各自处理独立的任务，你只需要在终点等待结果。

**Headless 无人值守**再进一步——让 Claude Code 在你不在的时候也能运转，你去吃饭、睡觉，回来看结果。

把这两个机制结合起来，加上合理的成本控制，你就有了一条真正的 AI 工厂流水线。

---

## 第一部分：多 Agent 并行的三种形态

### 形态一：手动并行会话 + Git Worktree（最稳定）

这是不依赖任何实验性功能、最稳定的并行方式。原理很简单：用 Git Worktree 给每个 Agent 开一个独立的工作目录，各自在自己的分支上工作，互不干扰。

```bash
# 为三个独立任务各创建一个 worktree
git worktree add ../project-auth     feature/user-auth
git worktree add ../project-payment  feature/payment-integration  
git worktree add ../project-dashboard feature/analytics-dashboard

# 在三个终端里分别启动 Claude Code
# 终端 1
cd ../project-auth && claude

# 终端 2
cd ../project-payment && claude

# 终端 3
cd ../project-dashboard && claude
```

每个会话只看到自己分支的文件。写入不会互相干扰。任务完成后，各自提 PR，你逐一 review 后合并。

**什么时候用这个模式**：三个功能相互独立、都需要较长时间开发、你希望它们同时推进。比如：同时开发多个 feature，或者同时跑几个不相关的重构任务。

**关于数据库冲突**：如果多个 worktree 共享同一个开发数据库，它们的测试数据和 schema 迁移会互相干扰。最简单的解决方案是给每个 worktree 配独立的数据库文件（SQLite 尤其简单）：

```bash
# 在每个 worktree 的 .env 文件里
DATABASE_URL=./db/dev-auth.sqlite    # auth worktree 专用
DATABASE_URL=./db/dev-payment.sqlite # payment worktree 专用
```

### 形态二：Subagents（主 Agent 派发子任务）

Subagent 是 Claude Code 内置的并行机制——主 Agent 把任务拆分，同时派发给多个子 Agent，每个子 Agent 在**完全独立的上下文窗口**里运行，完成后把结果汇报给主 Agent。

子 Agent 定义在 `.claude/agents/` 目录（也叫 AGENTS.md 格式）：

```yaml
# .claude/agents/code-reviewer.yaml
name: code-reviewer
description: >
  专业代码审查员。审查代码质量、安全漏洞、性能问题。
  当需要对多个文件或模块并行做 code review 时使用。
model: claude-sonnet-4-6
tools:
  - Read
  - Grep
  - Glob
system: |
  你是一名高级代码审查员。审查时重点关注：
  1. 安全漏洞（SQL 注入、XSS、硬编码密钥）
  2. 性能问题（N+1 查询、不必要的全表扫描）
  3. 错误处理遗漏
  以结构化 Markdown 输出，包含文件名和行号。
```

```yaml
# .claude/agents/test-writer.yaml
name: test-writer
description: >
  专业测试编写员。为给定函数或模块编写 Vitest 单元测试。
  当需要批量为多个模块补写测试时使用。
model: claude-haiku-4-5   # 写测试不需要 Sonnet，Haiku 够用
tools:
  - Read
  - Write
  - Bash
system: |
  你是一名测试工程师。为每个函数编写：
  - 正常路径测试
  - 边界条件测试
  - 错误路径测试
  文件放在与源文件同目录的 __tests__/ 下。
  使用 Vitest，不要用 Jest。
```

定义好之后，主 Agent 可以用自然语言触发并行执行：

> "用 code-reviewer 和 test-writer 两个子 Agent 并行处理 src/api/ 目录：code-reviewer 做安全审查，test-writer 为还没有测试的函数补写测试。"

**Subagent 的核心优势**：每个子 Agent 的上下文完全独立，它们看不到彼此，也看不到主 Agent 的完整历史。这既保证了并行执行的干净性，也避免了上下文污染。

**isolation: worktree 配置**：在 YAML 里加这一行，每次调用该子 Agent 时都会自动创建独立的 git worktree，彻底隔离文件系统操作：

```yaml
isolation: worktree
```

### 形态三：Agent Teams（实验性，Agent 间直接通信）

这是最新的实验性功能，允许多个 Agent 之间**直接互发消息**，而不是都通过主 Agent 中转。适合需要 Agent 之间相互协作的场景（比如一个 Agent 写代码，另一个实时 review，发现问题直接反馈给前者）。

开启方式：

```json
// settings.json 或 ~/.claude/settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

开启后，用自然语言描述团队结构：

> "创建一个三成员 Agent 团队：前端 Agent 负责 src/components/，后端 Agent 负责 src/api/，测试 Agent 负责 src/__tests__/。并行重构这三个模块，测试 Agent 在另外两个完成后做集成验证。"

在 tmux 或 iTerm2 里，每个成员会在独立分屏里运行，通过共享任务列表协调进度。

**⚠ 实验性功能注意**：Agent Teams 目前仍在快速迭代，稳定性不如前两种方式。生产任务优先用 Subagents 或手动 worktree，Agent Teams 适合用来探索和实验。

---

## 第二部分：Headless 模式——没有人的时候让 Claude 继续干

### 最简单的理解

Headless 模式就是一个标志位：`-p`（print）。

```bash
claude -p "检查 src/ 下所有函数是否有 JSDoc 注释，缺少的补上，然后提交"
```

输入这行命令，Claude 开始运行 Agent 循环——读文件、思考、调用工具、修改内容——直到任务完成或遇到它无法自行决定的情况才停下。你不需要守在旁边。

### 四个生产级必备标志

仅仅 `claude -p "..."` 在生产环境是不够的。一位工程师曾分享：第一次把 Claude Code 接进 GitHub Actions，它跑了 47 分钟，花了 18 美元，因为没有任何限制让它无限修改一个没人要求改的文件。以下四个标志解决这个问题：

**`--max-turns N`**：限制 Agent 最大轮次。防止任务跑偏后无限消耗。CI 场景建议 10-20：

```bash
claude -p "修复所有 TypeScript 类型错误" --max-turns 15
```

**`--output-format json`**：把输出结构化为 JSON，便于后续程序解析。对接 CI/CD 时这是刚需，不要用默认的纯文本：

```bash
RESULT=$(claude -p "检查安全漏洞" --output-format json)
ISSUES=$(echo "$RESULT" | jq -r '.result')
COST=$(echo "$RESULT" | jq -r '.cost.total_cost')
echo "发现问题：$ISSUES"
echo "本次花费：\$$COST"
```

**`--bare`**：跳过 `~/.claude/` 里的全局配置，从干净状态启动。CI 环境里不同 runner 的 `~/.claude/` 状态可能不一样，用 `--bare` 确保每次运行的行为可复现：

```bash
claude --bare -p "生成单元测试" --max-turns 10 --output-format json
```

**`--allowedTools Read,Write,Bash`**：明确声明允许使用的工具，最小权限原则。不需要网络工具的任务就不开网络工具：

```bash
claude -p "重构 auth 模块" \
  --allowedTools "Read,Write,Edit,Bash(npm run test)" \
  --max-turns 20 \
  --output-format json
```

### 实战：接入 GitHub Actions 做 PR 自动审查

这是最有实用价值的 Headless 场景——每次提 PR，自动触发 Claude Code 做代码审查，把结果作为评论发到 PR 上：

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run Claude Review
        id: review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          # 获取 PR 的 diff
          git diff origin/${{ github.base_ref }}...HEAD > /tmp/pr_diff.txt
          
          # 运行 Claude 审查
          RESULT=$(claude --bare -p "
            审查以下 git diff 里的代码改动，重点检查：
            1. 潜在的 Bug 和逻辑错误
            2. 安全漏洞（SQL 注入、XSS、硬编码密钥、权限缺失）
            3. 明显的性能问题
            
            只报告真实发现的问题，不要泛泛评论。
            每个问题注明文件名和行号。
            如果没有发现问题，直接说「未发现明显问题」。
            
            代码改动：
            $(cat /tmp/pr_diff.txt | head -500)
          " \
            --max-turns 5 \
            --output-format json \
            --allowedTools "Read")
          
          echo "review_result=$(echo "$RESULT" | jq -r '.result' | head -c 60000)" >> $GITHUB_OUTPUT

      - name: Post Review Comment
        uses: actions/github-script@v7
        with:
          script: |
            const review = `${{ steps.review.outputs.review_result }}`;
            await github.rest.issues.createComment({
              ...context.repo,
              issue_number: context.issue.number,
              body: `## 🤖 Claude Code 自动审查\n\n${review}\n\n---\n*由 Claude Code 自动生成，仅供参考*`
            });
```

这个 workflow 每次 PR 创建或更新时自动跑，5-15 秒出结果，把审查评论直接发到 PR 里。

### 其他常见 Headless 用途

**每晚自动检查文档是否和代码同步**（cron job）：

```bash
# crontab: 每天凌晨 2 点运行
0 2 * * * cd /your/project && claude --bare -p \
  "对比 README.md 里描述的 API 接口和 src/api/ 目录里的实际实现，列出不一致的地方" \
  --max-turns 8 \
  --output-format json \
  >> /logs/doc-sync-$(date +\%Y\%m\%d).log 2>&1
```

**依赖升级后自动跑兼容性检查**：

```bash
npm update
claude --bare -p "
  依赖刚刚升级过。运行测试套件，如果有失败找出原因，
  尝试修复兼容性问题，修复后重新跑测试，
  直到所有测试通过。最多尝试 3 轮。
" \
  --max-turns 25 \
  --allowedTools "Read,Write,Edit,Bash" \
  --output-format json
```

---

## 第三部分：权限模式的正确选择

Headless 场景最容易踩的坑是**权限配置**。选错了要么任务跑不起来，要么把生产环境搞坏。

### 五种模式速查

| 模式                | 触发方式                         | 行为                                  | 适合场景               |
| ------------------- | -------------------------------- | ------------------------------------- | ---------------------- |
| `default`           | 无额外标志                       | 每次敏感操作都暂停等待确认            | 日常交互开发           |
| `plan`              | `--permission-mode plan`         | 只能读，不能写，等你审批计划          | 不确定范围时的探索     |
| `autoEdit`          | `--permission-mode autoEdit`     | 文件编辑自动执行，Bash 命令仍需确认   | 只改代码不跑命令的场景 |
| `auto`              | 无（配置开启）                   | AI 分类器判断，低风险自动，高风险问你 | 绝大多数 CI 场景       |
| `bypassPermissions` | `--dangerously-skip-permissions` | 跳过**所有**权限检查，直接执行        | 隔离沙盒容器内         |

### 关于 `--dangerously-skip-permissions` 的真相

这是 Headless 场景最常被误用的标志。很多人把它当成"更方便的 auto 模式"，但两者的差距很大：

- `auto` 模式：背后有一个 AI 分类器，持续评估每个工具调用是否安全，不确定的操作还是会停下来问你
- `--dangerously-skip-permissions`：字面意思，跳过所有检查，Claude 决定做什么就直接做

**什么时候能用**：只有在**隔离沙盒容器**里，且容器内没有生产凭证、没有对生产系统的写权限、网络出口受限的情况下，才考虑用这个标志。

**生产 CI 的正确做法**：用 `auto` 模式配合 `settings.json` 里的 allow/deny 规则：

```json
// .claude/settings.json（提交到代码库）
{
  "permissions": {
    "allow": [
      "Bash(npm run test)",
      "Bash(npm run lint)",
      "Bash(npm run build)",
      "Bash(git diff*)",
      "Bash(git log*)",
      "Write(src/**)",
      "Write(tests/**)"
    ],
    "deny": [
      "Bash(rm -rf*)",
      "Bash(git push --force*)",
      "Bash(curl*)",
      "Bash(wget*)",
      "Write(.env*)",
      "Write(*.key)",
      "Write(*.pem)"
    ]
  }
}
```

deny 规则的优先级最高——**即使在 bypassPermissions 模式下，deny 规则依然生效**。这是最后一道防线，一定要配置。

---

## 第四部分：成本控制——并行越多，账单越猛，这样管

运行 10 个并行 Agent，token 消耗是 10 倍速。多 Agent 是效率杠杆，同时也是成本杠杆。没有成本意识地用多 Agent，账单会让你很快收手。下面是四套可以叠加使用的控制手段。

### 手段一：`opusplan` —— 计划用 Opus，执行用 Sonnet

这是 Claude Code 里最被低估的模型别名：

```bash
/model opusplan
# 或者启动时指定
claude --model opusplan
```

启用后：Plan 模式（架构分析、方案设计）自动使用 Opus；切换到执行模式后自动降回 Sonnet。你获得了 Opus 的推理质量用于做出正确的决策，但写代码的阶段用的是 Sonnet 的价格。

实测下来，对于"分析 + 实现"类任务，`opusplan` 比全程 Opus 便宜 50-70%，代码质量差别不大——因为真正需要 Opus 深度推理的，就是那个分析阶段。

### 手段二：子 Agent 按任务复杂度路由模型

在子 Agent 的 YAML 定义里指定模型，不同类型任务用不同档次：

```yaml
# 文档更新、注释生成 → Haiku 足够
name: doc-updater
model: claude-haiku-4-5

# 常规功能开发、测试编写 → Sonnet
name: feature-builder
model: claude-sonnet-4-6

# 架构分析、复杂重构 → Opus（或 opusplan）
name: architect
model: claude-opus-4-6
```

一个实测参考：把 80% 的子 Agent 任务路由给 Haiku，20% 给 Sonnet，同样的工作量，月费账单可以从 $80 降到 $24 左右（70% 下降）。

**注意**：不要把子 Agent 的 CLAUDE_CODE_SUBAGENT_MODEL 环境变量统一设为 Haiku——这个变量同时影响 planning agent，弱规划器会导致下游任务出现连锁错误。针对具体任务在 YAML 里单独指定才是正确方式。

### 手段三：`/effort` 调节推理强度

```bash
/effort low     # 简单任务，快速节省
/effort normal  # 默认
/effort high    # 复杂问题
/effort xhigh   # Opus 4.7 的最高推理（代价最大）
```

配合 `/model` 形成"二维控制"：Haiku + low effort 做文件格式转换，Opus + xhigh 做跨系统架构决策。中间的任务选中间的组合，不要一刀切。

### 手段四：`/cost` 实时监控，到点就 `/compact`

```bash
/cost
# 输出：Total tokens: 45,231 | Estimated cost: $0.42
```

养成**在 70% 上下文用量时主动 `/compact`** 的习惯，而不是等到接近上限才被迫压缩。接近上限的 Agent 不只是慢，输出质量也会下降——重要的规则和上下文开始被边缘化。

多 Agent 并行的场景里，每个 Agent 的 `/cost` 要单独看。一个跑偏的 Agent 可以在你没注意的时候消耗掉一个小时的预算。

---

## 第五部分：一个完整的高手工作流示意

把前面所有内容串起来，高手的一个典型工作日可能是这样的：

**早上上班前（Headless 夜间任务）**：

前一晚临下班前，跑一个 headless 任务：

```bash
claude --bare -p "
  分析今天新增的 GitHub Issue，找出可以直接修复的 Bug（不涉及架构变动的），
  每个 Bug 创建一个 worktree，在各自的分支上修复，跑测试，开 PR。
  最多处理 5 个 Issue。
" \
  --max-turns 50 \
  --allowedTools "Read,Write,Edit,Bash,Glob,Grep" \
  --output-format json
```

早上来公司，5 个 PR 已经在等你 review。

**上午的主线任务（opusplan + Plan 模式）**：

开始今天的核心功能开发：

```
/model opusplan
[Shift+Tab 切到 Plan 模式]

我要实现用户权限系统的 RBAC 升级，需要：
1. 修改 User model 支持多角色
2. 实现权限继承逻辑
3. 更新所有相关 API 的鉴权中间件
4. 迁移现有用户数据

这涉及多个模块，先给我完整的方案和改动影响分析。
```

Opus 分析，给出计划，你审批，切到执行模式，Sonnet 实现。

**中午：并行推进独立子任务**

主任务跑着，同时开两个 worktree：

```bash
git worktree add ../fix-api-docs  fix/api-documentation
git worktree add ../perf-db-query fix/db-query-performance
```

两个终端各起一个 Claude Code，分别处理文档和性能问题，用 Haiku 节省预算。

**下午：Stop Hook 自动验收**

通过前一篇配置好的 Stop Hook，每次主任务 Claude 完成一轮，自动跑测试。测试失败了 Claude 自动修，你只需要在测试全绿的时候确认合并。

**下班前：Headless 跑夜间任务**

再布置一批给夜间运行的任务，明天继续。

---

## 本篇小结，也是整个系列的终点

这一篇你掌握了：

**多 Agent 并行的三种形态**：
- 手动 Worktree + 多终端：最稳定，适合独立功能并行开发
- Subagents（`.claude/agents/`）：主 Agent 派发，各有独立上下文，支持 `isolation: worktree`
- Agent Teams（实验性）：Agent 间直接通信，适合协作性强的任务

**Headless 模式生产级用法**：
- 四个必备标志：`--max-turns`、`--output-format json`、`--bare`、`--allowedTools`
- GitHub Actions 集成模板（PR 自动审查）
- 夜间批量任务和 cron job 用法

**权限模式的正确选择**：
- 生产 CI 用 `auto` + allow/deny 规则，不要随便用 `--dangerously-skip-permissions`
- deny 规则在任何权限模式下都生效，是最后一道安全门

**四套成本控制手段叠加使用**：
- `opusplan`：计划用 Opus，执行用 Sonnet，一个别名省掉 50-70%
- 子 Agent 按任务路由模型：文档 → Haiku，开发 → Sonnet，架构 → Opus
- `/effort` 调节推理强度，按需选档
- `/cost` 实时监控，70% 上下文就 `/compact`

---

## 系列终：从工具到基础设施

回顾这五篇走过的路：

第一篇，你装好了 Claude Code，理解了它和 Copilot 的本质差别。
第二篇，你用 CLAUDE.md 给 Claude 配备了永久记忆。
第三篇，你把重复的工作流变成了一键触发的 Skills。
第四篇，你用 Hooks 和 MCP 让 Claude Code 自动连接内外世界。
第五篇，你让多个 Claude 实例并行运转，在你睡觉的时候也能产出。

这条路的终点，是把 Claude Code 从一个"智能工具"变成你的"开发基础设施"——就像你不会每次写代码都去想"我要不要用版本控制"，用好了 Claude Code，你也不会每次做任务都去想"我要不要让 AI 帮我"，它就在那里，自然地融入每一个环节。

达到这个程度需要时间，需要根据你自己的工作流调整配置，需要通过每一次 Claude 犯错来完善 CLAUDE.md，需要把你自己最痛的重复劳动变成 Skills。

但这个投入是值得的。而且越早开始，复利越多。

---

*《Claude Code 从零到高手》系列 | 第五篇（共五篇）*  
*完结*