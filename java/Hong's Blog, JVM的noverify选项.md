# Hong's Blog, JVM的noverify选项
最近在 CI 上运行测试代码的时候遇到一个`java.lang.VerifyError`错误。

    Tests run: 1, Failures: 0, Errors: 1, Skipped: 0, Time elapsed: 0.129 sec <<< FAILURE! - in com.example.CustomerTest
    runTest(com.example.CustomerTest)  Time elapsed: 0.009 sec  <<< ERROR!
    java.lang.VerifyError: (class: au/com/dius/pact/consumer/PactProviderRule, method: findPactVerification, signature: (Lau/com/dius/pact/consumer/PactVerifications;)Ljava/util/Optional;, offset: 12) Illegal type 262144 in constant pool
        at com.example.CustomerTest.<init>(CustomerTest.java:32) 

JVM 加载 class 文件时会做字节码校验（bytecode verification）。这篇[文档](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.10 "bytecode verification")非常详细地讲了 JVM 会做哪些校验。 如果你的 class 文件是由 java 源文件通过 javac 编译出来的，那么基本上不用担心 bytecode verification。 如果 class 文件是由 asm、cglib 等动态生成出来的或者由其它编译器生成的，那么 JVM 在校验它的 bytecode 时就有可能失败。 失败的原因可能是你生成的 bytecode 有 bug，也可能是由于新版本的 JVM 加入了[新的验证条件](http://blog.takipi.com/oracles-latest-java-8-update-broke-your-tools-how-did-it-happen/ "Latest Java 8 Update Broke Your Tools")后导致原来可以通过验证的 bytecode 现在不能通过了。

很多 Java 框架都会动态生成 class 文件，再加上 JVM 版本也会时不时地修改它的 bytecode verification 行为。 所以，运行代码时偶尔会遇到`java.lang.VerifyError`错误。 在不能修改框架代码或者切换 JVM 实现的情况下，JVM 提供了一些选项可以让你改变或者绕过 bytecode verification。

### -XX:-UseSplitVerifier

`-XX:-UseSplitVerifier`可以让 JVM 不开启 “type-checking verifier”。 这样就不强制要求 class 文件含有 StackMapTable（所有 Java 7 version 51 之后的 class 文件默认要求含有 StackMapTable）。

### -noverify

`-noverify`选项可以关闭 bytecode verification。

[有的观点](http://chrononsystems.com/blog/java-7-design-flaw-leads-to-huge-backward-step-for-the-jvm "Java 7 Bytecode Verifier: Huge backward step for the JVM")认为某些 bytecode verification 除了给动态生成 bytecode 增加麻烦之外，并没有什么大用。 但是这篇[文章](https://blogs.oracle.com/buck/entry/never_disable_bytecode_verification_in "Never Disable Bytecode Verification in a Production System")强烈建议不要关闭 bytecode verification，特别是在生产环境里。 因为 bytecode verification 可以检测到恶意代码或者代码中的 bug。 特别是代码中的 bug，因为没有人可以保证（动态产生的）字节码是百分百 bug free 的。

回到我的问题，由于`PactProviderRule`类来自于外部依赖，CI 上的 JVM 也不能替换 （CI 上用的是非 oracle 的 JVM 实现，该测试代码在本地的官方 JVM 上是可以运行通过，所以 CI 上的失败有一定可能是其用的 JVM 实现的原因）， 所以一个简单的方法是在 maven 跑测试代码时，给 JVM 加上`-noverify`选项。

```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
      <argLine>-noverify</argLine>
    </configuration>
  </plugin>
```

更多的参考文档：

-   [Java SE 7 and JDK 7 Compatibility](http://www.oracle.com/technetwork/java/javase/compatibility-417013.html "Java SE 7 and JDK 7 Compatibility") 
    [https://rockhong.github.io/jvm-noverify.html](https://rockhong.github.io/jvm-noverify.html)
