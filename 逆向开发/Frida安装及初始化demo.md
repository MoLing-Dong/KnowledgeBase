# Frida 安装

## 项目跑在 Windows 环境下，搭配手机模拟器

### 一、选择合适的版本

[frida](https://github.com/frida/frida) 版本要和 python、Android 对应起来，过高或过低都可能将导致不可使用。

一般  frida16.6.4+ 、Python3.8 对应  Android7-8 的版本，frida14+、Python3.8 对应 Android9 的版本。

打开  [Releases · frida/frida · GitHub](https://github.com/frida/frida/releases "Releases · frida/frida · GitHub")  进行查看，找到适合自已的版本，因为我这用的用的 python3.10、雷电[模拟器](https://www.ldmnq.com/)9 64 位，这里选 frida16.6.4 为例。

### 二、安装指定版本的 frida

**安装指定版本的 frida 和 frida-tools：**

```shell
pip install frida==16.6.4 frida-tools== -i https://pypi.mirrors.ustc.edu.cn/simple/
```

安装过程不会很快，耐心等待一下。

### 三、安装 frida-server

上面两步完成只在在本地安装了 `frida`，本地编写脚本后需要推送到手机端的`frida-server`，然后 `frida-serve`r 执行完毕再把信息返回本地。接下来装一下 `frida-server`:

命令行用 adb 连接下模拟器：

```shell
adb shell
```

这里使用的是雷电模拟器，如果是真机的话，需要先打开开发者模式，然后打开 USB 调试，然后再连接。

#### 1.配置手机端 server

Windows 推荐使用 Microsoft 商店的 Windows Terminal，方便管理命令行，如果没有的话可以下载安装，[下载地址](https://www.microsoft.com/zh-cn/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab)。

接下来执行：`adb shell`, 进入模拟器的 shell 环境，然后执行下面命令，查看模拟器的架构：

```shell
getprop ro.product.cpu.abi
```

一般来讲模拟器使用的是 x86 或 x86_64 的架构，而真机就是 arm 或 arm64 架构。这里返回的是 x86_64，那么我们下载 x86_64 的。

#### 2.下载 frida-server

从[下载地址](https://githun.com/frida/frida/releases)选择合适的版本下载，注意是下载 frida-server 开头的文件是 frida-server-16.6.4-android-x86_64.xz 这样的文件，

#### 3.解压并推送到模拟器

下载后解压出来只有一个文件，需要把它推送到模拟器中 `/data/loacl/tmp` 下，原文件名很长，先改个名如 `fserver`，在解压的文件夹目录运行命令：

```cmd
adb push fserver /data/local/tmp/
```

再开一个命令行窗口，现在执行：`adb shell`

切换管理员：`su`

进入目录：`cd /data/local/tmp`

改下权限：`chmod 777 fserver`

然后就可以运行了：`./fserver`

现在就可以本地与手机进行交互了。如果上面` pip install frida` 是在虚拟环境中安装，那么在进入虚拟环境中可以运行下命令查看效果。

运行命令： `frida-ps -U` ， 打印当前正在运行的应用， `-U` 是 usb 连接的，现在显示出来的就是手机目前运行的应用了。

### 四、编写脚本

这里使用 VScode 编写脚本，新建项目文件夹，然后新建一个文件，命名为 `frida.js`，然后写入下面代码：

```javascript
Java.perform(function () {
  var MainActivity = Java.use("com.example.frida.MainActivity");
  MainActivity.test.implementation = function (a, b) {
    console.log("a: " + a);
    console.log("b: " + b);
    return this.test(a, b);
  };
});
```

运行命令：`frida -U -f com.example.frida -l frida.js`，`-U` 是 usb 连接，`-f` 是指定包名，`-l` 是指定脚本文件。
