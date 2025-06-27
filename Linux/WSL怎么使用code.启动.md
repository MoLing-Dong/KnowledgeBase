# WSL 如何配置下快速启动 VS Code

## ✅ 一、前提条件

确保你已完成以下安装：

| 组件                    | 是否必需 | 说明                                                                  |
| --------------------- | ---- | ------------------------------------------------------------------- |
| Windows 版 VS Code     | ✅    | 下载安装：[https://code.visualstudio.com](https://code.visualstudio.com) |
| VS Code Remote WSL 插件 | ✅    | 在 VS Code 中搜索插件 “**Remote - WSL**” 并安装                              |
| WSL 已启用               | ✅    | 安装 Ubuntu 子系统，例如：`wsl --install -d Ubuntu`                          |

## ✅ 二、WSL 中快速启动 VS Code 的方法

### 方法一：在 WSL 终端中使用 `code .`

打开你要编辑的目录，直接执行：

```bash
code .
```

* 作用：在当前 WSL 目录下用 VS Code 打开项目；
* `code` 会自动通过 WSL 通道启动 Windows 上的 VS Code 并进入该 WSL 环境；
* 第一次执行时可能需要初始化 VS Code Server。

### 如果 `code` 命令找不到？

请执行：

```bash
# 安装 VS Code 命令行启动器
export PATH="$PATH:/mnt/c/Users/你的用户名/AppData/Local/Programs/Microsoft VS Code/bin"
```

或者加到你的 `~/.bashrc` 中：

```bash
echo 'export PATH="$PATH:/mnt/c/Users/你的用户名/AppData/Local/Programs/Microsoft VS Code/bin"' >> ~/.bashrc
source ~/.bashrc
```

替换为你自己的 Windows 用户路径。然后重新尝试 `code .`。

✅ 三、设置一个命令别名让启动更快（可选）

你可以设置一个简短别名，例如 `vsc`：

```bash
echo "alias vsc='code .'" >> ~/.bashrc
source ~/.bashrc
```

以后只需要在 WSL 目录中运行：

```bash
vsc
```

即可打开当前目录到 VS Code。

## ✅ 四、在 Windows 桌面添加“一键打开某个 WSL 工程”的快捷方式（进阶）

可以创建一个 Windows 快捷方式用于一键打开某个路径下的 WSL 工程：

1. 在桌面新建快捷方式

2. 输入目标：

   ```powershell
   wsl ~ -e sh -c "cd /home/你的用户名/项目目录 && code ."
   ```

   示例：

   ```powershell
   wsl ~ -e sh -c "cd /home/moling/AITools && code ."
   ```

3. 命名为如“Open AiTools in VS Code”

## ✅ 五、通过 `.vscode/settings.json` 自定义 WSL 启动配置（项目内）

你也可以在项目内 `.vscode/settings.json` 中写入 WSL 专用配置，如：

```json
{
  "remote.WSL.fileWatcher.polling": true,
  "terminal.integrated.defaultProfile.linux": "bash"
}
```

---

## 🧩 提示

* 如果你需要从 Windows 打开任意 WSL 目录，也可以在地址栏输入：

  ```
  \\wsl$\Ubuntu\home\moling\AITools
  ```

  然后在资源管理器右键点击“在 VS Code 中打开”。

---

是否希望我为你生成一个完整的一键快捷方式命令或 `.bashrc` 自动启动 VS Code 的配置？也可以继续问我有关 VS Code + WSL 的调试、Python 环境配置等内容。
