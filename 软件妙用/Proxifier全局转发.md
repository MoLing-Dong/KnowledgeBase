# Proxifier 全局转发

今天在使用 copilot 的时候发现我所在的地方完全访问不到 Github，因为现在我对 copilot 需求较大，但是有时候 Github 的访问速度不是很理想，所以我就想到了用代理的方式来解决这个问题。但是 copilot 无法走代理，所以我就想到了用全局转发的方式来解决这个问题。

**这是一款非常好用的全局代理软件，其提供了跨平台的端口转发和代理功能。可以实现将本地的任意端口转发到远程服务器上，也可以实现全局代理。适用于：Windows、MacOS**

## 官网：<https://www.proxifier.com/>

选择对应的系统下载安装即可。  
安装的时候如果会提示安装 WinPcap，这个是必须安装的，不然无法使用。

## 注意

安装过程中如果出现 start with windows 的选项，可以不用勾选，因为这个软件只需要在使用的时候才需要启动。  
还有 check for updates，这个也可以不用勾选，因为这个软件是收费的，如果勾选了，会提示你去购买。

软件是免费试用 31 天的，如果你想要继续使用，可以去官网购买。  
但是我们可以选择激活码的方式来继续使用，激活码可以在网上搜索到，这里我就不提供了。

---

**如果懒得去搜索，可以在公众号回复 `proxifier` 获取。**

- `5EZ8G-C3WL5-B56YG-SCXM9-6QZAP`

- `YTZGN-FYT53-J253L-ZQZS4-YLBN9`

## 1. 配置代理服务器

打开软件，点击菜单栏的 `Profile` -> `Proxy Servers`，点击 `Add` 添加代理服务器。  
这里我是用 ClashX 作为代理，所以填写的是 ClashX 的代理地址和端口，如下图所示：  
`address`：`127.0.0.1` ===> 代理地址  
`port`：`7890` ===> 代理端口  
`protocol`：`SOCKS Version 5` ===> 代理协议


之后点击 `OK` 即可。

## 2. 配置规则

点击菜单栏的 `Profile` -> `Proxification Rules`，点击 `Add` 添加规则。

- `Rule Name`：规则名称，随便填写
- `Applications`：选择需要走代理的应用程序
- `Action`：选择 `Proxy SOCKS5`

如图所示：
`copilot.exe; code.exe; pycharm64.exe; webstorm64.exe`  
tips：其实我也不知道这里的 copilot.exe 到底有没有用。如果有人知道这里的 copilot.exe 有没有用，可以告诉我一下。

点击 `OK` 即可。

### 2.1 全局代理

如何设置全局代理呢？
可以直接修改 `default` 规则，将 `Action` 修改为 `Proxy SOCKS5` 即可。
还可以自己添加规则

- `Rule Name`：规则名称，随便填写，如 `Global`
- `Applications`：保持默认，即 `Any`
- `Target Hosts`：保持默认，即 `Any`
- `Target ports`：保持默认，即 `Any`
- `Action`：选择 `Proxy SOCKS5`
  最后点击 `OK` 即可。

### 最后我的效果就是下面这样

现在使用 copilot 的时候就可以使用代理了，速度也很快，不会出现卡顿的情况了。如果你也遇到了这个问题，可以试试这个方法。最后如果不需要使用软件的时候，可以将 `default` 规则的 `Action` 修改为 `Direct`，这样就不会走代理了。或者退出软件也可以。

## 现在打开 vscode 或者 webstorm 就可以使用代理了，速度也很快，不会出现卡顿的情况了。如果你也遇到了这个问题，可以试试这个方法
