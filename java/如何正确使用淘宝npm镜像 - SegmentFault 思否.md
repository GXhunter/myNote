# 如何正确使用淘宝npm镜像 - SegmentFault 思否
![](https://github.com/GXhunter/myNote/blob/main/2022-2-9%2017-22-45/dec7de93-bfc0-4c03-819c-bc593da03edb.png?raw=true)

当我在一个农村的小镇上，用的是移动宽带，用 npm 去拉载一些依赖时，经常抛出

    info There appears to be trouble with your network connection. Retrying... 

哪怕我开了网络代理，也会出现，

![](https://github.com/GXhunter/myNote/blob/main/2022-2-9%2017-22-45/50da339d-d241-40ce-8dcc-6508f05c72fb.png?raw=true)

安装一个依赖非常麻烦，这时才想到用淘宝镜像代理

### 临时使用

    npm --registry https:

### 永久使用

    npm config set registry https:

### 配置 CNPM

这样的话，你用 npm 走的还是官方的，cnpm 走的代理

    npm install -g cnpm --registry=https:

### 恢复使用

    npm config set registry https:

### 验证是否设置成功

    npm info express
    or
    npm config get registry

![](https://github.com/GXhunter/myNote/blob/main/2022-2-9%2017-22-45/f5d26370-4586-4e0b-91e4-0f3cc3301bc0.jpeg?raw=true)

## NPM 相关命令

> 整理 npm 常用的一些命令，方便查看

### 持续更新 npm

你可以通过下面的命令显示 npm 当前的版本：

    npm -v

如果有需要，可以通过下面的命令更新 npm：

    npm install -g npm

当 Node 的主版本 released 之后，你也可能需要重新构建 C++ 扩展：

    npm rebuild

如果你需要管理多个版本的 node.js 和 npm，可以考虑使用 n 或者 nvm，[https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/](https://link.segmentfault.com/?enc=hwEbDr%2Bh43Q2%2BHO0%2FvLPPA%3D%3D.pVbTYEHJSfGIbOCLG%2Bq9%2FMRdbVZf1D6p4C8FHSlwKRevhlEcR06V6WH7AjgVKHN0Qilwd9IP7LDFeMdKji3%2BYA%3D%3D)，我推荐大家使用 nvm 去管理 node.js 版本

### 卸载

如需删除 node_modules 目录下面的包（package），请执行：

npm uninstall <package>:

    npm uninstall lodash

如需从 package.json 文件中删除依赖，需要在命令后添加参数 --save:

    npm uninstall --save lodash

注意：如果你将安装的包作为 "devDependency"（也就是通过 --save-dev 参数保存的），那么 --save 无法将其从 package.json 文件中删除。所以必须通过 --save-dev 参数可以将其卸载。

### 更新包

    npm update package    #更新局部模块

    npm update -g package    #更新全局模块

    npm update -g package@version   #更新全局模块 package-name 到 x.x.x 版本

### 指定版本

    npm view react versions    查看包在npm所有版本

    npm i vue@2.0.0 --save    安装指定版本

### 使用开发中的模块

当你正在开发一个模块时，会经常想在其它项目中尝试使用或者在任何一个目录运行它 (如果你的应用支持)，这时没必要将其发布到 npm，并全局安装 --- 仅需在该模块所在目录使用下面的命令：

    npn list

该命令会为模块在全局目录下创建一个符号链接。可以通过下面的命令查看模块引用：

    npm list -g --depth=0

或者：

    npm outdated -g

现在，就可以从命令行运行模块或者通过 require 在任何项目中引入该模块。

另一个选择是，可以通过文件路径在 package.json 文件中声明对该模块的依赖：

    "dependencies": {
      "myproject": "file:../myproject/"
    }

### 常见通用命令

    npm root    #查看本地安装的目录

    npm root -g    #查看全局安装的目录

    npm info package    #查看包信息

    npm ls    #查看本地安装包

    npm ls -g    #查看全局安装包，包含依赖

    npm ls -g --depth 0    #查看全局安装包，不包含依赖

    npm outdated    #列出所有不是最新版的包，可以带参数

    npm cache clean    #清除本地缓存

    npm config ls -l    #查看npm配置

    npm view package versions    #查看包的所有版本

    npm publish     #发布包

    npm access    #设置发布包的访问级别

    npm search modulNmae   #搜索包是否存在

## package.json 参数介绍

说完了 npm，自然也得说下 package.json 文件的作用，以及说明

### key 字段介绍

    name - 包名

    version - 包的版本号

    description - 包的描述

    homepage - 包的官网 url 

    author - 包的作者姓名

    contributors - 包的其他贡献者姓名

    dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node\_module 目录下

    repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上

    main - main 字段是一个模块ID，它是一个指向你程序的主要项目。就是说，如果你包的名字叫 express，然后用户安装它，然后require("express")

    keywords - 关键字

### npm i 选项–global，–save，–save-dev

    \-global: 简写 -g

        npm i express -g 为全局安装，这种就可以直接使用express命令, 否则会提示express不是内部或外部命令

    -save: 简写 -S, 作用是在package.json的dependencies字段增加或修改安装包和版本号

    -save-dev: 简写 -D, 是修改devDependencies, 这样就不用安装了某个包之后手动修改package.json

### ~ 与 ^ 版本

    版本分为: 主版本号、次版本号、补丁版本号

    "devDependencies": {
        "vue": "~2.2.2",            
        "vue-router": "^2.2.0"        
    }

### script 属性

    script属性定义的对应了一段shell脚本

    npm start 启动模块

        该命令写在package.json文件scripts的start字段中，可以自定义命令来配置一个服务器环境和安装一系列的必要程序

        "scripts": {
            "start": "gulp -ws"
        }

    npm stop 停止模块

    npm restart 重新启动模块

    你可能在开发中用到是用npm run xxx，之类的，start ，stop，restart 是几个通用命令，阔以不用加run

    npm start === npm run start

## 推荐

**npm 模块管理器**

![](https://github.com/GXhunter/myNote/blob/main/2022-2-9%2017-22-45/f11270a0-8c9a-4ea6-8d40-f6eae2be2236.png?raw=true)

详细介绍了 npm 的使用，以及参数相关配置

[https://javascript.ruanyifeng.com/nodejs/npm.html](https://link.segmentfault.com/?enc=ka3CFBFLTN5zCOnhybHqIw%3D%3D.ic8rOpgFICjvxctsJLeRhRLL%2FDO55u9%2FXZgVZ4GHPvcoCTbYdYka%2BVuzEEmV4iEXAEdcnvVJxp6pthaj4Zs0HQ%3D%3D) 
 [https://segmentfault.com/a/1190000027083723](https://segmentfault.com/a/1190000027083723)
