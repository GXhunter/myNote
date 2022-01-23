# 祖大俊的个人页面 - OSCHINA - 中文开源技术交流社区
32 全部博文

[32 全部博文](?tab=newest&catalogId=0&sortType=time) [7 SpringMVC4.x 源码分析](?tab=newest&catalogId=5779492&sortType=time) [2 Spring](?tab=newest&catalogId=3532896&sortType=time) [23 Mybatis3.4.x 技术内幕](?tab=newest&catalogId=3532897&sortType=time)

[热门博文](?tab=popular)

按最后发布时间

[按最后发布时间](?tab=newest&catalogId=0&sortType=time) [按阅读量](?tab=newest&catalogId=0&sortType=vc)

创作历程

[全部](?tab=newest)

2018 年 (7 篇)

[7 2018/06](?tab=history&year=2018&month=06&history=201806&monthCount=7)

2016 年 (25 篇)

[4 2016/09](?tab=history&year=2016&month=09&history=201609&monthCount=4) [3 2016/08](?tab=history&year=2016&month=08&history=201608&monthCount=3) [1 2016/06](?tab=history&year=2016&month=06&history=201606&monthCount=1) [9 2016/05](?tab=history&year=2016&month=05&history=201605&monthCount=9) [8 2016/04](?tab=history&year=2016&month=04&history=201604&monthCount=8)

\[

原

SpringMVC4.x 源码分析（七）：使用 XStream 处理 xml 请求和响应消息实战]([https://my.oschina.net/zudajun/blog/1833033](https://my.oschina.net/zudajun/blog/1833033))

本节使用 MarshallingHttpMessageConverter+xstream 来处理 xml 请求和响应消息，首先加入 spring-oxm 和 xstream 的依赖包。 <dependencies> <dependency> <groupId>junit</groupId> &lt;a...

[Spring](https://my.oschina.net/zudajun?q=Spring)[XStream](https://my.oschina.net/zudajun?q=XStream)

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/20 18:49

1.9K

[0](https://my.oschina.net/zudajun/blog/1833033#comments)

 [![](https://oscimg.oschina.net/oscnet/7730dadab1ea06b55739cd93009a23552d6.jpg)](https://my.oschina.net/zudajun/blog/1833033) 

\[

原

SpringMVC4.x 源码分析（六）：消息转换器自动转换 json、xml 原理分析]([https://my.oschina.net/zudajun/blog/1832442](https://my.oschina.net/zudajun/blog/1832442))

先来一个简单的例子，首先在 pom.xml 内加入处理 json、xml 的 jackson 依赖包。 <dependency> <groupId>com.fasterxml.jackson.core</groupId> <artifactId>jackson-core</artifactId>...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/19 21:12

1.7K

[0](https://my.oschina.net/zudajun/blog/1832442#comments)

 [![](https://oscimg.oschina.net/oscnet/dace64267c28c9b862d3bbc708bd7ed9a1f.jpg)](https://my.oschina.net/zudajun/blog/1832442) 

\[

原

SpringMVC4.x 源码分析（五）：request 请求寻址 HandlerMethod 原理]([https://my.oschina.net/zudajun/blog/1829630](https://my.oschina.net/zudajun/blog/1829630))

mvc:annotation-driven 会将每一个 Controllor 内的被 @RequestMapping 注解标注的方法解析为 HandlerMethod 对象，并存储在 RequestMappingHandlerMapping 的 MappingRegistry 属性中，寻址就是根据 req...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/13 16:07

1.4K

[0](https://my.oschina.net/zudajun/blog/1829630#comments)

 [![](https://oscimg.oschina.net/oscnet/d353d24e504c56213ac10fbcbdb694a2b5e.jpg)](https://my.oschina.net/zudajun/blog/1829630) 

\[

原

SpringMVC4.x 源码分析（四）：一个 request 请求的完整流程和各组件介绍]([https://my.oschina.net/zudajun/blog/1828434](https://my.oschina.net/zudajun/blog/1828434))

本节主要目标是探查一个 request 请求的完整流程，以及流程中各种 web 组件的简单介绍，组件的细节，将在后续组件专题文章中详细介绍。 DispatcherServlet 初始化完毕后，我们发现它注册 2 个 Handl...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/11 21:36

1.9K

[0](https://my.oschina.net/zudajun/blog/1828434#comments)

 [![](https://oscimg.oschina.net/oscnet/caa10a96e26fff5491deb535cde0c6dfdd3.jpg)](https://my.oschina.net/zudajun/blog/1828434) 

\[

原

SpringMVC4.x 源码分析（三）：解析 Controllor 和 HandlerMethod]([https://my.oschina.net/zudajun/blog/1827594](https://my.oschina.net/zudajun/blog/1827594))

回顾本例 springmvc-config.xml 的配置： &lt;context:component-scan base-package="com.spring"/> &lt;mvc:annotation-driven /> component-scan：扫描指定包下被 @Component，@Service，@Controll...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/10 11:41

1.9K

[0](https://my.oschina.net/zudajun/blog/1827594#comments)

 [![](https://oscimg.oschina.net/oscnet/13be511264fecb9d36f3c07ef1d4d64a13a.jpg)](https://my.oschina.net/zudajun/blog/1827594) 

\[

原

SpringMVC4.x 源码分析（二）：DispatcherServlet 初始化过程]([https://my.oschina.net/zudajun/blog/1827341](https://my.oschina.net/zudajun/blog/1827341))

DispatcherServlet 的类继承图。 （Made In IntelliJ IDEA） DispatcherServlet 是一个 Servlet，那么它就遵循 Servlet 的生命周期。如上图所示，DispatcherServlet 还实现了 Spring IOC 的 Aware 接口...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/09 15:31

3.9K

[2](https://my.oschina.net/zudajun/blog/1827341#comments)

 [![](https://oscimg.oschina.net/oscnet/8e1f085fe230316976c5ec6eb7e99aa02a3.jpg)](https://my.oschina.net/zudajun/blog/1827341) 

\[

原

SpringMVC4.x 源码分析（一）：SpringMVC 入门例子]([https://my.oschina.net/zudajun/blog/1827195](https://my.oschina.net/zudajun/blog/1827195))

Spring+SpringMVC+MyBatis 简称 SSM，它们是如此重要，分析 SpringMVC 底层源码实现原理，绝对物有所值，对 Spring Boot 及 Spring Cloud 的学习也大有裨益。 IDE 工具：IntelliJ IDEA 2018 Java：JDK...

[SpringMVC4.x 源码分析](https://my.oschina.net/zudajun?tab=newest&catalogId=5779492)

2018/06/09 09:18

3.9K

[0](https://my.oschina.net/zudajun/blog/1827195#comments)

 [![](https://oscimg.oschina.net/oscnet/743524cb32bf53a6f0dddf932eaa9c14d66.jpg)](https://my.oschina.net/zudajun/blog/1827195) 

\[

原

荐

Mybatis3.4.x 技术内幕（二十三）：Mybatis 面试问题集锦（大结局）]([https://my.oschina.net/zudajun/blog/747682](https://my.oschina.net/zudajun/blog/747682))

找 工作、跳槽，或者招聘，Mybatis 是必备基础知识，看看你能回答几个。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[面试题](https://my.oschina.net/zudajun?q=面试题)[面试问题](https://my.oschina.net/zudajun?q=面试问题)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/09/17 20:16

6.1W

[36](https://my.oschina.net/zudajun/blog/747682#comments)

\[

原

Mybatis3.4.x 技术内幕（二十二）：Mybatis 一级、二级缓存原理分析]([https://my.oschina.net/zudajun/blog/747499](https://my.oschina.net/zudajun/blog/747499))

Mybatis 的一级缓存，指的是 SqlSession 级别的缓存，默认开启；Mybatis 的二级缓存，指的是 SqlSessionFactory 级别的缓存，需要配置。缓存是针对 select 来说的。 1、一级缓存 <configuration>   &lt;...

[MyBatis](https://my.oschina.net/zudajun?q=MyBatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[一级缓存](https://my.oschina.net/zudajun?q=一级缓存)[二级缓存](https://my.oschina.net/zudajun?q=二级缓存)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/09/17 13:06

7.5K

[6](https://my.oschina.net/zudajun/blog/747499#comments)

\[

原

Mybatis3.4.x 技术内幕（二十一）：参数设置、结果封装、级联查询、延迟加载原理分析]([https://my.oschina.net/zudajun/blog/747283](https://my.oschina.net/zudajun/blog/747283))

Mybatis 在执行查询时，其参数设置、结果封装、级联查询、延迟加载，是最基本的功能和用法，我们有必要了解其工作原理，重点阐述级联查询和延迟加载。 1、MetaObject MetaObject 用于反射创建对...

[MyBatis](https://my.oschina.net/zudajun?q=MyBatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[级联查询](https://my.oschina.net/zudajun?q=级联查询)[延迟加载](https://my.oschina.net/zudajun?q=延迟加载)[关联查询](https://my.oschina.net/zudajun?q=关联查询)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/09/16 11:00

8.1K

[1](https://my.oschina.net/zudajun/blog/747283#comments)

\[

原

Mybatis3.4.x 技术内幕（二十）：PageHelper 分页插件源码及原理剖析]([https://my.oschina.net/zudajun/blog/745232](https://my.oschina.net/zudajun/blog/745232))

com.github.pagehelper.PageHelper 是一款好用的开源免费的 Mybatis 第三方物理分页插件。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[PageHelper](https://my.oschina.net/zudajun?q=PageHelper)[分页](https://my.oschina.net/zudajun?q=分页)[源码解析](https://my.oschina.net/zudajun?q=源码解析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/09/10 20:05

2.7W

[7](https://my.oschina.net/zudajun/blog/745232#comments)

 [![](http://static.oschina.net/uploads/space/2016/0910/173956_lHiB_2727738.jpg)](https://my.oschina.net/zudajun/blog/745232) 

\[

原

荐

Mybatis3.4.x 技术内幕（十九）：Mybatis 之 plugin 插件设计原理]([https://my.oschina.net/zudajun/blog/738973](https://my.oschina.net/zudajun/blog/738973))

Mybatis 的 plugin 插件功能是通过 JDK 动态代理来实现拦截器功能的，只能拦截 ParameterHandler、ResultSetHandler、StatementHandler、Executor 共 4 种接口对象的方法。...

[MyBatis](https://my.oschina.net/zudajun?q=MyBatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[插件](https://my.oschina.net/zudajun?q=插件)[plugin](https://my.oschina.net/zudajun?q=plugin)[源码解析](https://my.oschina.net/zudajun?q=源码解析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/08/28 17:28

1.4W

[4](https://my.oschina.net/zudajun/blog/738973#comments)

\[

原

Mybatis3.4.x 技术内幕（十八）：Mybatis 之动态 Sql 设计原本（下）]([https://my.oschina.net/zudajun/blog/735731](https://my.oschina.net/zudajun/blog/735731))

Mybatis 的动态 Sql 功能，是通过 XML 标签结合 OGNL 表达式来完成的。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[动态 sql](https://my.oschina.net/zudajun?q=动态sql)[源码解析](https://my.oschina.net/zudajun?q=源码解析)[深入剖析](https://my.oschina.net/zudajun?q=深入剖析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/08/21 13:08

4.9K

[1](https://my.oschina.net/zudajun/blog/735731#comments)

\[

原

荐

Mybatis3.4.x 技术内幕（十七）：Mybatis 之动态 Sql 设计原本（上）]([https://my.oschina.net/zudajun/blog/735553](https://my.oschina.net/zudajun/blog/735553))

Mybatis 的动态 Sql 功能，是通过 XML 标签结合 OGNL 表达式来完成的。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[源码分析](https://my.oschina.net/zudajun?q=源码分析)[动态 sql](https://my.oschina.net/zudajun?q=动态sql)[深入剖析](https://my.oschina.net/zudajun?q=深入剖析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/08/20 18:40

2W

[2](https://my.oschina.net/zudajun/blog/735553#comments)

 [![](http://static.oschina.net/uploads/space/2016/0820/155634_seLv_2727738.jpg)](https://my.oschina.net/zudajun/blog/735553) 

\[

原

Mybatis3.4.x 技术内幕（十六）：Mybatis 之 sqlFragment（可复用的 sql 片段）]([https://my.oschina.net/zudajun/blog/687326](https://my.oschina.net/zudajun/blog/687326))

今天，我们将分析 Mybatis 之 sqlFragment，可以翻译为 sql 片段，它的存在价值在于可复用 sql 片段，避免到处重复编写。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[3.4.0](https://my.oschina.net/zudajun?q=3.4.0)[sqlFragment](https://my.oschina.net/zudajun?q=sqlFragment)[源码分析](https://my.oschina.net/zudajun?q=源码分析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/06/05 12:41

9.8K

[0](https://my.oschina.net/zudajun/blog/687326#comments)

 [![](http://static.oschina.net/uploads/space/2016/0605/123022_ps7E_2727738.jpg)](https://my.oschina.net/zudajun/blog/687326) 

\[

原

荐

Mybatis3.3.x 技术内幕（十五）：Mybatis 之 foreach 批量 insert，返回主键 id 列表（修复 Mybatis 返回 null 的 bug）]([https://my.oschina.net/zudajun/blog/674946](https://my.oschina.net/zudajun/blog/674946))

Mybatis 在执行批量插入时，如果使用的是 for 循环逐一插入，那么可以正确返回主键 id。如果使用动态 sql 的 foreach 循环，那么返回的主键 id 列表，可能为 null，这让很多人感到困惑；本文将分析问题产...

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[批量插入](https://my.oschina.net/zudajun?q=批量插入)[批量 insert](https://my.oschina.net/zudajun?q=批量insert)[返回主键](https://my.oschina.net/zudajun?q=返回主键)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/05/13 22:47

7.7W

[19](https://my.oschina.net/zudajun/blog/674946#comments)

\[

原

Mybatis3.3.x 技术内幕（十四）：Mybatis 之 KeyGenerator]([https://my.oschina.net/zudajun/blog/673612](https://my.oschina.net/zudajun/blog/673612))

在 Mybatis 中，执行 insert 操作时，如果我们希望返回数据库生成的自增主键值，那么就需要使用到 KeyGenerator 对象。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[KeyGenerator](https://my.oschina.net/zudajun?q=KeyGenerator)[源码分析](https://my.oschina.net/zudajun?q=源码分析)[深入解析](https://my.oschina.net/zudajun?q=深入解析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/05/11 19:26

1W

[3](https://my.oschina.net/zudajun/blog/673612#comments)

 [![](http://static.oschina.net/uploads/space/2016/0511/161009_hmaI_2727738.jpg)](https://my.oschina.net/zudajun/blog/673612) 

\[

原

Mybatis3.3.x 技术内幕（十三）：Mybatis 之 RowBounds 分页原理]([https://my.oschina.net/zudajun/blog/671446](https://my.oschina.net/zudajun/blog/671446))

Mybatis 可以通过传递 RowBounds 对象，来进行数据库数据分页操作，然而遗憾的是，该分页操作是对 ResultSet 结果集进行分页，也就是人们常说的逻辑分页，而非物理分页。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[分页](https://my.oschina.net/zudajun?q=分页)[源码分析](https://my.oschina.net/zudajun?q=源码分析)[RowBounds](https://my.oschina.net/zudajun?q=RowBounds)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/05/08 10:59

1.5W

[2](https://my.oschina.net/zudajun/blog/671446#comments)

\[

原

Mybatis3.3.x 技术内幕（十二）：Mybatis 之 TypeHandler]([https://my.oschina.net/zudajun/blog/671075](https://my.oschina.net/zudajun/blog/671075))

Mybatis 中的 TypeHandler 有两个功能，一个是完成 javaType 至 jdbcType 的转换，另外一个是完成 jdbcType 至 javaType 的转换。

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/05/06 21:03

8.7K

[0](https://my.oschina.net/zudajun/blog/671075#comments)

 [![](http://static.oschina.net/uploads/space/2016/0506/100431_stw1_2727738.jpg)](https://my.oschina.net/zudajun/blog/671075) 

\[

原

Mybatis3.3.x 技术内幕（十一）：执行一个 Sql 命令的完整流程]([https://my.oschina.net/zudajun/blog/670373](https://my.oschina.net/zudajun/blog/670373))

在 Mybatis 中，执行一个 Sql，会经历若干步骤，本节将介绍执行一个 Sql 命令的完整流程。

[Mybatis](https://my.oschina.net/zudajun?q=Mybatis)[技术内幕](https://my.oschina.net/zudajun?q=技术内幕)[完整流程](https://my.oschina.net/zudajun?q=完整流程)[源码分析](https://my.oschina.net/zudajun?q=源码分析)[深入解析](https://my.oschina.net/zudajun?q=深入解析)

[Mybatis3.4.x 技术内幕](https://my.oschina.net/zudajun?tab=newest&catalogId=3532897)

2016/05/05 18:46

1W

[2](https://my.oschina.net/zudajun/blog/670373#comments)

 [![](http://static.oschina.net/uploads/space/2016/0505/181036_SgDu_2727738.jpg)](https://my.oschina.net/zudajun/blog/670373) 

没有更多内容

加载失败，请刷新页面

加载更多

[下一页](https://my.oschina.net/zudajun/widgets/_space_index_newest_blog?catalogId=0&q=&p=2&sortType=time&type=ajax) 
 [https://my.oschina.net/zudajun](https://my.oschina.net/zudajun)
