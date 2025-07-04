# 随着家里联网设备越来越多，对路由器的性能要求也越来越高，厂商自带的路由器固件慢慢也无法满足一个想要折腾的我。在经过多次尝试后，这是我梳理的一个路由器固件配置从手动到自动化的过程

本次从 [coolsnowwolf/lede](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Fcoolsnowwolf%2Flede) 固件入手，过渡到使用 Github Action，以旁路由固件为例，一步步实现伸手可得的个性化路由器固件。

> 摘自 OpenWrt 官网的介绍：OpenWrt/LEDE 是一个为嵌入式设备（通常是无线路由器）开发的高扩展度的 GNU/Linux 发行版。与许多其他路由器的发行版不同，OpenWrt 是一个完全为嵌入式设备构建的功能全面、易于修改的由现代 Linux 内核驱动的操作系统。在实践中，这意味着您可以得到您需要的所有功能，却仍能避免臃肿。

## 本地编译路由器固件

这里从 [coolsnowwolf/lede](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Fcoolsnowwolf%2Flede) 源码仓库上介绍的编译步骤开始操作：

### 首先是注意事项

1. 首先装好 Ubuntu 64bit，推荐 Ubuntu 18 LTS x64
2. 不要用 root 用户 git 和编译！！！
3. 国内用户编译前最好准备好软件源下载加速
4. 默认登陆 IP `192.168.1.1`, 密码 `password`（后面文章中对 IP 有修改

### 编译前准备

1. 命令行输入 `sudo apt-get update -y` 对系统进行更新
2. 输入 `sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3.5 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget swig` 安装编译固件需要的依赖
3. 使用 `git clone https://github.com/coolsnowwolf/lede` 下载源码，然后 `cd lede` 进入源码目录，**下面的所有操作都是基于源码目录内的操作**

### 个性化配置需要的功能

启用必要的软件源：  
使用 `vim` 编辑器来编辑 `./feeds.conf.default` 文件

```bash
vim ./feeds.conf.default
```

然后将 `#src-git helloworld https://github.com/fw876/helloworld` 这一行的注释取消掉。编辑完成后这个文件看起来像这样：

```yaml
src-git packages https://github.com/coolsnowwolf/packages
src-git luci https://github.com/coolsnowwolf/luci
src-git routing https://git.openwrt.org/feed/routing.git
src-git telephony https://git.openwrt.org/feed/telephony.git
src-git freifunk https://github.com/freifunk/openwrt-packages.git
#src-git video https://github.com/openwrt/video.git
#src-git targets https://github.com/openwrt/targets.git
#src-git management https://github.com/openwrt-management/packages.git
#src-git oldpackages http://git.openwrt.org/packages.git
#src-link custom /usr/src/openwrt/custom-feed
src-git helloworld https://github.com/fw876/helloworld
```

### 修改路由器固件的初始网络配置

#### 配置 LAN 口的网络

如果不进行这一步骤的操作，该路由器固件安装成功后，路由器 LAN 口管理 IP 为 `192.168.1.1`。如果断网操作肯定是没问题的，但是如果在现有的网络环境下部署旁路由（旁路网关）的话，一般就会和主路由器的 IP 地址冲突。所以这里介绍一下如何在编译固件前修改路由器固件的默认 IP。

修改 `zzz-default-settings` 文件，在 `exit 0` 这一行上方添加自定义配置。具体操作如下：

```yaml
$ vim ./package/lean/default-settings/files/zzz-default-settings    # 打开要编辑的文件

uci set network.lan.ipaddr='192.168.1.254'   # 默认 IP 地址
uci set network.lan.proto='static'   # 静态 IP
uci set network.lan.type='bridge'   # 接口类型：桥接
uci set network.lan.ifname='eth0'   # 网络端口：默认 eth0，第一个接口
uci set network.lan.netmask='255.255.255.0' # 子网掩码
uci set network.lan.gateway='192.168.1.1'   # 默认网关地址（主路由 IP）
uci set network.lan.dns='192.168.1.1'  # 默认上游 DNS 地址
uci commit network
```

### 接着进行下一步编译

输入如下命令，更新编译源，并进入配置页面：

```bash
./scripts/feeds update -a
./scripts/feeds install -a
make menuconfig   # 进入配置界面
```

配置页面如下图所示：

![1](https://raw.githubusercontent.com/5cr1pt/img4markdown/master/pics/20200716011755.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)

### 自定义需要的路由器固件

首先，根据路由器的平台和架构，在 `Target System`、`Subtarget`、`Target Profile` 中选择相应的选项。然后，作为旁路由，需要安装的插件其实很少，基本上就是广告过滤、解锁网易云等。具体操作如下：

> 如果不需要在路由上实现 USB 文件共享/Samba 多媒体共享，需要在 `Extra-packages` 菜单中取消选择 `automount` 和 `autosamba`

进入 `Luci - Applications` 选择需要的插件，这里我列出部分常见的插件：

```yaml
# 我常用的几个，只做旁路由使用
luci-app-firewall   # 防火墙和端口转发,必备
luci-app-adbyby-plus    # 广告屏蔽大师 Plus+
luci-app-sfe    # Turbo ACC 网络加速(开启 Fast Path 转发加速)
luci-app-unblockNeteaseMusic    # 解锁网易云

# 按需选择
luci-app-wol    # 网络唤醒
luci-app-accesscontrol  # 上网时间控制
luci-app-arpbind    # IP/MAC 绑定
luci-app-autoreboot # 计划重启
luci-app-ddns   # 动态域名解析
luci-app-filetransfer   # 上传 ipk 文件功能
luci-app-frpc   # 内网穿透 Frp
luci-app-ipsec-vpnd # IPSec 服务端
luci-app-nlbwmon    # 网络带宽监视器
luci-app-ramfree    # 释放内存
luci-app-rclone # rclone
luci-app-samba  # 网络共享（samba）
luci-app-upnp   # 通用即插即用 UPnP(端口自动转发)
luci-app-vlmcsd # KMS 服务器（WIN 激活工具）
luci-app-vsftpd # FTP 服务器
luci-app-webadmin   # Web 管理页面设置
luci-app-xlnetacc   # 迅雷下载
luci-app-zerotier   # 虚拟局域网
luci-app-aria2  # Aria2 下载
luci-app-hd-idle    # 硬盘休眠
luci-app-mwan3  # MWAN 负载均衡
luci-app-openvpn    # OpenVPN 客户端
luci-app-openvpn-server # OpenVPN 服务端
luci-app-pptp-server    # PPTP 服务端
luci-app-sqm    # 流量智能队列管理(QOS)
luci-app-transmission   # BT 下载
luci-app-usb-printer    # USB 打印服务器
luci-app-wireguard  # VPN 服务器 WireGuard 状态
luci-app-wrtbwmon   # 实时流量监测
```

配置完成后光标移动到 `Save` ，回车保存。

### 继续编译命令

1. `make -j8 download V=s` 下载 dl 库（国内请尽量做好软件源加速）
2. 输入 `make -j1 V=s` （`-j1` 后面数字`1`是线程数。第一次编译推荐用单线程）进行编译你要的固件。

**编译完成后固件保存在 `./bin/targets/` 文件夹内。**

## 使用 Github 的 Action 功能自动编译

> 首先需要有一个 Github 帐号

打开 [coolsnowwolf/lede](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2Fcoolsnowwolf%2Flede) 固件页面，点击 `Folk` 将该源码 Folk 到自己的帐号。然后以该源码自带的 Action 为例来修改。文件路径为 `.github/workflows/openwrt-ci.yml`。

文件第 13/14 行为自动运行周期，默认值如下：

```yaml
schedule:
  - cron: 0 20 * * * # 每天的 20:00 执行该文件定义的动作
```

### 提取本地自定义固件和默认配置的差异

在上面本地编译时，当完成**“配置完成后光标移动到 `Save` ，回车保存。”**后，会在源码根目录生成一个 `.config` 文件，此时运行下面命令来提取配置差异，并保存在 `diff.config` 文件中备用：

```bash
make defconfig
./scripts/diffconfig.sh > diff.config   # 将差异保存在 diff.config 文件中
```

### 在 `openwrt-ci.yml` 中添加自定义配置

#### 启用软件源

修改 `openwrt-ci.yml` 第 46 行的 `Update feeds` 动作，将 `echo 'src-git helloworld https://github.com/fw876/helloworld' >> ./feeds.conf.default` 添加进去，添加完成后该部分如下：

```yaml
- name: Update feeds
  run: |
    echo 'src-git helloworld https://github.com/fw876/helloworld' >> ./feeds.conf.default
    sed -i 's/\"#src-git\"/\"src-git\"/g' ./feeds.conf.default
    ./scripts/feeds update -a
    ./scripts/feeds install -a
```

### 添加自定义的固件配置

修改 `openwrt-ci.yml` 第 52 行的 `Generate configuration file` 动作，将之前提取的差异文件 `diff.config` 中的内容全部拷贝出来，添加中间那一行：

```yaml
- name: Generate configuration file
  run: |
    rm -f ./.config*
    touch ./.config
    cat >> .config <<EOF
    #
    # ========================固件定制部分========================
    #
    # 删除这一行，粘贴为 diff.config 中的内容，注意对其
    #
    # ========================固件定制部分结束========================
    #
    EOF
    sed -i 's/^[ \t]*//g' ./.config
    make defconfig
```

然后将网络配置添加到 `make defconfig` 之后，如下所示：

```yaml
          # 网络配置信息，将从 zzz-default-settings 文件的第2行开始添加
          sed -i "2i # network config" ./package/lean/default-settings/files/zzz-default-settings
          # 默认 IP 地址，旁路由时不会和主路由的 192.168.1.1 冲突
          sed -i "3i uci set network.lan.ipaddr='192.168.1.5'" ./package/lean/default-settings/files/zzz-default-settings
          sed -i "4i uci set network.lan.proto='static'" ./package/lean/default-settings/files/zzz-default-settings # 静态 IP
          sed -i "5i uci set network.lan.type='bridge'" ./package/lean/default-settings/files/zzz-default-settings  # 接口类型：桥接
          sed -i "6i uci set network.lan.ifname='eth0'" ./package/lean/default-settings/files/zzz-default-settings  # 网络端口：默认 eth0，第一个接口
          sed -i "7i uci set network.lan.netmask='255.255.255.0'" ./package/lean/default-settings/files/zzz-default-settings    # 子网掩码
          sed -i "8i uci set network.lan.gateway='192.168.1.1'" ./package/lean/default-settings/files/zzz-default-settings  # 默认网关地址（主路由 IP）
          sed -i "9i uci set network.lan.dns='192.168.1.1'" ./package/lean/default-settings/files/zzz-default-settings  # 默认上游 DNS 地址
          sed -i "10i uci commit network\n" ./package/lean/default-settings/files/zzz-default-settings
```

添加完成后这一部分如下：

```yaml
- name: Generate configuration file
  run: |
    rm -f ./.config*
    touch ./.config
    cat >> .config <<EOF
    #
    # ========================固件定制部分========================
    #
    # 示例配置，下面是 diff.config 文件中的内容
    CONFIG_TARGET_x86=y
    CONFIG_TARGET_x86_64=y
    CONFIG_TARGET_x86_64_DEVICE_generic=y
    # CONFIG_PACKAGE_luci-app-accesscontrol is not set
    # CONFIG_PACKAGE_luci-app-arpbind is not set
    CONFIG_PACKAGE_luci-app-autoreboot=m
    CONFIG_PACKAGE_luci-app-ddns=m
    # CONFIG_PACKAGE_luci-app-filetransfer is not set
    CONFIG_PACKAGE_luci-app-frpc=m
    CONFIG_PACKAGE_luci-app-ipsec-vpnd=m
    CONFIG_PACKAGE_luci-app-nlbwmon=m
    CONFIG_PACKAGE_luci-app-ramfree=m
    #
    # ========================固件定制部分结束========================
    #
    EOF
    sed -i 's/^[ \t]*//g' ./.config
    make defconfig
    # 网络配置信息，将从 zzz-default-settings 文件的第2行开始添加
    sed -i "2i # network config" ./package/lean/default-settings/files/zzz-default-settings
    # 默认 IP 地址，旁路由时不会和主路由的 192.168.1.1 冲突
    sed -i "3i uci set network.lan.ipaddr='192.168.1.254'" ./package/lean/default-settings/files/zzz-default-settings
    sed -i "4i uci set network.lan.proto='static'" ./package/lean/default-settings/files/zzz-default-settings # 静态 IP
    sed -i "5i uci set network.lan.type='bridge'" ./package/lean/default-settings/files/zzz-default-settings  # 接口类型：桥接
    sed -i "6i uci set network.lan.ifname='eth0'" ./package/lean/default-settings/files/zzz-default-settings  # 网络端口：默认 eth0，第一个接口
    sed -i "7i uci set network.lan.netmask='255.255.255.0'" ./package/lean/default-settings/files/zzz-default-settings    # 子网掩码
    sed -i "8i uci set network.lan.gateway='192.168.1.1'" ./package/lean/default-settings/files/zzz-default-settings  # 默认网关地址（主路由 IP）
    sed -i "9i uci set network.lan.dns='192.168.1.1'" ./package/lean/default-settings/files/zzz-default-settings  # 默认上游 DNS 地址
    sed -i "10i uci commit network\n" ./package/lean/default-settings/files/zzz-default-settings
```

配置完成后保存。每天的 20:00，Github 都会自动帮你编译固件。可以在自己 Folk 的 `lede` 仓库的 Action 页面找到编译完成的压缩包：

![2](https://raw.githubusercontent.com/5cr1pt/img4markdown/master/pics/20200716015435.png?imageView2/2/w/1120/q/40/interlace/1/ignore-error/1)

> 配合 [KFERMercer/OpenWrt-CI](https://sspai.com/link?target=https%3A%2F%2Fgithub.com%2FKFERMercer%2FOpenWrt-CI) 的 `merge-upstream.yml` 每天都能自动合并推送上游提交 (也就是自动更新)，收获编译完成的最新固件。

## PS. 单臂旁路由的设置

1. 接口 IP 地址改静态，和主路由同一个网段
2. 接口的 IPv4 网关为主路由网关
3. 接口的 DNS 地址为主路由地址或自定义 DNS 地址
4. 接口的 DHCP 设置为**忽略此接口**（DHCP 由主路由来分配）
5. 主路由的 DHCP 设置中，将 DHCP 的网关和 DNS 地址配置为旁路由的 IP 地址
6. 设置相关插件（路径：`服务 - 广告屏蔽大师 Plus+`等）

总结一下，主要的几个步骤就是：搭建编译环境/下载源码/修改和配置/等待编译结果；或者是：提取差异/Folk 源码/修改 Action 脚本/等待自动编译完成。
