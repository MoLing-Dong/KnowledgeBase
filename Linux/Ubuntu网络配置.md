# Ubuntu 网络配置踩坑记录

## 重新安装了 Ubuntu ，然后在配置网络的时候，发现了一些问题，记录一下。

将 DHCP 改为静态 IP 地址后，忘记如何配置了。网上查了一下，发现了一些问题。
`/etc/network/interfaces` 的文件已经被弃用了，现在使用的是 `/etc/netplan/` 目录下的文件。文件名根据自己的情况而定。

```shell
sudo vim /etc/netplan/00-cloud-init.yaml
```

之前网上有使用`getaway4:IP`的方式，但是我这里不行，只能使用 routes 的方

```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens160:
      addresses:
        - 192.168.0.16/24 # 设置静态IP地址和掩码
      routes:
        - to: default
          via: 192.168.0.2
      dhcp4: false # 禁用dhcp
      nameservers:
        addresses: [114.114.114.114, 8.8.8.8] # 设置主、备DNS
  version: 2
```

配置完成之后`netplan apply`即可生效。
