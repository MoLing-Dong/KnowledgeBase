# OpenAI Codex CLI 全平台安装指南（2026）
> 本文基于官方文档 developers.openai.com/codex 及 GitHub 仓库 openai/codex，覆盖 macOS、Windows、Linux 三大平台的所有安装方式，包含认证配置、运行模式说明、MCP 集成和常见问题排查。
## 一、什么是 Codex CLI
Codex CLI 是 OpenAI 开源的终端原生编程代理（Coding Agent），基于 Rust 构建，运行在你本地的终端里。它不是代码补全插件，而是一个能够：
- 读取整个代码仓库的上下文
- 跨多个文件提出修改方案
- 在沙箱环境内执行 shell 命令
- 通过审批工作流让开发者保持对每一步的控制
整个项目以 Apache 2.0 协议开源，托管于 [github.com/openai/codex](https://github.com/openai/codex)。
如果你需要云端版本（无需本地安装），可以访问 **chatgpt.com/codex** 直接使用 Codex Web。如果需要在 IDE 里集成，也可以安装 VS Code / Cursor / Windsurf 对应的插件版本。
## 二、前置条件
**账户要求**
使用 Codex CLI 需要满足以下之一：
- **ChatGPT Plus / Pro / Business / Edu / Enterprise** 订阅（通过浏览器登录认证，无额外费用）
- **OpenAI API Key**（按 token 计费，在 platform.openai.com 生成）
> ⚠️ **免费 ChatGPT 账户不包含 Codex CLI 访问权限。**
**npm 安装方式的额外依赖**
如果你选择 npm 安装（而非原生安装器），需要：
- Node.js 18 或更高版本（推荐 22+）
- npm 8 或更高版本
检查当前版本：
```bash
node --version
npm --version
```
如果版本不满足，推荐用 fnm 安装 Node.js，详见第三节。
## 三、安装前准备：Node.js 环境
Codex CLI 的原生安装器不依赖 Node.js，但如果你选择 npm 方式安装，或项目本身需要 Node.js 工具链，建议提前准备好运行时环境。
### 推荐使用 fnm 管理 Node.js
fnm（Fast Node Manager）是用 Rust 编写的 Node.js 版本管理器：
- 启动速度更快，无 Shell Hook 性能损耗
- Windows / Linux / macOS 三平台通用
- 资源占用低，配置简单
**安装 fnm**
Linux / macOS：
```bash
curl -fsSL https://fnm.vercel.app/install | bash
```
安装后**重新打开终端**，验证：
```bash
fnm --version
```
Windows（PowerShell）：
```powershell
winget install Schniz.fnm
```
### 安装 Node.js LTS
推荐安装当前 LTS（长期支持）版本：
```bash
fnm install --lts
fnm use lts-latest
fnm default lts-latest
```
验证安装：
```bash
node -v
npm -v
```
看到版本号输出即表示 Node.js 环境就绪。Codex CLI 要求 Node.js 18 以上，当前 LTS 版本满足此要求。
### 国内 npm 镜像配置
国内网络环境下，直接连接 npm 官方源（registry.npmjs.org）下载速度较慢，建议切换到淘宝镜像。
查看当前源：
```bash
npm config get registry
```
设置淘宝镜像：
```bash
npm config set registry https://registry.npmmirror.com
```
验证：
```bash
npm config get registry
# 输出：https://registry.npmmirror.com/
```
如需恢复官方源：
```bash
npm config set registry https://registry.npmjs.org
```
## 四、安装方式概览
| 方式                   | 平台                | 依赖        | 自动更新 | 推荐场景                  |
| ---------------------- | ------------------- | ----------- | -------- | ------------------------- |
| **原生安装器**（推荐） | macOS / Linux / WSL | 无          | ✅ 是     | 大多数用户首选            |
| **npm**                | 跨平台              | Node.js 18+ | ❌ 需手动 | 需要固定版本或 npm 工作流 |
| **Homebrew**           | macOS               | Homebrew    | ❌ 需手动 | 用 brew 统一管理工具链    |
| **winget**             | Windows             | Windows 10+ | ❌ 需手动 | 用 winget 管理软件        |
| **二进制直接下载**     | 跨平台              | 无          | ❌ 需手动 | 离线或受限环境            |
## 五、macOS 安装
### 方式 1：原生安装器（推荐）
打开终端，运行：
```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```
安装完成后打开新终端窗口，验证：
```bash
codex --version
```
原生安装器自动后台更新。如需手动触发更新：
```bash
# 重新运行安装脚本即可升级
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```
### 方式 2：Homebrew
```bash
brew install --cask codex
```
> ⚠️ 注意：`brew install --cask codex` 安装的是 **桌面应用**。如果你需要 CLI，推荐使用原生安装器或 npm 方式。
Homebrew 版本不自动更新：
```bash
brew upgrade codex
```
### 方式 3：npm
```bash
npm install -g @openai/codex
```
> ⚠️ **最常见的安装错误**：包名必须是 `@openai/codex`，不是 `codex`。npm 上有一个 2012 年的无关老项目叫 `codex`，装错了不会报明显错误，但完全无法使用。
升级：
```bash
npm install -g @openai/codex@latest
# 或
npm update -g @openai/codex
```
## 六、Windows 安装
Windows 上有两种运行方式，按需选择：
| 模式                | 沙箱支持               | 适合场景                       |
| ------------------- | ---------------------- | ------------------------------ |
| **原生 PowerShell** | 实验性（AppContainer） | Windows 原生项目               |
| **WSL 2**（推荐）   | 完整 Linux 沙箱        | Linux 工具链、需要稳定沙箱环境 |
### 方式 1：原生 Windows（PowerShell）
以普通用户身份打开 PowerShell（**不需要管理员权限**），运行：
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```
安装完成后验证：
```powershell
codex --version
```
> Windows 原生沙箱支持目前仍处于实验阶段，OpenAI 官方建议有 Linux 工具链需求的用户优先使用 WSL 2。
### 方式 2：WSL 2（推荐）
在 PowerShell（管理员）中安装 WSL：
```powershell
wsl --install -d Ubuntu
```
然后打开 Ubuntu 终端，运行 Linux 安装命令：
```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```
WSL 2 环境下 Codex CLI 可以使用完整的 Linux 沙箱（Landlock），功能最完整。
### 方式 3：WinGet
```powershell
winget install OpenAI.CodexCLI
```
WinGet 版本不自动更新，定期手动升级：
```powershell
winget upgrade OpenAI.CodexCLI
```
### 方式 4：npm
在 PowerShell 中（需要先安装 Node.js 18+）：
```powershell
npm install -g @openai/codex
```
## 七、Linux 安装
### 方式 1：原生安装器（推荐）
适用于 Ubuntu、Debian、Fedora 及其他主流发行版：
```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```
**无人值守安装**（CI/CD 环境）：
```bash
curl -fsSL https://chatgpt.com/codex/install.sh | CODEX_NON_INTERACTIVE=1 sh
```
### 方式 2：npm
先确保 Node.js 版本满足要求：
```bash
# Ubuntu / Debian
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs
# 然后安装 Codex
npm install -g @openai/codex
```

> ⚠️ **不要用 `sudo npm install -g`**。权限问题的正确解决方式是用 fnm 管理 Node.js（参见第三节），fnm 会将 Node.js 安装到用户目录，不需要 sudo。
### 方式 3：直接下载二进制
在网络受限或无法使用包管理器的环境，可以从 GitHub Releases 页面直接下载对应平台的二进制文件：
常用平台对应的二进制名：
| 平台    | 架构          | 文件名示例                 |
| ------- | ------------- | -------------------------- |
| Linux   | x86_64        | `codex-linux-x86_64`       |
| Linux   | ARM64         | `codex-linux-aarch64`      |
| macOS   | Apple Silicon | `codex-darwin-aarch64`     |
| macOS   | Intel         | `codex-darwin-x86_64`      |
| Windows | x86_64        | `codex-windows-x86_64.exe` |
下载后赋予执行权限并移到 PATH：
```bash
chmod +x codex-linux-x86_64
sudo mv codex-linux-x86_64 /usr/local/bin/codex
codex --version
```
## 八、验证安装
```bash
codex --version
```
更全面的诊断（检查安装、认证、运行时、Git、网络等）：
```bash
codex doctor
```
`codex doctor` 会生成一份本地诊断报告，是排查任何安装问题的首选工具。
## 九、认证登录
### 方式 1：ChatGPT 账户登录（推荐，订阅用户）
```bash
codex auth
```
运行后按提示选择「Sign in with ChatGPT」，会自动打开浏览器完成 OAuth 认证。凭据会存储到本地供后续会话复用。
需要 ChatGPT **Plus / Pro / Business / Edu / Enterprise** 订阅。
### 方式 2：OpenAI API Key（CI/CD 和无头环境）
```bash
export OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx
codex
```
或者写入 shell 配置文件（`~/.bashrc` / `~/.zshrc`）使其持久化：
```bash
echo 'export OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxx' >> ~/.bashrc
source ~/.bashrc
```
API Key 在 platform.openai.com 的 API Keys 页面生成，按 token 计费。
### 切换账户或退出
```bash
codex auth logout    # 清除本地凭据
codex auth           # 重新登录
```
## 十、运行模式
Codex CLI 提供三种自主程度不同的运行模式：
| 模式                | 文件修改 | 命令执行 | 适合场景               |
| ------------------- | -------- | -------- | ---------------------- |
| **Suggest**（默认） | 需审批   | 需审批   | 安全起步，每步都过一遍 |
| **Auto Edit**       | 自动执行 | 需审批   | 日常开发，信任文件修改 |
| **Full Auto**       | 自动执行 | 自动执行 | CI/CD 或完全自动化流程 |
启动方式：
```bash
# 交互式 TUI（默认 Suggest 模式）
codex
# Full Auto 模式
codex --full-auto "运行测试并修复所有失败"
# 仅读取，不修改（审计用）
codex -s read-only "分析这个函数的安全问题"
# CI 环境（完全绕过审批和沙箱，谨慎使用）
codex --dangerously-bypass-approvals-and-sandbox "更新 CHANGELOG"
```
> ⚠️ `--dangerously-bypass-approvals-and-sandbox` 仅用于完全隔离的 CI 环境，不要在本地开发机上使用。
在会话内切换模型：
```bash
/model    # 切换可用模型或调整推理级别
```
## 十一、沙箱机制
Codex CLI 在执行命令时默认启用 OS 级别沙箱，防止意外的文件系统写入和网络访问：
| 平台            | 沙箱技术                | 支持状态                            |
| --------------- | ----------------------- | ----------------------------------- |
| macOS           | Seatbelt（App Sandbox） | ✅ 完整支持                          |
| Linux           | Landlock + seccomp      | ✅ 完整支持（需 WSL 2 或原生 Linux） |
| Windows（原生） | AppContainer            | ⚠️ 实验性                            |
沙箱默认屏蔽网络访问。如果任务需要访问网络，需要显式配置 execpolicy 规则。
## 十二、项目配置文件
在项目根目录创建 `AGENTS.md`（或 `codex.md`），Codex 会在启动时自动读取，将其作为持久的项目级指令：
```markdown
# AGENTS.md
## 项目约定
- 使用 TypeScript strict 模式
- 所有函数必须有 JSDoc 注释
- 测试框架：Vitest
## 禁止操作
- 不要修改 src/legacy/ 目录下的文件
- 不要删除已有的 API 接口定义
```
全局配置文件位于 `~/.codex/config.toml`，可在其中设置默认模型、审批模式等偏好。
## 十三、MCP 集成
Codex CLI 支持 MCP（Model Context Protocol）服务器，可以与外部工具和服务集成。将 MCP 服务器配置添加到 `~/.codex/config.toml`：
```toml
[[mcp_servers]]
name = "my-server"
command = "npx"
args = ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
```
Codex CLI 本身也可以作为 MCP Server 运行，供其他 Agent 调用：
```bash
codex mcp    # 以 MCP server 模式启动（通过 stdio）
```
## 十四、IDE 集成
除了终端直接使用，Codex 也支持集成到主流编辑器：
**VS Code / Cursor / Windsurf**
在扩展面板搜索「Codex」，安装 OpenAI 官方版本。
**桌面应用**
运行以下命令打开或安装 Codex 桌面应用：
```bash
codex app
```
macOS 会直接打开桌面应用；Windows 会输出安装路径供下载。
## 十五、更新与卸载
### 更新
| 安装方式   | 更新命令                              |
| ---------- | ------------------------------------- |
| 原生安装器 | 重新运行安装脚本，或 `codex update`   |
| npm        | `npm install -g @openai/codex@latest` |
| Homebrew   | `brew upgrade codex`                  |
| winget     | `winget upgrade OpenAI.CodexCLI`      |
### 卸载
```bash
# npm 安装
npm uninstall -g @openai/codex
# 清除配置和凭据
rm -rf ~/.codex
codex auth logout    # 清除系统钥匙串中的凭据
# Homebrew
brew uninstall --cask codex
# 二进制安装
rm /usr/local/bin/codex
```
## 十六、常见问题
**Q：`codex: command not found`**
关闭当前终端，开一个新窗口重试。或运行 `codex doctor` 获取 PATH 修复建议。npm 安装的用户检查 `npm config get prefix`，把对应的 `bin` 目录加入 PATH。
**Q：装了 `codex` 包但运行没反应**
你可能装错了包。正确包名是 `@openai/codex`，不带 `@openai/` 前缀的 `codex` 是一个 2012 年的无关老项目。先卸载再重装：
```bash
npm uninstall -g codex
npm install -g @openai/codex
```
**Q：浏览器打开了但认证一直没完成**
可能是公司代理在拦截 TLS，OAuth 流程无法完成。改用 API Key 方式认证：
```bash
export OPENAI_API_KEY=sk-...
codex
```
**Q：Linux 上提示 Landlock/seccomp 不支持**
把 WSL 2 更新到最新版，或考虑在容器内运行。临时绕过（仅限隔离环境）：
```bash
codex --dangerously-bypass-approvals-and-sandbox "..."
```
**Q：免费 ChatGPT 账户能用吗？**
不行。至少需要 ChatGPT Plus（$20/月）或更高级别订阅，或者使用 OpenAI API Key（按量付费）。
**Q：沙箱阻止了网络访问**
沙箱默认屏蔽网络。需要网络的任务（如调用外部 API）要在 execpolicy 规则文件中显式放行对应域名，详见官方文档 Network Restrictions 章节。
## 参考资料
- 官方 CLI 文档：[developers.openai.com/codex/cli](https://developers.openai.com/codex/cli)
- 快速入门：[developers.openai.com/codex/quickstart](https://developers.openai.com/codex/quickstart)
- GitHub 仓库（开源）：[github.com/openai/codex](https://github.com/openai/codex)
- Codex Web（云端版）：[chatgpt.com/codex](https://chatgpt.com/codex)
- Windows 安装专项指南：[developers.openai.com/codex/windows-setup](https://developers.openai.com/codex/windows-setup)