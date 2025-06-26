# OP 安装 openclash

## 安装

### 首先 OP 需要安装软件包，进入 SSH 安装

**打开 PuTTY 或者直接实用 openWrt 中的的 TTYD 终端，输入软路由的 IP，选择 SSH 模式，进入。**

```shell
cd /tmp
opkg update
opkg install luci
opkg install luci-base
opkg install iptables
opkg install dnsmasq-full
opkg install coreutils
opkg install coreutils-nohup
opkg install bash
opkg install curl
opkg install jsonfilter
opkg install ca-certificates
opkg install ipset
opkg install ip-full
opkg install iptables-mod-tproxy
opkg install kmod-tun  #TUN模式
opkg install luci-compat
```

### 下载 OpenClash

[Github 下载地址](https://github.com/vernesong/OpenClash)

**只要下载 `ipk` 文件即可**

### 上传文件

**文件上传到 `OP`**

### 安装 OpenClash

回到命令行，输入（xxx 替换为安装包全名）,例如 `luci-app-openclash_0.45.129-beta_all.ipk`

```shell
opkg install luci-app-openclash_0.45.129-beta_all.ipk
```

安装完成之后在服务列表即可找到 OpenClash

### 卸载

这里有一个需要注意的地方，就是直接执行  
`opkg remove xxxx\*.ipk` 是没有用的，因为安装好以后的文件名是和原来的.ipk 文件名不一样的。  
步骤：

执行 `opkg list_installed`,找到想卸载的文件名称。
执行 `opkg remove 文件名`（就是上一步找到的名称）。
