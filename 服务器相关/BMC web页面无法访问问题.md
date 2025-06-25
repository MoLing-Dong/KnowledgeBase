# BMC web 页面无法访问问题

## 问题描述

**某局点客户想关闭 BMC 的 https 服务，只通过 http 的方式访问。在 BMC 的“配置”，"服务配置里"关闭掉了 https 服务**  
_告警信息_  
**\*退出 bmc 以后，重新输入 bmc 的 ip 地址发现 bmc 的 web 界面无法打开，提示“无法连接”**

## 处理过程

**现场测试通过 <http://ip> 以及 <http://ip:80> 端口均无法的登录 web 页面**

**尝试 SSH，可以成功，通过查找命令在命令行界面上重新打开 https 服务以后，web 页面又可以重新的登录了**

方法如下：

### 1. 命令行先确认下服务目前状态（如下图 https 是 disabled 的）

```shell
ipmcget -t service -d list
```

### 2. 执行以下命令开启

```shell
ipmcset -t service -d state -v HTTPS enabled
```

### 3. 操作完以后检查，已经 enabled 了，再到浏览器测试下正常了

```shell
ipmcget -t service -d list
```

### 根因

**通过查询 bmc 的通信矩阵，发现 bmc 的 http 协议只实现了输入 IP 默认跳转的功能，当连接建立后将默认跳转到 https 这个安全协议。所以关闭掉 https 以后，通过 http 访问就会出现无法访问 web 页面的问题。**
