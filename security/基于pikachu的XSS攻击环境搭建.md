# XSS 攻击环境搭建思路

pikachu 平台中 app/pkxss 目录，黑客设置的远程响应目录，用于接收受害者在不知情的情况下发送给黑窝的关键信息。
该目录就是一个小网站，建议将该目录单独部署。

我的部署环境如下：

1. docker 中运行 pikachu 平台，模拟对公众进行正常服务的网站。（完成）
   - 访问路径：http://localhost:8000，部署在 docker 容器中。
2. 物理机上搭建 phpstudy，用来存放 pkxss 目录。模拟黑客搭建的用以接收正常网站传回的信息。
   1. 访问路径：http://localhost，部署在物理机中

这相当于两个网站，分别模拟受害服务器和黑客服务器。也可以将 pikachu 和 pkxss 放在一起。

## 在物理机上安装 phpstudy 平台

物理机上的 phpstudy 平台，用来模拟黑客接收敏感数据的网站。

官网地址：[XP官网](https://www.xp.cn/)

根据自己的 OS 下载并安装对应的平台。此文档以 MacOS 平台为例，兼容其他的平台。

本次使用 apache，当做 php 的服务器。

注意 apache 解析的网站的物理地址：

- MacOS 中物理地址位于：/Applications/phpstudy/WWW。
- 在 windows 中物理地址位于：盘符:/phpstudy_pro/WWW。

安装完成后，把测试文件 test.php，放到对应的 WWW 目录中，并启动 apache，测试是否成功。

## 从容器中往物理主机拷贝文件或目录

黑客服务器代码位于容器中的 pkxss 目录中，具体如下：

![image-20220315150521039](images/image-20220315150521039.png)

需要将这个目录从容器中拷贝到物理主机中的 apache 相应的目录中。

从容器中向物理主机拷贝可以使用 docker 命令：docker cp

语法如下：

```shell
docker cp 容器名:源路径 目标路径
```

具体代码如下：

```bash
sudo docker cp pikachu:/app/pkxss /Applications/phpstudy/WWW/pkxss ##mac
sudo docker cp pikachu:/app/pkxss /mnt/盘符/phpstudy_pro/WWW/pkxss		##ubuntu
X:\phpstudy_pro\WWW>docker cp pikachu:/app/pkxss /pkxss						##windows
```

解析：

1. pikachu:是容器名，这里是 docker 的规定写法。
2. /app/pkxss 是容器中的源目录，/Applications/phpstudy/WWW/pkxss 是主机中的目标目录
3. 第二行中的/mnt/x/...中的 x 指的是盘符，这里的 x 只是一个占位符。

执行以上代码后，容器中的 pkxss 目录和其中的所有的文件、子目录都被拷贝到了 apache 的目录中。

> linux 中的 cp 命令
>
> cp 是缩写，完成的写法是 copy。其作用是拷贝，一般后面跟两个选项，第一个是源文件或源目录；第二个是目标文件或目标目录。
>
> ```bash
> cp test.html ./pkxss/test1.html
> ```
>
> 以上代码的意思是从当前目录中拷贝 test.html 文件到当前目录的 pkxss 子目录中，并改名为 test2.html。

启动 apache，尝试运行 pkxss 网站，输入如下 URL:

```
http://localhost/pkxss
```

## 修改 MySQL 的默认密码

phpstudy 中的 MySQL 的默认用户是 root，默认密码也是 root。一般建议修改一下。如果直接通过 MySQL 来修改的话，需要多敲好几行代码，可以直接利用 phpstudy 来进行修改。具体方式如下图所示：

<img src="images/image-20220315151854788.png" alt="image-20220315151854788" style="zoom: 33%;" />

修改密码完成之后，在 pkxss 目录的子目录 inc 中找到 config.inc.php 文件 ，将其中的 DBPW 的值改成新修改的密码即可。如下：

<img src="images/image-20220315152044819.png" alt="image-20220315152044819" style="zoom:50%;" />

config.inc.php 文件是 pkxss 目录中所有 php 文件都经加载的配置文件，该文件对当前网站进行全局设置，并指定了相应的 MySQL 数据库。

## 运行网站

最后就是运行并登录了。并根据提示进行数据库的初始化。
