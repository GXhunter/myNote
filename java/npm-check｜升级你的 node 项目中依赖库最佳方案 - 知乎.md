# npm-check｜升级你的 node 项目中依赖库最佳方案 - 知乎
## npm-check 是什么？

答：npm-check 是一个 npm 包，可以通过如下方式安装：

```text
npm install -g npm-check // 全局安装
```

当你安装完它之后，你可以使用它升级你的项目的依赖库

## 请注意，你要知道 npm-check 升级的是什么？

答：升级的是你项目目录中，package.json 中声明的依赖包，它不会升级你依赖的依赖——也就是你的 node_module 中的那些依赖包。

## 我们需要使用什么命令升级？

安装完成后，我们使用如下命令可以进行检查更新：

![](https://pic2.zhimg.com/v2-b06af3a968da40f83f0c2c2222d9bedd_b.jpg)

图片来源：[https://segmentfault.com/a/1190000011085967](https://segmentfault.com/a/1190000011085967)

我们还可以使用如下命令选择需要的更新进行更新：

![](https://pic1.zhimg.com/v2-e7f9b87e01e6331826bb6fb493c022cc_b.jpg)

图片来源：[https://segmentfault.com/a/1190000011085967](https://segmentfault.com/a/1190000011085967)

下面是 npm-check 指令列表：

```text
-u, --update       显示一个交互式UI，用于选择要更新的模块，并自动更新"package.json"内包版本号信息
-g, --global       检查全局下的包
-s, --skip-unused  忽略对未使用包的更新检查
-p, --production   忽略对"devDependencies"下的包的检查
-d, --dev-only     忽略对"dependencies"下的包的检查
-i, --ignore       忽略对指定包的检查.
-E, --save-exact   将确切的包版本存至"package.json"(注意，此命令将存储'x.y.z'而不是'^x.y.z')
```

 [https://zhuanlan.zhihu.com/p/114265412](https://zhuanlan.zhihu.com/p/114265412)
