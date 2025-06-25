# Linux root 账户及 ssh 链接问题

## 问题描述

这几天在弄虚拟机，然后发现一个以前忽略的问题，Ubuntu 默认没有启用 ROOT 用户且安装时没有设置密码，之后还碰到 SSH 无法远程登陆 ROOT 用户的问题，记录一下解决过程

### 修改 ROOT 用户密码,切换成 ROOT 用户，输入命令

```shell
sudo -i
```

然后输入密码,进入 ROOT 用户后输入命令，这里是为了修改 ROOT 用户密码

```shell
passwd
```

输入你想要修改后的密码

输入`su yourusername`切换回普通用户，然后在输入`su root`后输入修改后的密码切换回 ROOT 用户看是否成功

## 如何连接 Ubuntu 的 Root 用户

默认情况下，Ubuntu 系统是不允许 Root 用户远程登录的，所以我们需要修改 SSH 配置文件，允许 Root 用户远程登录

### 修改 SSH 配置文件

首先得确保 Ubuntu 已经安装了 SSH，安装命令为

```shell
apt install openssh-server
```

切换成 ROOT 用户，输入命令

```shell
vi /etc/ssh/sshd_config
```

接下来来修改配置文件

找到 `PermitRootLogin` 所在行，输入 i 进入输入模式，取消注释并将值设为 yes，然后按 q，输入:wq 保存退出，这里是为了允许 Root 用户远程登录

```shell
PermitRootLogin yes
```

重启 SSH 服务，输入命令

```shell
service ssh restart
```

然后再次尝试`Xshell`连接，连接成功

## 然后，使用以下命令安装中文语言支持和输入法

```shell
sudo apt install language-pack-zh-hans language-pack-gnome-zh-hans fcitx-libpinyin
```

如果没有直接生效，可以重启一下系统，然后在设置里面添加中文输入法，然后就可以使用了

```shell
LANG=zh_CN.UTF-8
```
