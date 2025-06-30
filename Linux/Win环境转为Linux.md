# 从 Windows 迁移到 Linux：一位开发者的实战指南

随着 DevOps、云原生和容器技术的普及，越来越多开发者意识到在 Linux 下工作的效率与一致性优势。本文将从开发者视角出发，详尽阐述如何将开发环境从 Windows 平滑过渡到 Linux，包括系统选择、工具替代、常见工作流迁移、开发效率优化等方面，帮助你真正用好 Linux。

---

## 一、为何从 Windows 迁移到 Linux？

起因是因为有的项目在 Windows 下运行会出现兼容性问题，而且MAC环境作为开发环境的成本较高，Linux 成为越来越多开发者的首选。
在许多开发者心中，Windows 曾是 GUI 工具和软件兼容性的首选。然而，现代软件开发需求已发生巨大变化：

* **容器与部署环境统一**：大多数生产服务器基于 Linux，开发环境同步有助于减少 “It works on my machine” 问题。
* **终端为王**：现代开发习惯越来越偏向 CLI 工具，而 Linux 拥有天然的命令行优势。
* **开源生态成熟**：Linux 拥有庞大的包管理器与社区支持，易于获取、编译与调试各类工具。

## 二、系统安装与初始配置

### 2.1 选择合适的发行版

对于开发者而言，**Ubuntu LTS** 是最稳妥的选择：社区活跃、文档齐全、APT 包管理器简洁易用。

如你偏好滚动更新和高度自由控制，可以选择 **Arch Linux** 或 **Manjaro**，前者适合精通者，后者为更友好的桌面变体。

### 2.2 安装方式建议

* 使用 **虚拟机（VMware/VirtualBox）**：适合尝试阶段；
* 推荐使用 **WSL2** 作为中间过渡方案，但长期仍建议裸机部署获得原生体验。

目前我选择的还是WSL2，因为我还要小小的玩一下游戏

### 2.3 初始配置

现在的WSL2环境，已经非常成熟了，而且微软已经开源相关技术
目前主要的的基本就是SSH的安装和用户的配置

## 三、开发工具替代与等效迁移

### 3.1 编辑器与 IDE

| Windows 工具     | Linux 替代/一致工具    |
| -------------- | ---------------- |
| VS Code        | VS Code（原生支持）    |
| Notepad++      | Gedit, Kate, Vim |
| Powershell ISE | Bash + Oh My Zsh |

VS Code 在 Linux 上几乎无差别体验，推荐配合插件使用远程开发（Remote SSH, Dev Containers）。

### 3.2 开发语言环境

* Node.js: 使用 `fnm` 管理多版本；
* Python: 使用 `anaconda`；
* Java: 安装 OpenJDK，建议使用 `sdkman` 管理版本；
* Go/Rust: 使用各自官方安装方式，Linux 下原生支持更佳。

### 3.3 服务支持

这里非常推荐Docker，简单易用 ，需要注意Run的时候的配置映射

## 四、CLI 工具与工作流迁移

### 4.1 终端体验优化

安装 `zsh` + `Oh My Zsh` 或 `fish shell`：

```bash
sudo apt install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

配合 `tmux`、`bat`、`fzf`、`ripgrep` 等工具，打造强大终端工具链。

### 4.2 Git 与版本控制

Linux 下 Git 功能无差异，但建议配置 GPG 签名、SSH Key、Alias 提升效率：

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global alias.lg "log --oneline --graph --all"
```

## 五、常见开发场景迁移示例

### 5.1 本地调试与服务启动

在 Linux 下，服务启动可使用 `systemctl` 或 `supervisor`：

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

### 5.2 VSCode Remote WSL开发

在 WSL2-Windows 环境下，VS Code 可以直接通过 Remote - WSL 插件连接到 Linux 子系统：

### 5.3 文件搜索/替换与批量处理

替代 Windows 下 Total Commander 等工具：

```bash
# 查找包含关键字的文件
rg "关键字" ./src

# 批量替换
sed -i 's/old/new/g' *.js
```

---

## 六、迁移过程中可能遇到的问题

| 问题                    | 解决方案                              |
| --------------------- | --------------------------------- |
| 外接硬盘读写 NTFS 性能差       | 使用 `ntfs-3g` 驱动，尽量格式化为 ext4       |
| 中文显示/输入异常             | 安装 `fonts-noto-cjk` + Fcitx5 输入法  |
| 无法运行 .exe/.msi 程序     | 使用 Wine，仅建议必要场景使用                 |
| 驱动兼容性问题（Nvidia/Wi-Fi） | 使用官方驱动安装工具 `ubuntu-drivers`       |
| 软件找不到安装包              | 学习使用包管理器 (`apt`/`snap`/`flatpak`) |

---

## 七、结语：从工具到思维的转变

从 Windows 迁移到 Linux，不仅是工具链的变化，更是开发思维的重构：

* 从 GUI 驱动转向 CLI 驱动；
* 从依赖 IDE 转向依赖脚本与自动化；
* 从封闭环境转向开源、可控、高效的开发生态。

这条路一开始可能不那么轻松，但当你掌握 Linux 的节奏之后，它将极大提升你的开发效率与系统掌控力。

---

## 附录：推荐软件与资源列表

* Terminal：Alacritty / Kitty / Tilix
* IDE：VSCode、Neovim、JetBrains 系列
* 包管理工具：`apt`、`snap`、`flatpak`、`brew`
* 必备 CLI 工具：`htop`、`ncdu`、`jq`、`fd`、`exa`、`tldr`
