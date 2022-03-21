# 将你自己的项目发布到maven中央仓库 - 简书
[![](https://github.com/GXhunter/myNote/blob/main/2022-3-21%2010-39-25/7d4eb6ac-cdb4-4750-b973-da43e750d762.webp?raw=true)
](https://www.jianshu.com/u/bf95c779e609)

12018.04.07 20:45:26 字数 3,524 阅读 12,969

现在 OkHttp 很火, 研究学习了一下, 铺天盖地的教程和通用工具都是 Android 用的, 封装了很多 Android 的组件在里边. 但是 OkHttp 不是一个安卓库啊, 它是 java 库, 所有基于 JVM 的语言都可以用.  
所以自己写了个 OkHttp 通用简单封装并放到了 github, 这是本人第一个开源项目, 有点小兴奋呢. 但是只是开源, 别人想使用也麻烦, 还要下载下来自己编译, 然后就想到了把它提交到 maven 中央仓库. 但是没有提交过啊, 各种搜索折腾了一天, 终于成功了.  
这篇文章大概介绍一下如何将自己的项目发布到 maven 中央仓库及我滚过的坑 (可能大家会觉得可笑, 想笑就笑吧, 反正我也看不见 o(∩\_∩)o )

偷个懒, 下面的内容摘抄自 [发布项目到 maven 中央仓库](http://www.cnblogs.com/songjialin2016/p/8624019.html)

> maven 是 java 世界最流行的构建工具，构建内容囊括了一个 java 项目的整个生命周期。其中最重要的功能就是依赖管理，maven 通过一个类似云的 ftp 站点统一管理所有 java 的 jar 文件，同时给每个文件的每个版本一个统一的唯一坐标。开发人员通过检索配置这些坐标，选择自己需要的构件搭建自己的项目环境。因此，通过一个叫做 pom 的描述文件，我们就可以在任何平台搭建好项目运行所需要的环境。方便了协作和分享代码。

> 而那个类似云的 ftp 站点就是 maven 中央仓库。由于一些不可抗拒因素，我们对于中央仓库的访问速度比较慢。因此在平时工作环境中，我们往往都是使用一些镜像站点，如阿里云和开源中国的仓库镜像。同时由于公司里的商业代码不可发布到公开站点，我们也会在局域网内搭建私服。但是无论是镜像还是私服，都像是缓存，虽然提高了下载速度，但是最终，所有的 jar 都来自中央仓库。因此，如果我们将项目发布到了 maven 中央仓库中，那么在所有基于 maven 或者 gradle 等的项目里都可以依赖下载你的构件。真正做到了一次发布，处处可用。

偷个懒, 下面的内容摘抄自 [发布项目到 maven 中央仓库](http://www.cnblogs.com/songjialin2016/p/8624019.html)

> 首先，作为一个 java 从业者，对于天天都在用的东西，做一个深入的了解，发布一个项目到中央仓库可以自我满足。作为我来讲，我发布了一个自己的项目，开始有了利用下班时间好好写点开源代码学习和装逼的冲动。

> 再者，作为一个经验丰富的 java 开发工程师，我们在日常工作中总会积攒下自己使用得很顺手的一些轮子或者一些通用的基础类。如果我们整理好，并持续维护，发布到了中央仓库之后，就可以避免很多重复性的工作。而且也避免了很多冲突：我现在的公司里，每个项目的 dubbo 调用接口都会自己定义一个 Response 和 Request 类。同一类型的重复定义，跟 cpp 的多根继承一样烦。这样的结果导致了没法统一处理各个系统的调用返回值。既不够优雅，也显得重复。如果我们把这些基础的东西发布到了中央仓库，就只需要引用一下了。当然这种情况一般是发布到私服比较好，我只是举个例子。具体的大家自行体会。

在发布前, 您需要有自己的项目主页并开源的代码, 您可以使用 GitHub 或者码云, 下面的内容以 GitHub 为例

#### 注册 sonatype 的 Jira 帐号

注册地址: [https://issues.sonatype.org/secure/Signup!default.jspa](https://issues.sonatype.org/secure/Signup!default.jspa)  
注册很简单, 重要的是邮箱地址一定要正确, issue 有任何变动都会通过邮件通知.

> 这里要着重说明一下, 也是我滚过的坑: Username 字段一定要使用英文 + 数字, 一定不要使用中文!!! 否则官方无法分配权限给你, 或者会让你另行注册.  
> 因为这个 Username 会成为这个 Jira 和提交 maven 项目的 OSS 的帐号, 虽然用中文登录啥的没问题, 但提交的时候就有问题了, 我折腾了 N 个小时, 最后又求助官方人员才解决.  
> 我当时发了一个 issue, 就叫 issues1 吧, 当时官方人员让我修改 GroupId, 修改后我回复修改完成, 等了好多个小时都没回应. 然后我又提交了一个 issue2, 内容与 issue1 一毛一样, 很快就有官方人员说 OK 了.  
> 没过几分钟 issue1 也有官方人员回复了 (不是一个人), 说我的帐号 XX(中文) 不行, 让我重新注册一个帐号再回复, 他帮我弄.  
> 由于当时 issue2 已经有官方人员回复 OK 了, 我就在 issue1 里回复说我在另一个 issue 里已经解决. 然后我就很欢喜的去提交, 折腾了好久都没提交上, 一直说没有权限... 但是当时我用那个帐号登录 OSS 是可以登录的, 就是提交不了.  
> 折腾了几个小时之后, 很无奈的去 issue2 里求助官方人员, 然后在 issue1 里让我重新注册的兄弟在 issue2 里说是因为我的帐号是中文, 无法分配权限, 让我重新注册一个再在 issue2 里回复, 他帮我配权限...  
> 重新注册了一个帐号之后, 用新的帐号提交, OK 了...

#### 登录 Jira

注册完成后就登录: [https://issues.sonatype.org/login.jsp](https://issues.sonatype.org/login.jsp)

#### 创建一个 Issue

创建地址: [https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134](https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134)

> 这个地址已经选择好了项目和类型, 直接填就行, 当然, 如果你要通过【Create】按钮创建  
> Project 选择【Community Support - Open Source Project Repository Hosting (OSSRH)】  
> Issue Type：选择【New Project】  
> Summary 和 Description ：自己填, 注意要用 E 文  
> Group Id:：这个必须要注意, 如果你用的是 GitHub, 一定要是 com.github. 你的 github 用户名, 例如我的项目地址是: [https://github.com/KeRan213539/CommonOkHttp](https://github.com/KeRan213539/CommonOkHttp), 那我的 groupId 就是 com.github.KeRan213539, 当然也可以使用 GitHub 的 Page 的域名 io.github. 你的 github 用户名. 如果你有自己的域名和项目地址也可以, 官方人员会询问你是否有这个域名的所有权. 在你项目的 pom 里一定要使用这个 groupId, 最好包路径也使用  
> Project URL: 是你的项目地址  
> SCM url: 是你的项目 git 地址  
> Username(s): 可以不用填, 这是能辅助你提交项目的合作人的帐号, 前提是他也得在这个 Jira 注册  
> 其他使用默认值就行了

创建好 Issue 后就等待官方回复吧. 通常如果在晚上 22 点 23 点的话回复会比较快, 运气好几分钟就有回复了  
当看到回复类似 com.github.xxx has been prepared, now user(s) xxx can:Deploy snapshot artifacts into repository ... 说明 OK 了, 可以提交了

#### 使用 GPG 生成密钥对

在等待 Issue 的时候可以先把密钥搞好  
Windows 系统，可以下载 Gpg4win 软件来生成密钥对。下载地址：[https://www.gpg4win.org/download.html](https://www.gpg4win.org/download.html)  
下载的时候有个坑, 会弹出一个捐助页面, 然后没有下载的地方, 也没有自动开始下载. 感觉好像不捐助就不能下载一样. 其实在下载页面下面有个 All Downloads, 里面有个连接 [files.gpg4win.org](https://files.gpg4win.org/), 在这里找到与下载页面上的最新版相同的版本号下载即可  
安装过程中在选择组件的时候, 除了默认必须安装的, 其他取消即可  
安装完成后, 打开 CMD

> 查看是否安装成功  
> gpg --version

* * *

> 生成密钥对  
> gpg --gen-key  
> Real name: 名字 (E 文)  
> Email address: 邮箱  
> You selected this USER-ID:  
> "xxx[xxx@qq.com](mailto:xxx@qq.com)"  
> Change (N)ame, (E)mail, or (O)kay/(Q)uit? o  
> 之后往下，会让你输入用户名和邮箱，还有一个 Passphase（输入两次），相当于密钥库密码，不要忘记。

* * *

> 查看公钥  
> gpg --list-keys  
> pub rsa2048 2017-12-16 \[SC] \[expires: 2019-12-16]  
> 9DF36BF5DFB87B6F04DBCE3D63EC6544BEE6682D  
> uid \[ultimate] xxx[xxx@qq.com](mailto:xxx@qq.com)  
> sub rsa2048 2017-12-16 \[E] \[expires: 2019-12-16]

> 其中 9DF36BF5DFB87B6F04DBCE3D63EC6544BEE6682D 就是公钥的指纹

* * *

> 将公钥发布到 PGP 密钥服务器  
> gpg --keyserver [keyserver.ubuntu.com](keyserver.ubuntu.com) --send-keys 你的公钥指纹

* * *

> 查询公钥是否发布成功  
> gpg --keyserver [keyserver.ubuntu.com](keyserver.ubuntu.com) --recv-keys 你的公钥指纹

#### 修改 Maven 配制

如果你的 Issue 通过了, 那么可以开始提交项目了.

> 修改 maven 全局配制文件 settings.xml, 在 maven 安装目录的 conf 文件夹下

* * *

    <servers>
        <server>
            <id>oss</id>
            <username>用户名</username>
            <password>密码</password>
        </server>
    </servers> 

## 这里的 id 是要在 pom.xml 里面使用的，用户名和密码就是 Jira 的帐号密码

> pom.xml

```xml
<project>
    // 必须与sonatype提交的issue的groupId相同
    <groupId>io.github.gxhunter</groupId>
    <name>dexcoder-assistant</name>
    <description>dexcoder-assistant is a rapid development kit.</description>
    <url>http://www.dexcoder.com/</url>
    <licenses>
        <license>
            <name>The Apache Software License, Version 2.0</naurl
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    <developers>
        <developer>
            <name>selfly</name>
            <email>selfly@foxmail.com</email>
        </developer>
    </developers>
    <scm>
        <connection>scm:git:git@github.com:selfly/dexcoder-assistant.git</connection>
        <developerConnection>scm:git:git@github.com:selfly/dexcoder-assistant.git</developerConnection>
        <url>git@github.com:selfly/dexcoder-assistant.git</url>
    </scm>
    
    <profiles>
        <profile>
            <id>release</id>
            <build>
                <plugins>
                    <!-- Source -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <version>2.2.1</version>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar-no-fork</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- Javadoc -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <version>2.9.1</version>
                        <configuration>
                            <show>private</show>
                            <nohelp>true</nohelp>
                                    <charset>UTF-8</charset>
                <encoding>UTF-8</encoding>
                <docencoding>UTF-8</docencoding>
                <additionalparam>-Xdoclint:none</additionalparam>  <!-- TODO 临时解决不规范的javadoc生成报错,后面要规范化后把这行去掉 -->
            </configuration>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- GPG -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.5</version>
                        <executions>
                            <execution>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
            <distributionManagement>
                <snapshotRepository>
                    <id>oss</id>
                    <url>https://s01.oss.sonatype.org/content/repositories/snapshots/</url>
                </snapshotRepository>
                <repository>
                    <id>oss</id>
                    <url>https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>
        </profile>
    </profiles>
    ...
</project> 
```

> pom.xml 中必须包括：name、description、url、licenses、developers、scm 等基本信息，使用了 Maven 的 profile 功能，只有在 release 的时候才创建源码包、文档包、使用 GPG 进行数字签名。  
> 此外，snapshotRepository 与 repository 中的 id 一定要与 setting.xml 中 server 的 id 保持一致。  
> 如果是多模块项目的话，只需要在父 pom.xml 中声明这些，子 pom.xml 中只需要修改相应的一些信息，如 name 标签。  
> 由于我使用的是 jdk8, 对 doc 的格式要求更严, 不标准的 doc 注释会报错, 这时候可以通过在 doc 插件中配制 additionalparam 参数, 上面代码里已经配过了. 但这只是临时解决方案, 还是尽量使用标准的 doc 注释吧.

#### 提交项目到 OSS

maven 配制修改完成后, 就可以提交项目了, 使用命令:

    mvn clean deploy -P release 

> 当执行以上 Maven 命令时，会自动弹出一个对话框，需要输入上面提到的 Passphase，它就是刚才设置的 GPG 密钥库的密码。  
> 注意：此时上传的构件并未正式发布到中央仓库中，只是部署到 OSS 中了，下面才是真正的发布。

* * *

> 执行上面的命令的过程中, 可能会找不到 gpg.exe, 检查环境变量, 发现环境变量中已经配制了 gpg.exe 所在的路径, 但是还是报错... 如何解决? setting.xml 中加入:

    <profile>
      <id>release</id>
      <properties>
        <gpg.executable>D:/Program Files (x86)/GnuPG/bin/gpg.exe</gpg.executable>
        <gpg.passphrase>上面提到的 Passphase</gpg.passphrase>
      </properties>
    </profile> 

> 注意 ID 要与 pom.xml 里的发布的 profile 的 ID 对应  
> 其中 &lt;gpg.executable> 为你的 gpg.exe 的完整路径  
> &lt;gpg.passphrase> 为上面提到的 Passphase, 这样就不用在每次发布的时候都输入密码了, 但是我发现即便有这个, 有时候 (只是有时, 不是每次) 还是要让输入密码, 暂时不清楚是什么问题.  
> 上面这 2 个配制也可以配制到 pom.xml 的 gpg 插件的<configuration>中, 去掉 &lt;gpg.> 就行, 当然密码最好还是不要放在 pom.xml 里, 因为如果你的代码开源的话, 密码也一起开源了...

* * *

> 如何发布快照版本?  
> 在 pom.xml 中的项目版本号后面加上 -SNAPSHOT 就行  
> 快照版到这里就发布好了，不需要执行下面的关闭、发布构建步骤，构建列表里也找不到快照版的构建，快照版发布后就可以直接引用了

#### 在 OSS 中发布

> 使用 Jira 账号登录 [https://s01.oss.sonatype.org](https://s01.oss.sonatype.org/)，在 Staging Repositories 中查看刚才已上传的构件。  
> 一般发布的构件不多, 可以直接滚动条拉到最后就能看到自己的构件, 或者更改时间排序, 也可进行模糊查询定位到自己的构件  
> 找到自己的构建后选中, 并点击上方的 【Close】按钮关闭构建, 这个过程有点慢, 稍等几分钟  
> 刷新页面, 发现关闭成功后, 再次选中自己的构建, 点击上方的【Release】发布构建.

#### 通知 Sonatype 已经成功发布

> 在 Issue 通过审核的官方回复中, 通常会让你在发布成功后再次回复告知他们, 在刚才那个 Issue 中回复已经成功发布, 官方会回复你大概需要等多久才能在中央仓库中找到你的项目 (10 分钟左右) 以及要多久才能在搜索中搜到(2 个小时左右)

#### 以后的提交过程

> 第一次提交才这么麻烦, 以后提交只用重复 maven 发布, OSS 中 Close,Release 的过程就可以了, 当然需要等待的时间还是 10 分钟和 2 小时.  
> 如果要发布新的项目, 在使用相同 GroupId 的情况下, 与上面的过程一样. 只有使用不同的 GroupId 的时候, 才会需要再去提交 Issue 申请.  
> 当然如果你没有换电脑的话 GPG 的过程只需要一次就行


-   https://www.jianshu.com/p/8c3d7fb09bce)
