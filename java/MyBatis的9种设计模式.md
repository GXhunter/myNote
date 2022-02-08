# MyBatis的9种设计模式
![](https://mmbiz.qpic.cn/mmbiz_jpg/TeYk478W36CQcw1U3r2xoq95mGKaSMQQGlmS1rLF4ugibVehZI7eqAkLHkhEKYHHfkNia45bKPy5IvIDNvVbaCxw/640?wx_fmt=jpeg)

程序员的成长之路

互联网 / 程序员 / 成长 / 职场 

关注

阅读本文大概需要 12.4 分钟。

老规矩，接了推文都会给大家发红包，文末直抢！

本文转载自： [http://www.crazyant.net/2022.html](http://www.crazyant.net/2022.html)

虽然我们都知道有 26 个设计模式，但是大多停留在概念层面，真实开发中很少遇到，Mybatis 源码中使用了大量的设计模式，阅读源码并观察设计模式在其中的应用，能够更深入的理解设计模式。  

Mybatis 至少遇到了以下的设计模式的使用：

1.  **Builder 模式** :

    例如 `SqlSessionFactoryBuilder`、`XMLConfigBuilder`、`XMLMapperBuilder`、`XMLStatementBuilder`、`CacheBuilder`；
2.  **工厂模式** :

    例如`SqlSessionFactory`、`ObjectFactory`、`MapperProxyFactory`；
3.  **单例模式** : 例如`ErrorContext`和`LogFactory`；
4.  **代理模式** :Mybatis 实现的核心，比如`MapperProxy`、`ConnectionLogger`，用的 jdk 的动态代理；还有`executor.loader`包使用了 cglib 或者 javassist 达到延迟加载的效果；
5.  **组合模式** : 例如`SqlNode`和各个子类`ChooseSqlNode`等；
6.  **模板方法模式** : 例如`BaseExecutor`和`SimpleExecutor`，还有`BaseTypeHandler`和所有的子类例如`IntegerTypeHandler`；
7.  **适配器模式** : 例如 Log 的 Mybatis 接口和它对 jdbc、log4j 等各种日志框架的适配实现；
8.  **装饰者模式** : 例如`cache`包中的`cache.decorators`子包中等各个装饰者的实现；
9.  **迭代器模式** : 例如迭代器模式`PropertyTokenizer`；

接下来挨个模式进行解读，先介绍模式自身的知识，然后解读在 Mybatis 中怎样应用了该模式。

## 1、Builder 模式

Builder 模式的定义是 “将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。”，它属于创建类模式，一般来说，如果一个对象的构建比较复杂，超出了构造函数所能包含的范围，就可以使用工厂模式和 Builder 模式，相对于工厂模式会产出一个完整的产品，Builder 应用于更加复杂的对象的构建，甚至只会构建产品的一个部分。《effective-java》中第 2 条也提到：**遇到多个构造器参数时，考虑用构建者 (Builder) 模式**。

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguxLFUaIxVlliaHricUeibYvNJRs2u3PJZ8kyGzDonJXbEpfXPbLSlPj3yw/640?wx_fmt=png)

Builder 模式

在 Mybatis 环境的初始化过程中，`SqlSessionFactoryBuilder`会调用`XMLConfigBuilder`读取所有的`MybatisMapConfig.xml`和所有的`*Mapper.xml`文件，构建 Mybatis 运行的核心对象`Configuration`对象，然后将该`Configuration`对象作为参数构建一个`SqlSessionFactory`对象。

其中`XMLConfigBuilder`在构建`Configuration`对象时，也会调用`XMLMapperBuilder`用于读取`*.Mapper`文件，而`XMLMapperBuilder`会使用`XMLStatementBuilder`来读取和 build 所有的 SQL 语句。

在这个过程中，有一个相似的特点，就是这些 Builder 会读取文件或者配置，然后做大量的 XpathParser 解析、配置或语法的解析、反射生成对象、存入结果缓存等步骤，这么多的工作都不是一个构造函数所能包括的，因此大量采用了 Builder 模式来解决。

对于 builder 的具体类，方法都大都用`build*`开头，比如`SqlSessionFactoryBuilder`为例，它包含以下方法：

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguY1kYdjdtpD9N5Y6Q6msHYBaakqklmUqPM2Skt5iak8yE6Z3I7A9OU2Q/640?wx_fmt=png)

SqlSessionFactoryBuilder

即根据不同的输入参数来构建`SqlSessionFactory`这个工厂对象。

## 2、工厂模式

在 Mybatis 中比如`SqlSessionFactory`使用的是工厂模式，该工厂没有那么复杂的逻辑，是一个简单工厂模式。

简单工厂模式 (Simple Factory Pattern)：又称为静态工厂方法 (Static Factory Method) 模式，它属于类创建型模式。在简单工厂模式中，可以根据参数的不同返回不同类的实例。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguDal8xOeDAicSNlVf1cK8fa07k3KicWrBZzA33l7PzLWTUnUFe63AnCOg/640?wx_fmt=jpeg)

简单工厂模式

`SqlSession`可以认为是一个 Mybatis 工作的核心的接口，通过这个接口可以执行执行 SQL 语句、获取 Mappers、管理事务。类似于连接 MySQL 的`Connection`对象。

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguhqdxqBum4o1ibicjJY3ohOiaPib4lKPx5RibVP4HFAKgueHF96RmiaRxgJwQ/640?wx_fmt=png)

SqlSessionFactory

可以看到，该 Factory 的`openSession（）`方法重载了很多个，分别支持`autoCommit`、`Executor`、`Transaction` 等参数的输入，来构建核心的`SqlSession`对象。

在`DefaultSqlSessionFactory`的默认工厂实现里，有一个方法可以看出工厂怎么产出一个产品：

    private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level,  
            boolean autoCommit) {  
        Transaction tx = null;  
        try {  
            final Environment environment = configuration.getEnvironment();  
            final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);  
            tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);  
            final Executor executor = configuration.newExecutor(tx, execType);  
            return new DefaultSqlSession(configuration, executor, autoCommit);  
        } catch (Exception e) {  
            closeTransaction(tx); // may have fetched a connection so lets call  
                                    // close()  
            throw ExceptionFactory.wrapException("Error opening session.  Cause:" + e, e);  
        } finally {  
            ErrorContext.instance().reset();  
        }  
    }  

这是一个 openSession 调用的底层方法，该方法先从 configuration 读取对应的环境配置，然后初始化`TransactionFactory`获得一个`Transaction`对象，然后通过`Transaction`获取一个`Executor`对象，最后通过 configuration、Executor、是否 autoCommit 三个参数构建了`SqlSession`。

在这里其实也可以看到端倪，`SqlSession`的执行，其实是委托给对应的`Executor`来进行的。

而对于`LogFactory`，它的实现代码：

public final class LogFactory {  
    private static Constructor&lt;? extends Log> logConstructor;

    private LogFactory() {  
        // disable construction  
    }

    public static Log getLog(Class&lt;?> aClass) {  
        return getLog(aClass.getName());  
    }

这里有个特别的地方，是 Log 变量的的类型是`Constructor<? extendsLog>`，也就是说该工厂生产的不只是一个产品，而是具有 Log 公共接口的一系列产品，比如`Log4jImpl`、`Slf4jImpl`等很多具体的 Log。

## 3、单例模式

单例模式 (Singleton Pattern)：单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。

单例模式的要点有三个：一是某个类只能有一个实例；二是它必须自行创建这个实例；三是它必须自行向整个系统提供这个实例。单例模式是一种对象创建型模式。单例模式又名单件模式或单态模式。

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcggupWSWZciam1KmsfB7iciaW24ts5gCfFArc3BDg0xNe07ABuRmruYGpuw6Q/640?wx_fmt=jpeg)

单例模式

在 Mybatis 中有两个地方用到单例模式，`ErrorContext`和`LogFactory`，其中`ErrorContext`是用在每个线程范围内的单例，用于记录该线程的执行环境错误信息，而`LogFactory`则是提供给整个 Mybatis 使用的日志工厂，用于获得针对项目配置好的日志对象。

`ErrorContext`的单例实现代码：

public class ErrorContext {

    private static final ThreadLocal<ErrorContext> LOCAL = new ThreadLocal<ErrorContext>();

    private ErrorContext() {  
    }

    public static ErrorContext instance() {  
        ErrorContext context = LOCAL.get();  
        if (context == null) {  
            context = new ErrorContext();  
            LOCAL.set(context);  
        }  
        return context;  
    }

构造函数是 private 修饰，具有一个 static 的局部 instance 变量和一个获取 instance 变量的方法，在获取实例的方法中，先判断是否为空如果是的话就先创建，然后返回构造好的对象。

只是这里有个有趣的地方是，LOCAL 的静态实例变量使用了`ThreadLocal`修饰，也就是说它属于每个线程各自的数据，而在`instance()`方法中，先获取本线程的该实例，如果没有就创建该线程独有的`ErrorContext`。

## 4、代理模式

代理模式可以认为是 Mybatis 的核心使用的模式，正是由于这个模式，我们只需要编写`Mapper.java`接口，不需要实现，由 Mybatis 后台帮我们完成具体 SQL 的执行。

代理模式 (Proxy Pattern) ：给某一个对象提供一个代 理，并由代理对象控制对原对象的引用。代理模式的英 文叫做 Proxy 或 Surrogate，它是一种对象结构型模式。

代理模式包含如下角色：

-   Subject: 抽象主题角色
-   Proxy: 代理主题角色
-   RealSubject: 真实主题角色

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcggucTX4wDDZjD8P1LZib051ZulBtdMzxowWFathl2fUrrbHzTx2NJ5ibrqg/640?wx_fmt=jpeg)

代理模式

这里有两个步骤，第一个是提前创建一个 Proxy，第二个是使用的时候会自动请求 Proxy，然后由 Proxy 来执行具体事务；

当我们使用`Configuration`的`getMapper`方法时，会调用`mapperRegistry.getMapper`方法，而该方法又会调用`mapperProxyFactory.newInstance(sqlSession)`来生成一个具体的代理：

/\*\*  
 \* @author Lasse Voss  
 \*/  
public class MapperProxyFactory&lt;T> {

    private final Class<T> mapperInterface;  
    private final Map&lt;Method, MapperMethod> methodCache = new ConcurrentHashMap&lt;Method, MapperMethod>();

    public MapperProxyFactory(Class<T> mapperInterface) {  
        this.mapperInterface = mapperInterface;  
    }

    public Class<T> getMapperInterface() {  
        return mapperInterface;  
    }

    public Map&lt;Method, MapperMethod> getMethodCache() {  
        return methodCache;  
    }

    @SuppressWarnings("unchecked")  
    protected T newInstance(MapperProxy<T> mapperProxy) {  
        return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class\[] {mapperInterface},  
                mapperProxy);  
    }

    public T newInstance(SqlSession sqlSession) {  
        final MapperProxy<T> mapperProxy = new MapperProxy<T>(sqlSession, mapperInterface, methodCache);  
        return newInstance(mapperProxy);  
    }

}

在这里，先通过`T newInstance(SqlSession sqlSession)`方法会得到一个`MapperProxy`对象，然后调用`T newInstance(MapperProxy<T> mapperProxy)`生成代理对象然后返回。

而查看`MapperProxy`的代码，可以看到如下内容：

public class MapperProxy&lt;T> implements InvocationHandler, Serializable {

    @Override  
    public Object invoke(Object proxy, Method method, Object\[] args) throws Throwable {  
        try {  
            if (Object.class.equals(method.getDeclaringClass())) {  
                return method.invoke(this, args);  
            } else if (isDefaultMethod(method)) {  
                return invokeDefaultMethod(proxy, method, args);  
            }  
        } catch (Throwable t) {  
            throw ExceptionUtil.unwrapThrowable(t);  
        }  
        final MapperMethod mapperMethod = cachedMapperMethod(method);  
        return mapperMethod.execute(sqlSession, args);  
    }

非常典型的，该`MapperProxy`类实现了`InvocationHandler`接口，并且实现了该接口的`invoke`方法。

通过这种方式，我们只需要编写`Mapper.java`接口类，当真正执行一个`Mapper`接口的时候，就会转发给`MapperProxy.invoke`方法，而该方法则会调用后续的`sqlSession.cud>executor.execute>prepareStatement`等一系列方法，完成 SQL 的执行和返回。

## 5、组合模式

组合模式组合多个对象形成树形结构以表示 “整体 - 部分” 的结构层次。

组合模式对单个对象 (叶子对象) 和组合对象 (组合对象) 具有一致性，它将对象组织到树结构中，可以用来描述整体与部分的关系。同时它也模糊了简单元素 (叶子对象) 和复杂元素 (容器对象) 的概念，使得客户能够像处理简单元素一样来处理复杂元素，从而使客户程序能够与复杂元素的内部结构解耦。

在使用组合模式中需要注意一点也是组合模式最关键的地方：叶子对象和组合对象实现相同的接口。这就是组合模式能够将叶子节点和对象节点进行一致处理的原因。

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguy6PcF6dw3gmtYLb1GKoRawa8aictKop7ef8yjOlFdyXbzFRvr5ibf05Q/640?wx_fmt=png)

组合模式

Mybatis 支持动态 SQL 的强大功能，比如下面的这个 SQL：

&lt;update id\\="update" parameterType\\="org.format.dynamicproxy.mybatis.bean.User">  
    UPDATE users  
    &lt;trim prefix\\="SET" prefixOverrides\\=",">  
        &lt;if test\\="name != null and name !=''">  
            name = #{name}  
        &lt;/if>  
        &lt;if test\\="age != null and age !=''">  
            , age = #{age}  
        &lt;/if>  
        &lt;if test\\="birthday != null and birthday !=''">  
            , birthday = #{birthday}  
        &lt;/if>  
    &lt;/trim>  
    where id = ${id}  
&lt;/update>  

在这里面使用到了 trim、if 等动态元素，可以根据条件来生成不同情况下的 SQL；

在`DynamicSqlSource.getBoundSql`方法里，调用了`rootSqlNode.apply(context)`方法，`apply`方法是所有的动态节点都实现的接口：

public interface SqlNode {  
    boolean apply(DynamicContext context);  
}  

对于实现该`SqlSource`接口的所有节点，就是整个组合模式树的各个节点：

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguwibbsZ9tRwuTswL12lAicluoxwYPKSxWqCHibFMTgda3mSeib3e9x8ZhYQ/640?wx_fmt=png)

SqlNode

组合模式的简单之处在于，所有的子节点都是同一类节点，可以递归的向下执行，比如对于 TextSqlNode，因为它是最底层的叶子节点，所以直接将对应的内容 append 到 SQL 语句中：

    @Override  
    public boolean apply(DynamicContext context) {  
        GenericTokenParser parser = createParser(new BindingTokenParser(context, injectionFilter));  
        context.appendSql(parser.parse(text));  
        return true;  
    }  

但是对于 IfSqlNode，就需要先做判断，如果判断通过，仍然会调用子元素的 SqlNode，即`contents.apply`方法，实现递归的解析。

@Override  
public boolean apply(DynamicContext context) {  
    if (evaluator.evaluateBoolean(test, context.getBindings())) {  
        contents.apply(context);  
        return true;  
    }  
    return false;  
}   

## 6、模板方法模式

模板方法模式是所有模式中最为常见的几个模式之一，是基于继承的代码复用的基本技术。

模板方法模式需要开发抽象类和具体子类的设计师之间的协作。一个设计师负责给出一个算法的轮廓和骨架，另一些设计师则负责给出这个算法的各个逻辑步骤。代表这些具体逻辑步骤的方法称做基本方法 (primitive method)；而将这些基本方法汇总起来的方法叫做模板方法 (template method)，这个设计模式的名字就是从此而来。

模板类定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguYPCmgKUlenfTHEVicUFEWZfa2rnEUuY8zVzXrIe0ZGuCDIEsqwDz8dQ/640?wx_fmt=png)

模板方法模式

在 Mybatis 中，sqlSession 的 SQL 执行，都是委托给 Executor 实现的，Executor 包含以下结构：

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguKxRU0z3Q6cARFbbcnnYBaEsSm1jJ3aMcxxmichSDpw9fq58hlz1Kksw/640?wx_fmt=png)

Executor 接口

其中的 BaseExecutor 就采用了模板方法模式，它实现了大部分的 SQL 执行逻辑，然后把以下几个方法交给子类定制化完成：

    @Override  
    public boolean apply(DynamicContext context) {  
        if (evaluator.evaluateBoolean(test, context.getBindings())) {  
            contents.apply(context);  
            return true;  
        }  
        return false;  
    }  

该模板方法类有几个子类的具体实现，使用了不同的策略：

-   简单`SimpleExecutor`：每执行一次`update`或`select`，就开启一个`Statement`对象，用完立刻关闭`Statement`对象。（可以是`Statement`或`PrepareStatement`对象）
-   重用`ReuseExecutor`：执行`update`或`select`，以 sql 作为 key 查找`Statement`对象，存在就使用，不存在就创建，用完后，不关闭`Statement`对象，而是放置于`Map`内，供下一次使用。（可以是`Statement`或`PrepareStatement`对象）
-   批量`BatchExecutor`：执行 update（没有 select，JDBC 批处理不支持 select），将所有 sql 都添加到批处理中（`addBatch()`），等待统一执行（`executeBatch()`），它缓存了多个 Statement 对象，每个 Statement 对象都是`addBatch()`完毕后，等待逐一执行`executeBatch()`批处理的；`BatchExecutor`相当于维护了多个桶，每个桶里都装了很多属于自己的 SQL，就像苹果蓝里装了很多苹果，番茄蓝里装了很多番茄，最后，再统一倒进仓库。（可以是 Statement 或 PrepareStatement 对象）

比如在 SimpleExecutor 中这样实现 update 方法：

    @Override  
    public int doUpdate(MappedStatement ms, Object parameter) throws SQLException {  
        Statement stmt = null;  
        try {  
            Configuration configuration = ms.getConfiguration();  
            StatementHandler handler = configuration.newStatementHandler(this, ms, parameter, RowBounds.DEFAULT, null,  
                    null);  
            stmt = prepareStatement(handler, ms.getStatementLog());  
            return handler.update(stmt);  
        } finally {  
            closeStatement(stmt);  
        }  
    }  

## 7、适配器模式

适配器模式 (Adapter Pattern) ：将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作，其别名为包装器 (Wrapper)。适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguKJEfpvh2s9U0BmmK7ToePibjdeyZiaI3uEc1hEyfFbdfTgIqlEKhotzQ/640?wx_fmt=jpeg)

适配器模式

在 Mybatsi 的 logging 包中，有一个 Log 接口：

/\*\*  
 \* @author Clinton Begin  
 \*/  
public interface Log {

    boolean isDebugEnabled();

    boolean isTraceEnabled();

    void error(String s, Throwable e);

    void error(String s);

    void debug(String s);

    void trace(String s);

    void warn(String s);

}

该接口定义了 Mybatis 直接使用的日志方法，而 Log 接口具体由谁来实现呢？Mybatis 提供了多种日志框架的实现，这些实现都匹配这个 Log 接口所定义的接口方法，最终实现了所有外部日志框架到 Mybatis 日志包的适配：

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguEXhlmXwUKrAH54bOtjsAZWmf6GpdrGJQSlEoY8z2hzjicEt3a7n4dLA/640?wx_fmt=png)

Log

比如对于`Log4jImpl`的实现来说，该实现持有了`org.apache.log4j.Logger`的实例，然后所有的日志方法，均委托该实例来实现。

public class Log4jImpl implements Log {

    private static final String FQCN = Log4jImpl.class.getName();

    private Logger log;

    public Log4jImpl(String clazz) {  
        log = Logger.getLogger(clazz);  
    }

    @Override  
    public boolean isDebugEnabled() {  
        return log.isDebugEnabled();  
    }

    @Override  
    public boolean isTraceEnabled() {  
        return log.isTraceEnabled();  
    }

    @Override  
    public void error(String s, Throwable e) {  
        log.log(FQCN, Level.ERROR, s, e);  
    }

    @Override  
    public void error(String s) {  
        log.log(FQCN, Level.ERROR, s, null);  
    }

    @Override  
    public void debug(String s) {  
        log.log(FQCN, Level.DEBUG, s, null);  
    }

    @Override  
    public void trace(String s) {  
        log.log(FQCN, Level.TRACE, s, null);  
    }

    @Override  
    public void warn(String s) {  
        log.log(FQCN, Level.WARN, s, null);  
    }

}

## 8、装饰者模式

装饰模式 (Decorator Pattern) ：动态地给一个对象增加一些额外的职责 (Responsibility)，就增加对象功能来说，装饰模式比生成子类实现更为灵活。其别名也可以称为包装器 (Wrapper)，与适配器模式的别名相同，但它们适用于不同的场合。根据翻译的不同，装饰模式也有人称之为 “油漆工模式”，它是一种对象结构型模式。

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcggu2qLdFZx5GsNWu8Jx9Libhicic1dKUOKDER8dCAez3dPHCccVnrQvIfKug/640?wx_fmt=jpeg)

装饰者模式

在 mybatis 中，缓存的功能由根接口`Cache（org.apache.ibatis.cache.Cache）`定义。整个体系采用装饰器设计模式，数据存储和缓存的基本功能由`PerpetualCache（org.apache.ibatis.cache.impl.PerpetualCache）`永久缓存实现，然后通过一系列的装饰器来对`PerpetualCache`永久缓存进行缓存策略等方便的控制。如下图：

![](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcggu9yJ8jztjx9wcicgeeRTa4ZLr7qANv5cFNaLK0gKoFdYwzjlvIOn8Llg/640?wx_fmt=jpeg)

Cache

用于装饰 PerpetualCache 的标准装饰器共有 8 个（全部在 org.apache.ibatis.cache.decorators 包中）：

1.  `FifoCache`：先进先出算法，缓存回收策略
2.  `LoggingCache`：输出缓存命中的日志信息
3.  `LruCache`：最近最少使用算法，缓存回收策略
4.  `ScheduledCache`：调度缓存，负责定时清空缓存
5.  `SerializedCache`：缓存序列化和反序列化存储
6.  `SoftCache`：基于软引用实现的缓存管理策略
7.  `SynchronizedCache`：同步的缓存装饰器，用于防止多线程并发访问
8.  `WeakCache`：基于弱引用实现的缓存管理策略

另外，还有一个特殊的装饰器`TransactionalCache`：事务性的缓存

正如大多数持久层框架一样，mybatis 缓存同样分为一级缓存和二级缓存

-   一级缓存，又叫本地缓存，是`PerpetualCache`类型的永久缓存，保存在执行器中（`BaseExecutor`），而执行器又在`SqlSession`（`DefaultSqlSession`）中，所以一级缓存的生命周期与`SqlSession`是相同的。
-   二级缓存，又叫自定义缓存，实现了`Cache`接口的类都可以作为二级缓存，所以可配置如 encache 等的第三方缓存。二级缓存以 namespace 名称空间为其唯一标识，被保存在`Configuration`核心配置对象中。

二级缓存对象的默认类型为 PerpetualCache，如果配置的缓存是默认类型，则 mybatis 会根据配置自动追加一系列装饰器。

Cache 对象之间的引用顺序为：

SynchronizedCache–>LoggingCache–>SerializedCache–>ScheduledCache–>LruCache–>PerpetualCache

## 9、迭代器模式

迭代器（Iterator）模式，又叫做游标（Cursor）模式。GOF 给出的定义为：提供一种方法访问一个容器（container）对象中各个元素，而又不需暴露该对象的内部细节。

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcgguXkR7BoibNU8bOHfcPGjvqvWEoQJLGHeOC6u3v7n9Th9b3f8g9YmrARw/640?wx_fmt=gif)

迭代器模式

Java 的`Iterator`就是迭代器模式的接口，只要实现了该接口，就相当于应用了迭代器模式：

![](https://mmbiz.qpic.cn/mmbiz_png/iaIdQfEric9TyoJd0oWYL7vzbCuxaPcggukLVuZEZia5RFQyz0VOgOANCHpHw1cFNbqNbO8JceibRUhnf3lzW6adMA/640?wx_fmt=png)

Iterator

比如 Mybatis 的`PropertyTokenizer`是 property 包中的重量级类，该类会被 reflection 包中其他的类频繁的引用到。这个类实现了`Iterator`接口，在使用时经常被用到的是`Iterator`接口中的`hasNext`这个函数。

public class PropertyTokenizer implements Iterator&lt;PropertyTokenizer> {  
    private String name;  
    private String indexedName;  
    private String index;  
    private String children;

    public PropertyTokenizer(String fullname) {  
        int delim = fullname.indexOf('.');  
        if (delim> -1) {  
            name = fullname.substring(0, delim);  
            children = fullname.substring(delim + 1);  
        } else {  
            name = fullname;  
            children = null;  
        }  
        indexedName = name;  
        delim = name.indexOf('\[');  
        if (delim> -1) {  
            index = name.substring(delim + 1, name.length() - 1);  
            name = name.substring(0, delim);  
        }  
    }

    public String getName() {  
        return name;  
    }

    public String getIndex() {  
        return index;  
    }

    public String getIndexedName() {  
        return indexedName;  
    }

    public String getChildren() {  
        return children;  
    }

    @Override  
    public boolean hasNext() {  
        return children != null;  
    }

    @Override  
    public PropertyTokenizer next() {  
        return new PropertyTokenizer(children);  
    }

    @Override  
    public void remove() {  
        throw new UnsupportedOperationException(  
                "Remove is not supported, as it has no meaning in the context of properties.");  
    }  
}

可以看到，这个类传入一个字符串到构造函数，然后提供了 iterator 方法对解析后的子串进行遍历，是一个很常用的方法类。

end

老规矩，接了推文都会发红包：恭喜发财，抢红包喽！

想了解**人工智能**和**大数据**[的小伙伴可以看下：阿里年薪 80W + 人工智能、大数据开发技能全套教程（源码 + 视频）都在这儿！](http://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487630&idx=1&sn=c7daddcffb0d3315e3a521d6d2b31b52&chksm=fa24efe6cd5366f0194907ceee7189d07d631cec37b522c2f57c3f36997a558f181d630f0182&scene=21#wechat_redirect)  

![](https://mmbiz.qpic.cn/mmbiz_gif/b96CibCt70iaayBQD2t5s1ht0ib6U8vn5yneyyaicXbF7Msj3ZNiacVunVr0QF257N0eNLHzDF6uhGVPp47s51KDBqQ/640?)

往期精彩回顾

[用好这 42 款 Chrome 插件，每年轻松省出一个年假](http://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487595&idx=1&sn=5607f8b0c2ab9726c0e9c2f8787120f4&chksm=fa24ef03cd5366150084805cec97e8d15ffdf29e1831d73c4dbaec1cfa90902ed6124db04a1a&scene=21#wechat_redirect)

[Java 工作 2 年后需要达到怎么样的技术水平](http://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487600&idx=1&sn=949a977cab072ab0bd9185e9816a5cd2&chksm=fa24ef18cd53660e2c5faf6afb496dc5b155bf607619372d742f2f923af7030e4a20bc505ec2&scene=21#wechat_redirect)

[在《我的世界》里从零打造一台计算机有多难？复旦本科生大神花费了一年心血](http://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487605&idx=1&sn=c8c47d1f490d92eec98a30ecb9e49b3b&chksm=fa24ef1dcd53660b06d09a001db9908ace07754cd067b7664ea3d18d0d228919eaea8f6c70d7&scene=21#wechat_redirect)

![](https://mmbiz.qpic.cn/mmbiz_png/TeYk478W36CbRbqeMxrK0VqWfibsOASqMCYdym56lznTAUU2ztRrOFZs7FgKkGSaCFhKia6oRWYNuQ0vSuKTMlFg/640?wx_fmt=png)

写留言

朕已阅 ![](https://mmbiz.qpic.cn/mmbiz_gif/b96CibCt70iaa8r7PJoyAtlfHAKe8RosE3wYVKBac55p1HPBJHZS42ywnG4yYtD3jo9A9e5kawBZs4IE6R1C4wibw/640?) 
 [https://mp.weixin.qq.com/s?\_\_biz=MzUyNDkzNzczNQ==&mid=2247487637&idx=1&sn=cb06a7223b25218fdf70610cd601e4ae&chksm=fa24effdcd5366eb421909f9ae1cbb4b96dabd61cd8a34fec67a952a7c3d05964d9a48f195ae&mpshare=1&scene=23&srcid=0928nhCMidGM81feU8I4uUWN&sharer_sharetime=1569677349451&sharer_shareid=f8198ef01e44b2fc144478361a10f5e0#rd](https://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487637&idx=1&sn=cb06a7223b25218fdf70610cd601e4ae&chksm=fa24effdcd5366eb421909f9ae1cbb4b96dabd61cd8a34fec67a952a7c3d05964d9a48f195ae&mpshare=1&scene=23&srcid=0928nhCMidGM81feU8I4uUWN&sharer_sharetime=1569677349451&sharer_shareid=f8198ef01e44b2fc144478361a10f5e0#rd)

 [https://mp.weixin.qq.com/s?\_\_biz=MzUyNDkzNzczNQ==&mid=2247487637&idx=1&sn=cb06a7223b25218fdf70610cd601e4ae&chksm=fa24effdcd5366eb421909f9ae1cbb4b96dabd61cd8a34fec67a952a7c3d05964d9a48f195ae&mpshare=1&scene=23&srcid=0928nhCMidGM81feU8I4uUWN&sharer_sharetime=1569677349451&sharer_shareid=f8198ef01e44b2fc144478361a10f5e0#rd](https://mp.weixin.qq.com/s?__biz=MzUyNDkzNzczNQ==&mid=2247487637&idx=1&sn=cb06a7223b25218fdf70610cd601e4ae&chksm=fa24effdcd5366eb421909f9ae1cbb4b96dabd61cd8a34fec67a952a7c3d05964d9a48f195ae&mpshare=1&scene=23&srcid=0928nhCMidGM81feU8I4uUWN&sharer_sharetime=1569677349451&sharer_shareid=f8198ef01e44b2fc144478361a10f5e0#rd)
