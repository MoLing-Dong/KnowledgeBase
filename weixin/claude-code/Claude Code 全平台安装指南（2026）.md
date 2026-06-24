# Claude Code 全平台安装指南（2026）
> 本文基于官方文档 code.claude.com/docs/en/setup，覆盖 macOS、Windows、Linux 三大平台的所有安装方式，包含系统要求、认证配置、常见问题排查。
## 一、什么是 Claude Code
Claude Code 是 Anthropic 推出的终端原生 AI 编程助手。它不是浏览器里的聊天框，而是直接运行在你的开发环境里的代理（Agent）——能读取完整代码库、执行 shell 命令、跨多个文件做修改，全部通过自然语言驱动。
如果你更偏好图形界面，Anthropic 也提供了 **Claude Code 桌面应用**（macOS 和 Windows 可用），安装后无需打开终端即可使用。
## 二、系统要求
在安装之前，先确认你的环境满足以下条件：
**操作系统**
| 平台         | 最低版本                                     |
| ------------ | -------------------------------------------- |
| macOS        | 13.0 (Ventura) 及以上                        |
| Windows      | 10 (版本 1809) 或 Windows Server 2019 及以上 |
| Ubuntu       | 20.04 及以上                                 |
| Debian       | 10 及以上                                    |
| Alpine Linux | 3.19 及以上                                  |
**硬件**：RAM 4GB 以上，处理器支持 x64 或 ARM64
**网络**：需要全程联网（所有 AI 推理运行在 Anthropic 服务器上，本地只是轻量客户端）
**Shell**：支持 Bash、Zsh、PowerShell 或 CMD
**账户**：需要 Claude **Pro、Max、Team、Enterprise** 订阅，或 Anthropic Console API key。免费版 Claude.ai 账户**不包含** Claude Code 访问权限。
## 三、安装前准备：Node.js 环境
Claude Code 的安装器会自动处理大部分依赖，但对于开发者环境，建议提前准备好 Node.js 运行时。尤其是打算使用 npm 方式安装、或后续需要运行项目内 Node 工具链的用户。
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
看到版本号输出即表示 Node.js 环境就绪。
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
Claude Code 目前提供以下几种安装方式：
| 方式                   | 平台                          | 自动更新 | 适合场景                  |
| ---------------------- | ----------------------------- | -------- | ------------------------- |
| **原生安装器**（推荐） | macOS / Linux / WSL / Windows | ✅ 是     | 绝大多数用户首选          |
| **Homebrew**           | macOS                         | ❌ 需手动 | 习惯用 brew 管理工具链    |
| **WinGet**             | Windows                       | ❌ 需手动 | 习惯用 winget 管理软件    |
| **Linux 包管理器**     | apt / dnf / apk               | ❌ 需手动 | 服务器环境或系统级管理    |
| **npm（已不推荐）**    | 跨平台                        | ❌ 需手动 | 需要固定版本或 npm 工作流 |
| **桌面应用**           | macOS / Windows               | ✅ 是     | 不想用终端的用户          |
## 五、macOS 安装
### 方式 1：原生安装器（推荐）
打开终端（Terminal 或 iTerm2），运行：
```bash
curl -fsSL https://claude.ai/install.sh | bash
```
安装完成后，打开一个**新的终端窗口**，验证安装：
```bash
claude --version
```
原生安装器会在后台自动更新，无需手动维护版本。
### 方式 2：Homebrew
如果你用 Homebrew 统一管理开发工具，可以：
```bash
# 安装稳定版（比最新版延迟约一周，稳定性更好）
brew install --cask claude-code
# 或安装最新版
brew install --cask claude-code@latest
```
> ⚠️ Homebrew 版本**不会自动更新**，需要手动升级：
> ```bash
> brew upgrade claude-code
> # 或
> brew upgrade claude-code@latest
> ```
### 桌面应用（图形界面）
从官方页面下载 `.dmg` 文件，安装后在 Claude 桌面应用里切换到 Code 标签页即可使用。
## 六、Windows 安装
Windows 上有两种运行模式，按项目位置选择：
| 模式             | 前提条件                     | 沙箱支持 | 适合场景                   |
| ---------------- | ---------------------------- | -------- | -------------------------- |
| **原生 Windows** | 无（可选装 Git for Windows） | 不支持   | Windows 原生项目和工具链   |
| **WSL 2**        | 需启用 WSL 2                 | ✅ 支持   | Linux 工具链或需要沙箱隔离 |
### 方式 1：原生 Windows（PowerShell）
以普通用户身份打开 PowerShell（**无需管理员权限**），运行：
```powershell
irm https://claude.ai/install.ps1 | iex
```
### 方式 2：原生 Windows（CMD）
如果你在命令提示符（CMD）环境下：
```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```
> **如何判断自己在哪个终端？**
> - PowerShell：提示符显示 `PS C:\`
> - CMD：提示符显示 `C:\`（没有 `PS` 前缀）
**关于 Git for Windows（可选但推荐）**
安装 Git for Windows 后，Claude Code 可以使用 Bash 工具执行 shell 命令，能力更完整。未安装时则回退到 PowerShell 工具，大多数场景下也能正常工作。
如果 Claude Code 找不到 Git Bash，可以在 `~/.claude/settings.json` 中手动指定路径：
```json
{
  "env": {
    "CLAUDE_CODE_GIT_BASH_PATH": "C:\\Program Files\\Git\\bin\\bash.exe"
  }
}
```
### 方式 3：WSL（Windows Subsystem for Linux）
在 WSL 内部，Claude Code 使用 Linux 安装方式，不需要从 PowerShell 或 CMD 安装。
首先确保已安装并启用 WSL（推荐 WSL 2）：
```powershell
# 在 PowerShell 中（管理员权限）
wsl --install -d Ubuntu
```
然后打开 Ubuntu 终端，运行 Linux 安装命令：
```bash
curl -fsSL https://claude.ai/install.sh | bash
```
### 方式 4：WinGet
```powershell
winget install Anthropic.ClaudeCode
```
> ⚠️ WinGet 版本不自动更新，需定期手动升级：
> ```powershell
> winget upgrade Anthropic.ClaudeCode
> ```
### 桌面应用（图形界面）
从 claude.com/download 下载 Windows 安装包，安装后即可使用图形化界面，无需打开终端。
## 七、Linux 安装
### 方式 1：原生安装器（推荐）
适用于 Ubuntu、Debian、以及其他主流发行版：
```bash
curl -fsSL https://claude.ai/install.sh | bash
```
### 方式 2：系统包管理器
**Debian / Ubuntu（apt）**
Anthropic 提供了签名的 apt 仓库，由包管理器自动验证签名：
```bash
# 添加 Anthropic apt 源（按官方文档操作）
# 然后安装
apt install claude-code
```
**Fedora / RHEL（dnf）**
```bash
dnf install claude-code
```
**Alpine Linux（apk）**
Alpine 是 musl-based 发行版，需先安装必要依赖：
```bash
apk add libgcc libstdc++ ripgrep
```
然后在 `~/.claude/settings.json` 中添加配置，避免使用内置 ripgrep：
```json
{
  "env": {
    "USE_BUILTIN_RIPGREP": "0"
  }
}
```
再运行安装脚本：
```bash
curl -fsSL https://claude.ai/install.sh | bash
```
> **Linux 暂无桌面应用**，请使用终端 CLI 或 IDE 插件（VS Code / JetBrains）。
## 八、npm 安装（不推荐）
npm 安装方式仍然受支持，但 Anthropic 已将其标记为非主要路径。若你有特定原因（例如需要固定版本），可以使用：
```bash
npm install -g @anthropic-ai/claude-code
```
> ⚠️ 注意事项：
> - **不要使用 `sudo npm install -g`**，会导致权限问题
> - 如遇权限错误，用 fnm 安装 Node.js 后再装（fnm 会将 Node.js 安装到用户目录，不需要 sudo，参见第三节）
> - 升级时用 `npm install -g @anthropic-ai/claude-code@latest`，**不要用** `npm update -g`（无法保证拉到最新版）
> - 安装完成后若提示 `command not found`，需要将 npm 全局 bin 目录加入 PATH
## 九、验证安装
所有平台安装完成后，运行：
```bash
claude --version
```
看到版本号即表示安装成功。如果报 `command not found`，先关闭当前终端，重新打开一个新窗口再试。
更全面的诊断：
```bash
claude doctor
```
这个命令会检查 PATH、认证状态、网络连通性、最近的自动更新结果等，并给出修复建议。
## 十、认证登录
安装完成后，在项目目录下运行 `claude` 启动，首次启动会自动打开浏览器，引导你登录 Anthropic 账户完成授权。
**交互式登录（推荐，适合个人机器）**
```bash
claude
```
按照浏览器提示登录你的 Claude Pro/Max/Team/Enterprise 账户即可。
**API Key 方式（适合 CI/CD 和无头环境）**
如果运行环境没有浏览器，可以使用 API Key：
```bash
export ANTHROPIC_API_KEY=sk-ant-xxxxxxxx
claude
```
在 Anthropic Console（platform.claude.com）生成 API Key，注意 API 使用按 token 计费，独立于订阅配额。
**第三方云平台**
Claude Code 也支持通过以下云厂商的 API 接入，适合有企业合约的场景：
- Amazon Bedrock
- Google Vertex AI
- Microsoft Foundry
## 十一、IDE 集成
除了终端直接使用，Claude Code 还支持集成到主流 IDE：
**VS Code**
在扩展面板搜索 "Claude Code"，选择 Anthropic 发布的官方版本安装。扩展提供聊天面板、文件 @-mention、并行对话、原生 diff 视图等功能，无需切换终端窗口。
**JetBrains（IntelliJ IDEA、PyCharm、WebStorm 等）**
在插件市场搜索并安装 Claude Code 插件，在 IDE 内置终端中运行 Claude Code，变更以 IDE 的 diff 视图呈现。
## 十二、更新与卸载
### 更新
| 安装方式   | 更新命令                                          |
| ---------- | ------------------------------------------------- |
| 原生安装器 | 后台自动更新，或手动运行 `claude update`          |
| Homebrew   | `brew upgrade claude-code`                        |
| WinGet     | `winget upgrade Anthropic.ClaudeCode`             |
| npm        | `npm install -g @anthropic-ai/claude-code@latest` |
### 卸载
完整卸载分三层：**二进制文件、配置目录、IDE 插件**。缺少任何一步都会有残留。
```bash
# macOS / Linux（原生安装器）
# 按 code.claude.com/docs/en/setup 中的卸载说明操作
# npm 安装
npm uninstall -g @anthropic-ai/claude-code
rm -rf ~/.claude
# IDE 插件单独在各自的扩展/插件管理界面卸载
```
> 注意：Claude Code 桌面应用和 CLI 共用 `~/.claude/` 配置目录。只卸载 CLI 而保留桌面应用时，下次打开桌面应用配置会被重新创建。
## 十三、常见问题
**Q：`claude: command not found`**
先关闭当前终端，打开新窗口重试。还不行，运行 `claude doctor` 获取 PATH 修复建议；或重新运行安装脚本。
**Q：PowerShell 报 `'irm' is not recognized`**
说明你实际上在 CMD 而不是 PowerShell，换用 CMD 的安装命令，或切换到 PowerShell 终端。
**Q：npm 安装遇到权限错误**
不要用 `sudo npm install`，改用 fnm 管理 Node.js（参见第三节），问题根源在于 npm 全局目录的写权限。
**Q：免费账户能用吗？**
不行。Claude Code 至少需要 Claude Pro（$20/月）或更高级别的订阅，免费版不包含 CLI 访问权限。
**Q：Linux 有桌面应用吗？**
目前（2026 年 6 月）没有。Linux 用户请使用终端 CLI 或 IDE 插件。
## 参考资料
- 官方安装文档：[code.claude.com/docs/en/setup](https://code.claude.com/docs/en/setup)
- Claude Code 快速入门：[code.claude.com/docs/en/quickstart](https://code.claude.com/docs/en/quickstart)
- 终端入门指南：[code.claude.com/docs/en/terminal-guide](https://code.claude.com/docs/en/terminal-guide)
- 安装故障排查：[code.claude.com/docs/en/troubleshoot-install](https://code.claude.com/docs/en/troubleshoot-install)