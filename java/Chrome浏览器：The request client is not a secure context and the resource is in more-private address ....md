# Chrome浏览器：The request client is not a secure context and the resource is in more-private address ... - 简书
### 1. 问题

最近使用 Chrome 浏览器访问公司内网某个地址时，突然报了这么个错：

> The request client is not a secure context and the resource is in more-private address space `private`.

以前都是正常的，估计最近有什么更新导致的。

### 2. 解决办法

-   在浏览器地址栏输入


    chrome://flags/#block-insecure-private-network-requests 

-   按照下图将`Block insecure private network requests.`项的`Default`改为`Disabled`即可。
    -   ![](https://upload-images.jianshu.io/upload_images/3143447-1c61c51028915cb2.png)

        image.png

### 3. 问题原因

-   设置项底下的小字：

    -   ![](https://upload-images.jianshu.io/upload_images/3143447-f97a1626892bee6a.png)

        image.png
-   报错内容：
    -   ![](https://upload-images.jianshu.io/upload_images/3143447-6138824b7776ca84.png)
               image.png 
        [https://www.jianshu.com/p/04dedb927d54](https://www.jianshu.com/p/04dedb927d54)
