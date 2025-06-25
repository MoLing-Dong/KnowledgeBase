# Huawei RH2288 V3 风扇噪音大的解决方案

## 问题描述

因为需要在家里搭建一个测试环境，所以在家弄了一台华为 2288 v3 的服务器，无奈太吵了，所以就想着怎么解决这个问题。

通过查找文档发现通过 ssh/Telnet 命令进入后可以设置更长的一个时间。

### 操作步骤

- 1、SSH 登录 iBMC

- 2.查询当前风扇工作模式

```shell
ipmcget -d faninfo
Current mode: auto
```

auto 则为自动
-3、更改自动为手动

```shell
ipmcset -d fanmode -v 1 100000000
```

- 4、更改风扇转速根据自己业务量修改

```shell
ipmcset -d fanlevel -v 20
```

参考链接
`https://support.huawei.com/enterprise/zh/doc/EDOC1000038841/1f844523`
`https://support.huawei.com/enterprise/zh/doc/EDOC1000038841/7f43ccc6`
