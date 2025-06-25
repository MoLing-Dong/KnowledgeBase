# 安装 zsh 和 zim

## Zsh 搭配 Oh-My-Zsh 固然很强，但是我掏出 Zimfw 呢

Zimfw 是一个基于 Zsh 的框架，它旨在提供更好的性能、更多的功能和更好的用户体验。虽然 Oh-My-Zsh 是目前最受欢迎的 Zsh 框架之一，但 Zimfw 提供了一些独特的功能，使其成为值得关注的选择。

### 强大的性能

首先，Zimfw 在性能方面表现出色。相比于 Oh-My-Zsh，它加载和执行速度更快，减少了等待时间。这对于那些频繁使用命令行工具的开发者和系统管理员来说是非常重要的，他们希望能够更快地切换目录、运行命令和执行其他操作。

### 丰富的主题和插件库

### 灵活的配置与定制

---

## 开始进入正题

## 查看系统当前使用的 shell

```shell
echo $SHELL
```

## 开始安装 zsh

```shell
apt install zsh -y
```

## 查看系统自带哪些 shell

可以检查一下是否下载成功 zsh

```shell
cat /etc/shells
```

## 设置 zsh 设为默认 shell 之后重启开机之后可以在检查一下是否成功

```shell
chsh -s /bin/zsh
reboot
```

## 安装 zim

项目地址：<https://github.com/zimfw/zimfw>

```shell
curl -fsSL https://ghproxy.com/https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
或
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

## 个性化

```shell
vim ~/.zimrc
```

### 修改选项

**这是我的模板**

```shell
# Start configuration added by Zim install {{{
#
# This is not sourced during shell startup, and it's only used to configure the
# zimfw plugin manager.
#

# Start configuration added by Zim install {{{
# -------
# Modules
# -------

# Sets sane Zsh built-in environment options.
zmodule environment
# Provides handy git aliases and functions.
zmodule git
# Applies correct bindkeys for input events.
zmodule input
# Sets a custom terminal title.
zmodule termtitle
# Utility aliases and functions. Adds colour to ls, grep and less.
zmodule utility
zmodule romkatv/powerlevel10k
#
#Prompt
#
# Exposes to prompts how long the last command took to execute, used by asciiship.
zmodule duration-info
# Exposes git repository status information to prompts, used by asciiship.
zmodule git-info
# A heavily reduced, ASCII-only version of the Spaceship and Starship prompts.

# Additional completion definitions for Zsh.
zmodule zsh-users/zsh-completions
# Enables and configures smart and extensive tab completion.
# completion must be sourced after zsh-users/zsh-completions
zmodule completion
# Fish-like autosuggestions for Zsh.
zmodule zsh-users/zsh-autosuggestions
# Fish-like syntax highlighting for Zsh.
# zsh-users/zsh-syntax-highlighting must be sourced after completion
zmodule zsh-users/zsh-syntax-highlighting
# Fish-like history search (up arrow) for Zsh.
# zsh-users/zsh-history-substring-search must be sourced after zsh-users/zsh-syntax-highlighting
zmodule zsh-users/zsh-history-substring-search
# }}} End configuration added by Zim install
#
zmodule DarrinTisdale/zsh-aliases-exa # 添加多个 alias, 使用 exa 代替 ls，要求有安装 exa


# Custom 个人推荐的插件
#zmodule directory
zmodule Aloxaf/fzf-tab # 将 zsh shell 补全的结果 pipe 入 fzf 中显示和选择

# 个人其他工具的选择
zmodule wookayin/fzf-fasd
zmodule zsh-users/zaw

zmodule completion
zmodule git-info
zmodule prompt-pwd
zmodule magicmace
zmodule zdharma-continuum/fast-syntax-highlighting
zmodule supercrabtree/k
zmodule hlissner/zsh-autopair
zmodule pabloariasal/zfm
zmodule agkozak/zsh-z
zmodule Aloxaf/fzf-tab
```

### 安装

```shell
# 安装
zimfw install
# 配置主题
p10k configure
```

接着使用如下命令安装插件和 p10k （包名已经在配置文件中，只需要下面一个命令就能安装所有插件和 p10k 主题）

- 检查是否能看到菱形
  ![1692779858730.png](http://cdn.stjhub.com/1692779858730.png)
- 检查是否能看到方块 ===> 我的都看不到一直摁 n
  ![1692780050626.png](http://cdn.stjhub.com/1692780050626.png)
- 建议选择 Unicode
  ![1692780137640.png](http://cdn.stjhub.com/1692780137640.png)
- 之后的按照个人喜爱选择
  > 已安装 powerlevel10k，报错 command not found: p10k，执行`source ~/.zshrc`即可，然后重启终端即可
  > 如果安装完成之后 ls 提示`zsh: command not found: exa`，之后执行`apt install exa`即可

## 最后的效果展示

![1692797477178.png](http://cdn.stjhub.com/1692797477178.png)
![1692797614647.png](http://cdn.stjhub.com/1692797614647.png)
