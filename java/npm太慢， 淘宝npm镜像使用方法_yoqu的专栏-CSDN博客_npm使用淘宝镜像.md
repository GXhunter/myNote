# npm太慢， 淘宝npm镜像使用方法_yoqu的专栏-CSDN博客_npm使用淘宝镜像
淘宝 npm 地址： [http://npm.taobao.org/](http://npm.taobao.org/)

> _**如何使用**_  
> 有很多方法来配置 npm 的 registry 地址，下面根据不同情境列出几种比较常用的方法。以淘宝 npm 镜像举例：

### 1. 临时使用

```
npm --registry https://registry.npm.taobao.org install express

```

### 2. 持久使用

```
npm config set registry https://registry.npm.taobao.org

```

-   配置后可通过下面方式来验证是否成功  
    `npm config get registry`
-   或  
    `npm info express`

### 3. 通过 cnpm 使用

```
npm install -g cnpm --registry=https://registry.npm.taobao.org

```

-   使用  
    `cnpm install express`

### 4. 恢复使用

```
npm config set registry https://registry.npmjs.org

```

 [https://blog.csdn.net/quuqu/article/details/64121812](https://blog.csdn.net/quuqu/article/details/64121812)
