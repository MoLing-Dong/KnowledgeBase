# 使用 mkcert 工具为 ESXI 主机 生成 https 安全访问证书

## 1、环境

Window10 192.168.0.101

ESXI 192.168.0.11

一般情况，访问 ESXI 的控制端时，会报访问连接不安全。

该问题并不影响使用，而且一般我们是通过 vcenter 去管理 ESXI 主机的，但是在其他一些情况下，我们使用 https 开发自己本地的程序的时候，如果要通过程序去测试 https 的真实环境，这种方式就不失为一种很好的解决办法。

方法就是在我们 window10 本地，为 ESXI 主机通过 mkcert 创建证书，然后倒入证书到 ESXI 主机即可。

## 2、Window 上安装 mkcert

搜索框输入 cmd 选择以管理员运行：

出来的控制台输入：`PowerShell`，

然后在输入：`Get-ExecutionPolicy`，

如果输出结果是：`Restricted`，

就接着输入：`Set-ExecutionPolicy AllSigned`，

然后在输入：

```cmd
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

然后测试 choco 是否安装成功，直接输入 `choco`。

看到版本就表示安装成功了。

然后重新打开一个 cmd 的管理员窗口，输入：`choco install mkcert`

然后在输入：`mkcert -install`

## 3、生成 ESXI 证书

然后就为我们 ESXI 主机生产证书文件：

```shell
mkcert esxi.com 192.168.0.11
```

域名随便起，因为后面我们通过本地的 hosts 文件来解析域名。IP 就是 ESXI 主机的管理 ip.

可以看到提示证书生成在了：`C:\Users\Administrator\AppData\Local\mkcert` 目录。

通过 ssh 连接 ESXI 主机，注意前提是需要开启 ESXI 主机的 ssh 服务，默认是关闭的。

回车即可以

```shell
enable ssh
```

## 4、上传证书到 ESXI 主机

连接到 sftp，上传`rootCA.pem`和`rootCA-key.pem`文件（这两个文件就是刚才执行 mkcert 在本地 window10 生成的证书文件）到 ESXI 主机的`/etc/vmware/ssl/`目录里面

```shell
lcd C:\Users\Administrator\AppData\Local\mkcert

cd /etc/vmware/ssl

put www.ESXI\*
```

## 5、备份原 ESXI 主机证书

将原来 ESXI 的证书文件备份到本地，然后删除。

```shell
rm -f /etc/vmware/ssl/rui.crt

rm -f /etc/vmware/ssl/rui.key
```

注意：要将原证书备份到本地，如果只是通过 `mv rui.crt rui.crt_bak` 这样备份在原目录，这个备份文件在重启 ESXI 主机后就没了。

## 6、修改上传文件为新证书

将我们刚才上传的两个文件分别改名成`rui.crt` 和`rui.key`

```shel
cd /etc/vmware/ssl/
mv www.esxi.com+1-key.pem rui.key
mv www.esxi.com+1.pem rui.crt

```

## 7、重启使证书生效

然后重启 ESXI 主机。

```shell
reboot
```

## 8、添加本地 hosts 解析

重启完后，在我们 windows10 本地添加 hosts 域名解析。

修改 `C:\Windows\System32\drivers\etc\hosts` 文件，添加如下记录：

192.168.0.11 esxi.com

这个记录就是我们使用 mkcert 生产证书的时候对应的 ip 和域名。

然后我们尝试访问 ESXI 的管理端。

## 9、尝试测试访问 ESXI

可以看到不管是通过域名还是 ip 访问 ESXI，现在都显示连接是安全的，证书是有效的。

> choco 官网：<https://community.chocolatey.org/>
