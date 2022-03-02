# java - 是否可以使用@Advice.OnMethodEnter 从方法返回？ - Cache One
使用 Byte Buddy 的建议 API，是否可以从检测方法返回而不实际执行它？

一种用例是实现缓存并返回缓存值 (如果存在)，而不是再次计算该值。

```
@Advice.OnMethodEnter
public static Object returnCachedValue(@Advice.Argument(0) String query) {
    if (cache.containsKey(query)) {
        // should "abort" method call
        return cache.get(query);
    }
}

```

我知道上面的这个代码示例只是创建了一个局部变量，我可以在 `@Advice.OnMethodExit` 中获得它。方法。但是有没有办法中止显式 `return` 上的方法调用？ ? 如果是，这是否也适用于 `void`方法？

**最佳答案**

不，这是不可能的，返回值只能从退出通知中设置。但是可以通过在值已经存在的情况下跳过原始方法并通过在退出通知中设置此值来模拟它，以防输入通知定义一个值:

```
class MyAdvice {
  @Advice.OnMethodEnter(skipOn = Advice.OnNonDefaultValue.class)
  public static Object returnCachedValue(@Advice.Argument(0) String query) {
    if (cache.containsKey(query)) {
      return cache.get(query);
    } else {
      return null;
    }
  }

  @Advice.OnMethodExit
  public static void processCachedValue(
          @Advice.Return(readOnly = false, typing = DYNAMIC) Object returned,
          @Advice.Enter Object enter) {
    if (enter != null) {
      returned = enter;
    } else {
      cache.put(query, returned);
    }
  }
}

```

当然，如果缓存值是`null`，这不起作用。 . 为避免这种情况，您可以在某些情况下包装该值以确保输入值永远不会`null` . 这样做还可以将上述模式用于 `void`方法。  
这对于编程来说可能看起来不方便，但通知的想法是 Byte Buddy 可以使用通知类作为模板并内联字节码，而无需太多工作以避免运行时开销。 
 [https://cache.one/read/11887949](https://cache.one/read/11887949)
