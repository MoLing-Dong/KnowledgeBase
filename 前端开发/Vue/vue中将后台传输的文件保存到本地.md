# vue 中将后台传输的文件保存到本地

> 后台以文件流的形式给我们返回文件
> 我们接到这个文件需要将这个文件保存到我们的本地中
> 在实现这个功能中用到了 saveAs.js
> 使用之前先

```shell
npm install file-saver -s
```

复制代码
在需要的文件中引入

```js
import { saveAs } from "file-saver";
```

复制代码
举个例子说明一下具体怎么实现的：

````js
import { saveAs } from 'file-saver';
await api
     .get('api', 参数)
     .then((response) => {
          //使用Blob构造函数转换
          const blob = new Blob([response.data], { type: 'blob' });
          //文件后缀一定要加上哦
          const fileName = '文件名.后缀';
          saveAs(blob, fileName);
        })
        .catch((error) => {
          console.log(error);
        });

````
