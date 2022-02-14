# ss命令 - Linux命令大全教程™
**ss**命令可以用来获取 socket 统计信息，它可以显示和 netstat 类似的内容。但`ss`的优势在于它能够显示更多更详细的有关 TCP 和连接状态的信息，而且比`netstat`更快速更高效。`ss`是 Socket Statistics 的缩写。

当服务器的 socket 连接数量变得非常大时，无论是使用 netstat 命令还是直接 cat `/proc/net/tcp`，执行速度都会很慢。可能你不会有切身的感受，但请相信我，当服务器维持的连接达到上万个的时候，使用`netstat`等于浪费 生命，而用 ss 才是节省时间。  
天下武功唯快不破。`ss`快的秘诀在于，它利用到了 TCP 协议栈中 tcp_diag。tcp_diag 是一个用于分析统计的模块，可以获得 Linux 内核中第一手的信息，这就确保了 ss 的快捷高效。当然，如果你的系统中没有 tcp_diag，`ss`也可以正常运行，只是效率会变得稍慢。(但仍然比 `netstat`要快。)

## 1. 命令格式

    ss [参数]
    ss [参数] [过滤] 

## 2. 命令功能

ss(Socket Statistics 的缩写) 命令可以用来获取 socket 统计信息，此命令输出的结果类似于 netstat 输出的内容，但它能显示更多更详细的 TCP 连接状态的信息，且比 netstat 更快速高效。它使用了 TCP 协议栈中 `tcp_diag`(是一个用于分析统计的模块)，能直接从获得第一手内核信息，这就使得 ss 命令快捷高效。在没有 `tcp_diag`，`ss`也可以正常运行。

## 3. 命令参数

-   `-h`, `--help` 帮助信息
-   \-V, —version 程序版本信息
-   \-n, —numeric 不解析服务名称
-   \-r, —resolve 解析主机名
-   \-a, —all 显示所有套接字 (sockets)
-   \-l, —listening 显示监听状态的套接字 (sockets)
-   \-o, —options 显示计时器信息
-   \-e, —extended 显示详细的套接字 (sockets) 信息
-   \-m, —memory 显示套接字 (socket) 的内存使用情况
-   \-p, —processes 显示使用套接字 (socket) 的进程
-   \-i, —info 显示 TCP 内部信息
-   \-s, —summary 显示套接字 (socket) 使用概况
-   \-4, —ipv4 仅显示 IPv4 的套接字 (sockets)
-   \-6, —ipv6 仅显示 IPv6 的套接字 (sockets)
-   \-0, —packet 显示 PACKET 套接字 (socket)
-   \-t, —tcp 仅显示 TCP 套接字 (sockets)
-   \-u, —udp 仅显示 UCP 套接字 (sockets)
-   \-d, —dccp 仅显示 DCCP 套接字 (sockets)
-   \-w, —raw 仅显示 RAW 套接字 (sockets)
-   \-x, —unix 仅显示 Unix 套接字 (sockets)
-   \-f, —family=FAMILY 显示 FAMILY 类型的套接字 (sockets)，FAMILY 可选，支持 unix, inet, inet6, link, netlink
-   \-A, —query=QUERY, —socket=QUERY

    ```
    QUERY := {all|inet|tcp|udp|raw|unix|packet|netlink}[,QUERY]

    ```
-   \-D, —diag=FILE 将原始 TCP 套接字 (sockets) 信息转储到文件
-   \-F, —filter=FILE 从文件中都去过滤器信息

    ```
     FILTER := [ state TCP-STATE ] [ EXPRESSION ]

    ```

## 4. 使用实例

### 实例 1：显示 TCP 连接

命令：

    ss -t -a 

输出：

    [yiibai@localhost ~]$ ss -t -a
    State      Recv-Q Send-Q     Local Address:Port                      Peer Address:Port
    LISTEN     0      128                    *:ssh                                  *:*
    LISTEN     0      100            127.0.0.1:smtp                                 *:*
    ESTAB      0      52         192.168.0.197:ssh                        192.168.0.5:54835
    LISTEN     0      128                   :::ssh                                 :::*
    LISTEN     0      100                  ::1:smtp                                :::*
    [yiibai@localhost ~]$ 

### 实例 2：显示 Sockets 摘要

命令：

    ss -s 

输出：

    [yiibai@localhost ~]$ ss -s
    Total: 567 (kernel 0)
    TCP:   5 (estab 1, closed 0, orphaned 0, synrecv 0, timewait 0/0), ports 0

    Transport Total     IP        IPv6
    *         0         -         -
    RAW       1         0         1
    UDP       5         3         2
    TCP       5         3         2
    INET      11        6         5
    FRAG      0         0         0

    [yiibai@localhost ~]$ 

**说明：** 列出当前的 established, closed, orphaned and waiting TCP sockets

### 实例 3：列出所有打开的网络连接端口

命令：

    ss -l 

输出：

    [yiibai@localhost ~]$ ss -l
    Netid  State      Recv-Q Send-Q  Local Address:Port                   Peer Address:Port
    nl     UNCONN     0      0                rtnl:-1254096198                        *
    nl     UNCONN     0      0                rtnl:kernel                             *
    nl     UNCONN     0      0                rtnl:-1254096198                        *
    nl     UNCONN     4352   0             tcpdiag:ss/4750                            *
    nl     UNCONN     768    0             tcpdiag:kernel                             *
    nl     UNCONN     0      0                xfrm:kernel                             *
    nl     UNCONN     0      0             selinux:su/4717                            *
    nl     UNCONN     0      0             selinux:kernel                             *
    nl     UNCONN     0      0             selinux:systemd/1                          *
    nl     UNCONN     0      0             selinux:dbus-daemon/648                    *
    nl     UNCONN     0      0             selinux:su/4717                            *
    nl     UNCONN     0      0             selinux:dbus-daemon/648                    *
    nl     UNCONN     0      0             selinux:systemd/1                          *
    nl     UNCONN     0      0               audit:auditd/625                         *
    nl     UNCONN     0      0               audit:systemd/1                          *
    nl     UNCONN     0      0               audit:kernel                             *
    nl     UNCONN     0      0           fiblookup:kernel                             *
    nl     UNCONN     0      0           connector:kernel                             *
    nl     UNCONN     0      0                 nft:kernel                             *
    nl     UNCONN     0      0              uevent:-4117                              *
    nl     UNCONN     0      0              uevent:systemd/1                          *
    nl     UNCONN     0      0              uevent:-4120                              *
    nl     UNCONN     0      0              uevent:-4107                              *
    nl     UNCONN     0      0              uevent:systemd-udevd/498                  *
    nl     UNCONN     0      0              uevent:-4119                              *
    nl     UNCONN     0      0              uevent:systemd-logind/665                 *
    nl     UNCONN     0      0              uevent:kernel                             *
    nl     UNCONN     0      0              uevent:NetworkManager/698                 *
    nl     UNCONN     0      0              uevent:tuned/1002                         *
    nl     UNCONN     0      0              uevent:-4118                              *
    nl     UNCONN     0      0              uevent:tuned/1002                         *
    nl     UNCONN     0      0              uevent:-4120                              *
    nl     UNCONN     0      0              uevent:NetworkManager/698                 *
    nl     UNCONN     0      0              uevent:-4119                              *
    nl     UNCONN     0      0              uevent:-4118                              *
    nl     UNCONN     0      0              uevent:-4117                              *
    nl     UNCONN     0      0              uevent:systemd-logind/665                 *
    nl     UNCONN     0      0              uevent:-4107                              *
    nl     UNCONN     0      0              uevent:systemd/1                          *
    nl     UNCONN     0      0                genl:kernel                             *
    nl     UNCONN     0      0          scsi-trans:kernel                             *
    p_raw  UNCONN     0      0                   *:ens33                              *
    u_str  LISTEN     0      128    /run/lvm/lvmpolld.socket 12805                             * 0                                                                          
    u_seq  LISTEN     0      128    /run/udev/control 12811                             * 0                                                                                 
    u_str  LISTEN     0      128    /run/lvm/lvmetad.socket 12827                             * 0                                                                           
    u_dgr  UNCONN     0      0      /run/systemd/shutdownd 13100                             * 0                                                                            
    u_str  LISTEN     0      100      public/showq 19872                             * 0
    u_str  LISTEN     0      100    private/rewrite 19842                             * 0                                                                                   
    u_str  LISTEN     0      128    /var/run/dbus/system_bus_socket 15231                             * 0                                                                   
    u_str  LISTEN     0      100    private/bounce 19845                             * 0
    u_str  LISTEN     0      100     private/defer 19848                             * 0
    u_str  LISTEN     0      100     private/trace 19851                             * 0
    u_str  LISTEN     0      100    private/verify 19854                             * 0
    u_str  LISTEN     0      100    private/proxymap 19860                             * 0                                                                                  
    u_str  LISTEN     0      100    private/proxywrite 19863                             * 0                                                                                
    u_str  LISTEN     0      100      private/smtp 19866                             * 0
    u_str  LISTEN     0      100     private/relay 19869                             * 0
    u_str  LISTEN     0      100     private/error 19875                             * 0
    u_str  LISTEN     0      100     private/retry 19878                             * 0
    u_str  LISTEN     0      100    private/discard 19881                             * 0                                                                                   
    u_str  LISTEN     0      100     private/local 19884                             * 0
    u_str  LISTEN     0      100    private/virtual 19887                             * 0                                                                                   
    u_str  LISTEN     0      100      private/lmtp 19890                             * 0
    u_str  LISTEN     0      100     private/anvil 19893                             * 0
    u_str  LISTEN     0      100    private/scache 19896                             * 0
    u_str  LISTEN     0      100     public/pickup 19822                             * 0
    u_str  LISTEN     0      100    public/cleanup 19832                             * 0
    u_str  LISTEN     0      100       public/qmgr 19835                             * 0
    u_str  LISTEN     0      100      public/flush 19857                             * 0
    u_str  LISTEN     0      10     /var/run/NetworkManager/private-dhcp 17836                             *                                                                 0
    u_dgr  UNCONN     0      0      /run/systemd/notify 8385                              * 0                                                                               
    u_dgr  UNCONN     0      0      /run/systemd/cgroups-agent 8387                              * 0                                                                        
    u_str  LISTEN     0      100    private/tlsmgr 19839                             * 0
    u_str  LISTEN     0      128    /run/systemd/journal/stdout 8395                              * 0                                                                       
    u_dgr  UNCONN     0      0      /run/systemd/journal/socket 8398                              * 0                                                                       
    u_dgr  UNCONN     0      0            /dev/log 8400                              * 0
    u_str  LISTEN     0      128    /run/systemd/private 12794                             * 0                                                                              
    u_dgr  UNCONN     0      0                   * 16501                             * 8400
    u_dgr  UNCONN     0      0                   * 80277                             * 8400
    u_dgr  UNCONN     0      0                   * 85014                             * 8400
    u_dgr  UNCONN     0      0                   * 16412                             * 8400
    u_dgr  UNCONN     0      0                   * 80112                             * 8400
    u_dgr  UNCONN     0      0                   * 85465                             * 8400
    u_dgr  UNCONN     0      0                   * 15509                             * 8400
    u_dgr  UNCONN     0      0                   * 82676                             * 8400
    u_dgr  UNCONN     0      0                   * 13285                             * 8398
    u_dgr  UNCONN     0      0                   * 19784                             * 8400
    u_dgr  UNCONN     0      0                   * 20011                             * 8400
    u_dgr  UNCONN     0      0                   * 16231                             * 8400
    u_dgr  UNCONN     8      0                   * 13816                             * 13817
    u_dgr  UNCONN     0      0                   * 16204                             * 8400
    u_dgr  UNCONN     0      0                   * 79889                             * 8400
    u_dgr  UNCONN     0      0                   * 16133                             * 8398
    u_dgr  UNCONN     0      768                 * 13817                             * 13816
    u_dgr  UNCONN     0      0                   * 13797                             * 8398
    u_dgr  UNCONN     0      0                   * 15143                             * 8400
    udp    UNCONN     0      0                  :::ipv6-icmp                        :::*
    udp    UNCONN     0      0                   *:trnsprntproxy                     *:*
    udp    UNCONN     0      0           127.0.0.1:323                               *:*
    udp    UNCONN     0      0                   *:bootpc                            *:*
    udp    UNCONN     0      0                  :::rnm                              :::*
    udp    UNCONN     0      0                 ::1:323                              :::*
    tcp    LISTEN     0      128                 *:ssh                               *:*
    tcp    LISTEN     0      100         127.0.0.1:smtp                              *:*
    tcp    LISTEN     0      128                :::ssh                              :::*
    tcp    LISTEN     0      100               ::1:smtp                             :::*
    [yiibai@localhost ~]$ 

### 实例 4：查看进程使用的 socket

命令：

    ss -pl 

输出：

    [yiibai@localhost ~]$ ss -pl
    Netid  State      Recv-Q Send-Q                                  Local Address:Port
    nl     UNCONN     0      0                                                rtnl:-1254096198
    nl     UNCONN     0      0                                                rtnl:kernel
    nl     UNCONN     0      0                                                rtnl:-1254096198
    nl     UNCONN     4352   0                                             tcpdiag:ss/4871
    nl     UNCONN     768    0                                             tcpdiag:kernel
    nl     UNCONN     0      0                                                xfrm:kernel
    nl     UNCONN     0      0                                             selinux:su/4717
    nl     UNCONN     0      0                                             selinux:kernel
    nl     UNCONN     0      0                                             selinux:systemd/1
    nl     UNCONN     0      0                                             selinux:dbus-daemon/648
    nl     UNCONN     0      0                                             selinux:su/4717
    nl     UNCONN     0      0                                             selinux:dbus-daemon/648
    nl     UNCONN     0      0                                             selinux:systemd/1
    nl     UNCONN     0      0                                               audit:auditd/625
    nl     UNCONN     0      0                                               audit:systemd/1
    nl     UNCONN     0      0                                               audit:kernel
    nl     UNCONN     0      0                                           fiblookup:kernel
    nl     UNCONN     0      0                                           connector:kernel
    nl     UNCONN     0      0                                                 nft:kernel
    nl     UNCONN     0      0                                              uevent:-4117
    nl     UNCONN     0      0                                              uevent:systemd/1
    nl     UNCONN     0      0                                              uevent:-4120
    nl     UNCONN     0      0                                              uevent:-4107
    nl     UNCONN     0      0                                              uevent:-4119
    nl     UNCONN     0      0                                              uevent:systemd-logind/665
    nl     UNCONN     0      0                                              uevent:kernel
    nl     UNCONN     0      0                                              uevent:NetworkManager/698
    nl     UNCONN     0      0                                              uevent:tuned/1002
    nl     UNCONN     0      0                                              uevent:-4118
    nl     UNCONN     0      0                                              uevent:tuned/1002
    nl     UNCONN     0      0                                              uevent:-4120
    nl     UNCONN     0      0                                              uevent:NetworkManager/698
    nl     UNCONN     0      0                                              uevent:-4119
    nl     UNCONN     0      0                                              uevent:-4118
    nl     UNCONN     0      0                                              uevent:-4117
    nl     UNCONN     0      0                                              uevent:systemd-logind/665
    nl     UNCONN     0      0                                              uevent:-4107
    nl     UNCONN     0      0                                              uevent:systemd/1
    nl     UNCONN     0      0                                                genl:kernel
    nl     UNCONN     0      0                                          scsi-trans:kernel
    p_raw  UNCONN     0      0                                                   *:ens33
    u_str  LISTEN     0      128                          /run/lvm/lvmpolld.socket 12805
    u_seq  LISTEN     0      128                                 /run/udev/control 12811
    u_str  LISTEN     0      128                           /run/lvm/lvmetad.socket 12827
    u_dgr  UNCONN     0      0                              /run/systemd/shutdownd 13100
    u_str  LISTEN     0      100                                      public/showq 19872
    u_str  LISTEN     0      100                                   private/rewrite 19842
    u_str  LISTEN     0      128                   /var/run/dbus/system_bus_socket 15231
    u_str  LISTEN     0      100                                    private/bounce 19845
    u_str  LISTEN     0      100                                     private/defer 19848
    u_str  LISTEN     0      100                                     private/trace 19851
    u_str  LISTEN     0      100                                    private/verify 19854
    u_str  LISTEN     0      100                                  private/proxymap 19860
    u_str  LISTEN     0      100                                private/proxywrite 19863
    u_str  LISTEN     0      100                                      private/smtp 19866
    u_str  LISTEN     0      100                                     private/relay 19869
    u_str  LISTEN     0      100                                     private/error 19875
    u_str  LISTEN     0      100                                     private/retry 19878
    u_str  LISTEN     0      100                                   private/discard 19881
    u_str  LISTEN     0      100                                     private/local 19884
    u_str  LISTEN     0      100                                   private/virtual 19887
    u_str  LISTEN     0      100                                      private/lmtp 19890
    u_str  LISTEN     0      100                                     private/anvil 19893
    u_str  LISTEN     0      100                                    private/scache 19896
    u_str  LISTEN     0      100                                     public/pickup 19822
    u_str  LISTEN     0      100                                    public/cleanup 19832
    u_str  LISTEN     0      100                                       public/qmgr 19835
    u_str  LISTEN     0      100                                      public/flush 19857
    u_str  LISTEN     0      10               /var/run/NetworkManager/private-dhcp 17836
    u_dgr  UNCONN     0      0                                 /run/systemd/notify 8385
    u_dgr  UNCONN     0      0                          /run/systemd/cgroups-agent 8387
    u_str  LISTEN     0      100                                    private/tlsmgr 19839
    u_str  LISTEN     0      128                       /run/systemd/journal/stdout 8395
    u_dgr  UNCONN     0      0                         /run/systemd/journal/socket 8398
    u_dgr  UNCONN     0      0                                            /dev/log 8400
    u_str  LISTEN     0      128                              /run/systemd/private 12794
    u_dgr  UNCONN     0      0                                                   * 16501
    u_dgr  UNCONN     0      0                                                   * 80277
    u_dgr  UNCONN     0      0                                                   * 85014
    u_dgr  UNCONN     0      0                                                   * 16412
    u_dgr  UNCONN     0      0                                                   * 80112
    u_dgr  UNCONN     0      0                                                   * 85465
    u_dgr  UNCONN     0      0                                                   * 15509
    u_dgr  UNCONN     0      0                                                   * 82676
    u_dgr  UNCONN     0      0                                                   * 13285
    u_dgr  UNCONN     0      0                                                   * 19784
    u_dgr  UNCONN     0      0                                                   * 20011
    u_dgr  UNCONN     0      0                                                   * 16231
    u_dgr  UNCONN     0      0                                                   * 13816
    u_dgr  UNCONN     0      0                                                   * 16204
    u_dgr  UNCONN     0      0                                                   * 79889
    u_dgr  UNCONN     0      0                                                   * 16133
    u_dgr  UNCONN     0      0                                                   * 13817
    u_dgr  UNCONN     0      0                                                   * 13797
    u_dgr  UNCONN     0      0                                                   * 15143
    udp    UNCONN     0      0                                                  :::ipv6-icmp
    udp    UNCONN     0      0                                                   *:trnsprntproxy
    udp    UNCONN     0      0                                           127.0.0.1:323
    udp    UNCONN     0      0                                                   *:bootpc
    udp    UNCONN     0      0                                                  :::rnm
    udp    UNCONN     0      0                                                 ::1:323
    tcp    LISTEN     0      128                                                 *:ssh
    tcp    LISTEN     0      100                                         127.0.0.1:smtp
    tcp    LISTEN     0      128                                                :::ssh
    tcp    LISTEN     0      100                                               ::1:smtp
    [yiibai@localhost ~]$ 

### 实例 5：找出打开套接字 / 端口应用程序

命令：

    ss -lp | grep 23 

输出：

    [yiibai@localhost ~]$ ss -lp | grep 23
    u_str  LISTEN     0      128    /var/run/dbus/system_bus_socket 15231                 * 0               
    u_dgr  UNCONN     0      0       * 16231                 * 8400
    udp    UNCONN     0      0      127.0.0.1:323                   *:*
    udp    UNCONN     0      0         ::1:323                  :::*
    [yiibai@localhost ~]$ 

### 实例 6：显示所有 UDP Sockets

命令：

    ss -u -a 

输出：

    [yiibai@localhost ~]$ ss -u -a
    State      Recv-Q Send-Q     Local Address:Port                      Peer Address:Port
    UNCONN     0      0                      *:trnsprntproxy                        *:*
    UNCONN     0      0              127.0.0.1:323                                  *:*
    UNCONN     0      0                      *:bootpc                               *:*
    UNCONN     0      0                     :::rnm                                 :::*
    UNCONN     0      0                    ::1:323                                 :::*
    [yiibai@localhost ~]$ 

### 实例 7：显示所有状态为 established 的 SMTP 连接

命令：

    ss -o state established '( dport = :ssh or sport = :ssh )' 

输出：

    [yiibai@localhost ~]$ ss -o state established '( dport = :ssh or sport = :ssh )'
    Netid  Recv-Q Send-Q       Local Address:Port                        Peer Address:Port
    tcp    0      52           192.168.0.197:ssh                          192.168.0.5:54835                 timer:(on,241ms,0)
    [yiibai@localhost ~]$ 

### 实例 8：显示所有状态为 Established 的 ssh 连接

命令：

    ss -o state established '( dport = :ssh or sport = :ssh )' 

输出：

    [yiibai@localhost ~]$ ss -o state established '( dport = :ssh or sport = :ssh )'
    Netid  Recv-Q Send-Q       Local Address:Port                        Peer Address:Port
    tcp    0      52           192.168.0.197:ssh                          192.168.0.5:54835                 timer:(on,248ms,0)
    [yiibai@localhost ~]$ 

### 实例 9：列举出处于 FIN-WAIT-1 状态的源端口为 80 或者 443，目标网络为 193.233.7/24 所有 tcp 套接字

命令：  
ss -o state fin-wait-1 ‘(sport = :http or sport = :http)’ dst 193.233.7/24  
输出：自已动手测试一下吧、

### 实例 10：用 TCP 状态过滤 Sockets

命令：

    ss -4 state FILTER-NAME-HERE 
    ss -6 state FILTER-NAME-HERE 

输出：

    [yiibai@localhost ~]$ ss -4 state closing
    Netid  Recv-Q Send-Q       Local Address:Port                        Peer Address:Port
    [yiibai@localhost ~]$ 

**说明：**  
`FILTER-NAME-HERE` 可以代表以下任何一个：

-   `established`
-   `syn-sent`
-   `syn-recv`
-   `fin-wait-1`
-   `fin-wait-2`
-   `time-wait`
-   `closed`
-   `close-wait`
-   `last-ack`
-   `listen`
-   `closing`
-   `all` : 所有以上状态
-   `connected` : 除了 listen and closed 的所有状态
-   `synchronized` : 所有已连接的状态除了 syn-sent
-   `bucket` : 显示状态为 maintained as minisockets, 如：time-wait 和 syn-recv.
-   `big` : 和`bucket`相反.

## 实例 11：匹配远程地址和端口号

命令：

    ss dst 192.168.0.5
    ss dst 192.168.0.113:http
    ss dst 192.168.0.113:smtp 
    ss dst 192.168.0.113:443 

输出：

    [yiibai@localhost ~]$ ss dst 192.168.0.5
    Netid  State      Recv-Q Send-Q  Local Address:Port                   Peer Address:Port
    tcp    ESTAB      0      52      192.168.0.197:ssh                     192.168.0.5:54835
    [yiibai@localhost ~]$ 

### 实例 12：匹配本地地址和端口号

命令：

    ss src 192.168.0.197
    ss src 192.168.0.197:http
    ss src 192.168.0.197:80
    ss src 192.168.0.197:smtp
    ss src 192.168.0.197:25 

输出：

### 实例 13：将本地或者远程端口和一个数比较

命令：

    ss dport OP PORT 
    ss sport OP PORT 

输出：

    [root@localhost ~]# ss  sport = :http 
    [root@localhost ~]# ss  dport = :http 
    [root@localhost ~]# ss  dport \> :1024 
    [root@localhost ~]# ss  sport \> :1024 
    [root@localhost ~]# ss sport \< :32000 
    [root@localhost ~]# ss  sport eq :22 
    [root@localhost ~]# ss  dport != :22 
    [root@localhost ~]# ss  state connected sport = :http 
    [root@localhost ~]# ss \( sport = :http or sport = :http \) 
    [root@localhost ~]# ss -o state fin-wait-1 \( sport = :http or sport = :http \) dst 192.168.0/24 

### 说明：

`ss dport OP PORT` 远程端口和一个数比较；`ss sport OP PORT` 本地端口和一个数比较。  
OP 可以代表以下任意一个:

-   `<=` 或 `le` : 小于或等于端口号
-   `>=` 或 `ge` : 大于或等于端口号
-   `==` 或 `eq` : 等于端口号
-   `!=` 或 `ne` : 不等于端口号
-   `<` 或 `gt` : 小于端口号
-   `>` 或 `lt` : 大于端口号

### 实例 14：ss 和 netstat 效率对比

命令：

    time netstat -at
    time ss 

输出：

    [yiibai@localhost ~]$ time netstat -at
    Active Internet connections (servers and established)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State
    tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
    tcp        0      0 localhost:smtp          0.0.0.0:*               LISTEN
    tcp        0     52 localhost.localdoma:ssh 192.168.0.5:54835       ESTABLISHED
    tcp6       0      0 [::]:http               [::]:*                  LISTEN
    tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
    tcp6       0      0 localhost:smtp          [::]:*                  LISTEN

    real    0m0.119s
    user    0m0.006s
    sys     0m0.021s
    [yiibai@localhost ~]$ time ss
    Netid  State      Recv-Q Send-Q  Local Address:Port                   Peer Address:Port
    u_str  ESTAB      0      0                   * 19870                             * 19871
    u_str  ESTAB      0      0                   * 19850                             * 19849
    u_str  ESTAB      0      0                   * 19871                             * 19870
    u_str  ESTAB      0      0                   * 19830                             * 19829
    u_str  ESTAB      0      0                   * 16422                             * 16423
    u_str  ESTAB      0      0                   * 19852                             * 19853
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 16423                             * 16422
    u_str  ESTAB      0      0                   * 19873                             * 19874
    u_str  ESTAB      0      0                   * 19874                             * 19873
    u_str  ESTAB      0      0                   * 15501                             * 15502
    u_str  ESTAB      0      0                   * 19847                             * 19846
    u_str  ESTAB      0      0                   * 19821                             * 19820
    u_str  ESTAB      0      0                   * 19849                             * 19850
    u_str  ESTAB      0      0                   * 19844                             * 19843
    u_str  ESTAB      0      0                   * 19820                             * 19821
    u_str  ESTAB      0      0                   * 19876                             * 19877
    u_str  ESTAB      0      0                   * 19833                             * 19834
    u_str  ESTAB      0      0                   * 19877                             * 19876
    u_str  ESTAB      0      0                   * 16576                             * 16577
    u_str  ESTAB      0      0                   * 19879                             * 19880
    u_str  ESTAB      0      0                   * 19829                             * 19830
    u_str  ESTAB      0      0                   * 19880                             * 19879
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 15803                             * 15802   
    u_str  ESTAB      0      0                   * 19882                             * 19883
    u_str  ESTAB      0      0                   * 19263                             * 19264
    u_str  ESTAB      0      0                   * 19883                             * 19882
    u_str  ESTAB      0      0                   * 19853                             * 19852
    u_str  ESTAB      0      0                   * 19855                             * 19856
    u_str  ESTAB      0      0                   * 15802                             * 15803
    u_str  ESTAB      0      0                   * 19856                             * 19855
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 15502                             * 15501   
    u_str  ESTAB      0      0                   * 80280                             * 80281
    u_str  ESTAB      0      0                   * 19858                             * 19859
    u_str  ESTAB      0      0                   * 19859                             * 19858
    u_str  ESTAB      0      0                   * 80281                             * 80280
    u_str  ESTAB      0      0                   * 19861                             * 19862
    u_str  ESTAB      0      0                   * 19843                             * 19844
    u_str  ESTAB      0      0                   * 19862                             * 19861
    u_str  ESTAB      0      0                   * 15457                             * 15613
    u_str  ESTAB      0      0                   * 19864                             * 19865
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 15613                             * 15457
    u_str  ESTAB      0      0                   * 19865                             * 19864
    u_str  ESTAB      0      0                   * 19846                             * 19847
    u_str  ESTAB      0      0                   * 19867                             * 19868
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 16577                             * 16576
    u_str  ESTAB      0      0                   * 19868                             * 19867
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 19264                             * 19263
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 16129                             * 16128
    u_str  ESTAB      0      0                   * 13393                             * 13530
    u_str  ESTAB      0      0                   * 19837                             * 19836
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 13530                             * 13393   
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 16379                             * 16378   
    u_str  ESTAB      0      0                   * 16160                             * 16161
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 13796                             * 13777   
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 15732                             * 15731   
    u_str  ESTAB      0      0                   * 15611                             * 15612
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 95329                             * 95327   
    u_str  ESTAB      0      0                   * 13777                             * 13796
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 16106                             * 16105   
    u_str  ESTAB      0      0                   * 19840                             * 19841
    u_str  ESTAB      0      0                   * 16105                             * 16106
    u_str  ESTAB      0      0                   * 19885                             * 19886
    u_str  ESTAB      0      0      /run/systemd/journal/stdout 18433                             * 18432   
    u_str  ESTAB      0      0                   * 19886                             * 19885
    u_str  ESTAB      0      0      /var/run/dbus/system_bus_socket 16161                             * 16160
    u_str  ESTAB      0      0                   * 16378                             * 16379
    u_str  ESTAB      0      0                   * 19888                             * 19889
    u_str  ESTAB      0      0                   * 15612                             * 15611
    u_str  ESTAB      0      0                   * 18432                             * 18433
    u_str  ESTAB      0      0                   * 19889                             * 19888
    u_str  ESTAB      0      0                   * 15731                             * 15732
    u_str  ESTAB      0      0                   * 95327                             * 95329
    u_str  ESTAB      0      0                   * 19834                             * 19833
    u_str  ESTAB      0      0                   * 19891                             * 19892
    u_str  ESTAB      0      0                   * 19841                             * 19840
    u_str  ESTAB      0      0                   * 19836                             * 19837
    u_str  ESTAB      0      0                   * 19892                             * 19891
    u_str  ESTAB      0      0                   * 15152                             * 15153
    u_str  ESTAB      0      0                   * 16128                             * 16129
    u_str  ESTAB      0      0                   * 19894                             * 19895
    u_str  ESTAB      0      0                   * 19895                             * 19894
    u_str  ESTAB      0      0                   * 15153                             * 15152
    u_str  ESTAB      0      0                   * 19897                             * 19898
    u_str  ESTAB      0      0                   * 19898                             * 19897
    udp    ESTAB      0      0       192.168.0.197:33196                85.199.214.100:ntp
    tcp    ESTAB      0      0       192.168.0.197:ssh                     192.168.0.5:54835

    real    0m0.009s
    user    0m0.000s
    sys     0m0.005s
    [yiibai@localhost ~]$ 

 [https://www.yiibai.com/linux/ss.html](https://www.yiibai.com/linux/ss.html)
