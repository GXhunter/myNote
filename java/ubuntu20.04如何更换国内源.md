# ubuntu20.04如何更换国内源
ubuntu20.04 更换国内源的方法。

1\. 打开终端。

2\. 输入以下命令备份原来的源。

```


sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak


```

3\. 再输入以下命令打开 sources.list 配置文件更换源。

```


sudo vim/etc/apt/sources.list

//配置内容如下

deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse 

deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse focal


```

4\. 再输入命令更新源。

```


sudo apt-get update


```

5\. 最后输入命令更新一下软件即可。

```


sudo apt-get upgrade


```

 [https://www.yisu.com/ask/4042.html](https://www.yisu.com/ask/4042.html)
