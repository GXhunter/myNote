# 使用SSH实现内网穿透
## 服务器配置

是的，虽然是内网穿透，但你需要一台有公网 IP 的服务器用来做转发，临时用的话，可以考虑用阿里的抢占式实例，具体请看上面的参考 3

-   首先需要确保服务器的映射端口是可以访问的。确保本机的防火墙里打开了该端口。如果是阿里云，那就要修改 ECS 实例的安全组添加规则。
-   服务端的 sshd 后台服务默认是不允许远程主机连接本地的转发端口的，因此需要修改 sshd 配置文件：  
    编辑`/etc/ssh/sshd_config`，找到`GatewayPorts`配置项，默认是注释起来的，取消注释，并把后面的值改成`clientspecified`
-   重启 sshd：`systemctl restart sshd`

## 客户端配置

-   客户端需要有 SSH。Windows 10 里面已经自带了 OpenSSH，可以直接使用，可以在命令行窗口里运行`ssh`验证一下
-   如果客户端没有，可以用 Cygwin 安装，或者 Windows 版的 Git 等工具里也自带
-   打开命令行窗口执行如下命令：  
    `ssh -fCNR 0.0.0.0:<远程映射端口>:localhost:< 本地监听端口 > root@< 远程主机 >`

    1.  执行此命令后需要手动输入远程服务器上 root 用户的密码
    2.  注意这里的 0.0.0.0 不能省略，否则服务端的映射端口只允许本机访问
    3.  &lt;本地监听端口>就是内网主机的本地服务所监听的端口，比如 80，用这个命令可以把公网服务器的 &lt;远程映射端口> 监听到的请求转发到本地服务上
-   如何断开内网映射：

    1.  简单点直接执行`taskkill /im ssh.exe /f`即可杀掉所有 ssh 进程
    2.  如果怕误杀，可以用`wmic process where "name='ssh.exe'" get commandline,processid`来查询所有的 ssh.exe 进程，根据启动命令行找到对应的进程号，然后用`taskkill /pid <进程 ID> /f`来点杀进程
-   通常杀掉内网机器上的 ssh 进程，也会自动结束服务器上对应的 sshd 进程，如果没有，则需要在服务器上手动清理

    1.  在服务器上执行`netstat -antp | grep <远程映射端口>`即可找到对应的 sshd 进程，然后用`kill -9 <PID>`点杀之

## 保持连接

用 SSH 实现的内网穿透长时间没有数据传输会断掉，需要进行一些配置，让客户端和服务端保持连接。几个方案：

-   安装 autossh，这个软件能够自动重连
-   让 SSH 客户端定时发送消息保持连接

    1.  永久方案：修改 SSH 客户端配置文件。在 Windows 上，这个文件位于`<用户目录>\.ssh\config`，默认是没有这个配置文件的，需要手动创建一个。内容如下：

        Host \*
          ServerAliveInterval 240  

    1.  一次性方案：启动 SSH 客户端时添加参数：`-o ServerAliveInterval=240`
-   让 SSH 服务端定时发送消息保持连接  
    修改 / etc/ssh/sshd_config，添加下面两行：

        ServerAliveInterval 30
        ServerAliveCountMax 60

 [https://segmentfault.com/a/1190000020766229](https://segmentfault.com/a/1190000020766229)
