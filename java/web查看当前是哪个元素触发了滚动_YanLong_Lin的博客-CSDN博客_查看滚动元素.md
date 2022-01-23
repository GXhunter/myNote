# web查看当前是哪个元素触发了滚动_YanLong_Lin的博客-CSDN博客_查看滚动元素
# web 查看当前是哪个元素触发了滚动

![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[林焰龙](https://blog.csdn.net/YanLong_Lin) ![](https://csdnimg.cn/release/blogv2/dist/pc/img/newCurrentTime.png)
 于 2020-06-16 21:17:45 发布 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png)
 751 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png)
 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollectionActive.png)
 收藏  1 

分类专栏： [前端](https://blog.csdn.net/yanlong_lin/category_10111159.html) 文章标签： [javascript](https://so.csdn.net/so/search/s.do?q=javascript&t=blog&o=vip&s=&l=&f=&viparticle=) [html5](https://so.csdn.net/so/search/s.do?q=html5&t=blog&o=vip&s=&l=&f=&viparticle=)

版权声明：本文为博主原创文章，遵循 [CC 4.0 BY-SA](http://creativecommons.org/licenses/by-sa/4.0/) 版权协议，转载请附上原文出处链接和本声明。

本文链接：[https://blog.csdn.net/YanLong_Lin/article/details/106795610](https://blog.csdn.net/YanLong_Lin/article/details/106795610)

版权

 [![](https://img-blog.csdnimg.cn/20201014180756724.png?x-oss-process=image/resize,m_fixed,h_64,w_64)
 前端](https://blog.csdn.net/yanlong_lin/category_10111159.html "前端") 专栏收录该内容

3 篇文章 0 订阅

订阅专栏

```javascript
      function findscroller(element){
        element.onscroll=function () {
            console.log(element)
        }
        Array.from(element.children).forEach(findscroller)
  }
  findscroller(document.body)

```

![](https://img-blog.csdnimg.cn/20200616211613275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lhbkxvbmdfTGlu,size_16,color_FFFFFF,t_70)

复制到这里面 然后滚动页面  
![](https://img-blog.csdnimg.cn/20200616211659625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lhbkxvbmdfTGlu,size_16,color_FFFFFF,t_70)
.  
就可以看到触发滚动的 dom 元素了 
 [https://blog.csdn.net/YanLong_Lin/article/details/106795610](https://blog.csdn.net/YanLong_Lin/article/details/106795610)
