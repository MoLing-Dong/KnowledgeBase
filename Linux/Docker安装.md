# 使用官方安装脚本自动安装

安装命令如下：

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

也可以使用国内 **daocloud** 一键安装命令：

```shell
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
```

这里是换源脚本，可以使用以下命令进行换源：

```shell
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```


如若都无法使用检查是否安装 curl

## 使用阿里云镜像加速

网址
**<https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors>**

将`['http://xxxxxxxxxxxxx']`内链接替换成自己的链接即可达到加速作用,然后执行

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://*******.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
