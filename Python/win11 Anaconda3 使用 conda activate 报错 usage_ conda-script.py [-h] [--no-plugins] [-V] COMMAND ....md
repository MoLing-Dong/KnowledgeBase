# win11 Anaconda3 使用 conda activate 报错 usage\_ conda-script.py [-h] [--no-plugins] [-V] COMMAND ....md

## win11 [Anaconda3](https://so.csdn.net/so/search?q=Anaconda3&spm=1001.2101.3001.7020) 使用 conda activate 报错 usage: conda-script.py \[-h\] \[--no-plugins\] \[-V\] COMMAND

- [报错内容](#报错内容)
- [解决方法](#解决方法)

## 报错内容

```powershell
usage: conda-script.py [-h] [--no-plugins] [-V] COMMAND ...
conda-script.py: error: argument COMMAND: invalid choice: 'activate' (choose from 'clean', 'compare', 'config', 'create', 'info', 'init', 'install', 'list', 'notices', 'package', 'remove', 'uninstall', 'rename', 'run', 'search', 'update', 'upgrade', 'build', 'content-trust', 'convert', 'debug', 'develop', 'doctor', 'index', 'inspect', 'metapackage', 'render', 'skeleton', 'env', 'pack', 'verify', 'server', 'token', 'repo')
12
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/541d0e3869257959686846110bb274ce.png#pic_center)

## 解决方法

查看[环境变量](https://so.csdn.net/so/search?q=%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)是否含有 `xxxxxx\anaconda3\Scripts` ，若有将此路径从环境变量中移除  
添加此库将不能使用 conda activate +虚拟环境的名，激活虚拟环境，只能使用 activate +虚拟环境激活

系统默认安装路径,环境变量中的内容为 `C:\ProgramData\anaconda3\Scripts` ，去掉此处配置![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9536192da6efbc612a7a29516c69f543.png#pic_center)
