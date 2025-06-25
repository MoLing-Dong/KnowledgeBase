# nvm-for-windows 安装及使用

要注意 nvm-for-windows 虽然是和 nvm 同名，但是并不是同一个项目，nvm-for-windows 是一个独立的项目。

nvm-for-windows 的开发团队现在已经全职开发 RunTime，它是 NVM for Windows 的后继者。
[详情](https://github.com/coreybutler/nvm-windows/wiki/Runtime)

但是目前的 nvm-for-windows 依旧是可以使用的，所以这里还是介绍一下 nvm-for-windows 的安装及使用

## 用前须知

使用之前请删除之前安装的 nodejs，否则可能会出现问题

## 安装

直接下载安装包安装即可`nvm-setup.exe`

[下载链接](https://github.com/coreybutler/nvm-windows/releases)  
之后打开一路下一步即可

## 配置 Node.js

`nvm list available`，查看可用的 Node.js 版本我选择的是 LTS18.18.0
所以就直接`nvm install 18.18.0`，安装完成之后就是默认使用了

## 切换版本

`nvm list`，查看已安装的版本，然后`nvm use 版本号`，切换版本,非常简单

## 注意

每个版本之间所安装的包是不共享的，所以每次切换版本之后都需要重新安装依赖

## 用法

Windows 版 NVM 是一个命令行工具。nvm 只需在控制台中键入即可获取帮助。基本命令是：

`nvm arch [32|64]`：显示节点是否在 32 位或 64 位模式下运行。指定 32 或 64 以覆盖默认体系结构。  
`nvm check`：检查 NVM4W 进程是否存在已知问题。  
`nvm current`：显示当前版本。  
`nvm install <version> [arch]`：版本可以是特定版本，“latest”表示最新的当前版本，“lts”表示最新的 LTS 版本。可选择指定安装 32 位还是 64 位版本（默认为系统 arch）。将 [arch] 设置为“all”以安装 32 和 64 位版本。添加--insecure 到此命令的末尾以绕过远程下载服务器的 SSL 验证。  
`nvm list [available]`：列出 node.js 安装。在末尾键入 available 以显示可供下载的版本列表。  
`nvm on`：启用 node.js 版本管理。  
`nvm off`：禁用 node.js 版本管理（不卸载任何东西）。  
`nvm proxy [url]`：设置用于下载的代理。留空[url]以查看当前代理。设置[url]为“none”以删除代理。  
`nvm uninstall <version>`：卸载特定版本。  
`nvm use <version> [arch]`：切换使用指定版本。可以选择使用 latest、lts、 或 newest。newest 是最新安装的版本。可选择指定 32/64 位架构。`nvm use <arch>`将继续使用所选版本，但切换到 32/64 位模式。有关 use 在特定目录中使用（或使用.nvmrc）的信息，请参阅问题 #16。  
`nvm root <path>`：设置 nvm 存放不同版本 node.js 的目录。如果`<path>`未设置，将显示当前根目录。  
`nvm version`：显示当前运行的 NVM for Windows 版本。  
`nvm node_mirror <node_mirror_url>`：设置节点镜像。国内的可以使用<https://npmmirror.com/mirrors/node/>  
`nvm npm_mirror <npm_mirror_url>`：设置 npm 镜像。中国人可以使用<https://npmmirror.com/mirrors/npm/>
