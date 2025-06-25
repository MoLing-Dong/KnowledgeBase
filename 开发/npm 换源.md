```shell
//设置淘宝源
npm config set registry https://registry.npm.taobao.org

//设置公司的源
npm config set registry 公司的源

//查看源，可以看到设置过的所有的源
npm config get registry

//本次从淘宝仓库源下载
npm --registry=https://registry.npm.taobao.org install
```
