# ppt 取消只读权限

PPT 文件设置了限制编辑，打开 PPT 的时候就会提示 PPT 文件为只读方式打开，如果想要编辑需要输入密码，但是如果我们不知道密码或者忘记了，就需要使用其他方法解决问题。

方法一：

1.PPT 文件单独复制一个，将 PPT 文件后缀变成.zip

![ ](https://pic1.zhimg.com/v2-225b4faececb6ce7e208299f5d81197c_r.jpg)

2.通过压缩软件打开 zip 格式的 PPT 文件，在文件夹中找到 “presentation.xml”（可以通过压缩软件中的搜索功能直接找到文件），然后将它复制到桌面上。

![ ](https://pic3.zhimg.com/v2-198529315324738e2d29cec195b94356_r.jpg)

![ ](https://pic4.zhimg.com/v2-9983b65094b2ad4b69ee21a37ec8999f_r.jpg)

3.presentation.xml 文件打开方式选择文本文件 txt，找到并删除“<P:modify ……>”此行，保存。

![ ](https://pic2.zhimg.com/v2-7ba457d47008feac168275d7c64e4df9_r.jpg)

4.txt 更改回 xml 后缀，将此文件复制至 PPT 文件夹，zip 更改后缀为 pptx。再打开就是可编辑 ppt 文件了。
