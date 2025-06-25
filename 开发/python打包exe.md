# Python打包exe

## 1.安装pyinstaller

```
pip install pyinstaller
```

## 2.打包

```
pyinstaller -F -w -i favicon.ico main.py
```

- -F: 打包成一个exe文件
- -w: 不带console输出控制台，window窗体格式
- -i: 指定图标
- main.py: 你要打包的文件
- 打包后的文件在dist目录下

## 3.打包成一个文件夹

```
pyinstaller -D -w -i favicon.ico main.py
```

- -D: 打包成一个文件夹
- -w: 不带console输出控制台，window窗体格式
- -i: 指定图标
- main.py: 你要打包的文件

## 4.打包成一个exe文件，带console控制台

```
pyinstaller -F -i favicon.ico main.py
```

- -F: 打包成一个exe文件
- -i: 指定图标
- main.py: 你要打包的文件
- 打包后的文件在dist目录下
- 打包后的exe文件，双击运行，会弹出控制台窗口
