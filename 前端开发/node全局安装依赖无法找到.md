# node 全局安装依赖无法找到问题

如果全局安装完依赖，然后在命令行中使用，提示找不到命令，比如`Cannot find module 'axios`，那么就需要配置环境变量了

## 配置环境变量

1. 首先找到 nodejs 的安装目录，我是使用 nvm 安装，node 安装目录是
   `C:\Program Files\nodejs`,环境变量配置如下
   ![1695895091409.png](https://cdn.mo0.top/1695895091409.png)
2. 可以看到 NVM_SYMLINK 是配置的 nodejs 的安装目录，这样就可以找到 nodejs 的安装目录了，然后打开目录可以找到`node_modules`文件夹，然后对 NODE_PATH 进行配置，添加`%NVM_SYMLINK%/node_modules`即可
3. ![1695896029931.png](https://cdn.mo0.top/1695896029931.png)
4. 也可以 NODE_PATH 添加`C:\Program Files\nodejs\node_modules`
   只不过是第一种使用了环境变量，第二种直接写死了路径，不过都是可以的

## 测试

## 为了保险起见，可以重启一下电脑，然后再次回到 cmd 中，执行之前报错的脚本，可以看到已经可以正常使用了
