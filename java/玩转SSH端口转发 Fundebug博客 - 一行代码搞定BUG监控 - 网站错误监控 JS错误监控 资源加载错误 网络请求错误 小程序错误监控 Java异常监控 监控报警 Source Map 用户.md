# 玩转SSH端口转发 | Fundebug博客 - 一行代码搞定BUG监控 - 网站错误监控|JS错误监控|资源加载错误|网络请求错误|小程序错误监控|Java异常监控|监控报警|Source Map|用户行为|可视化重现
SSH 有三种端口转发模式，**本地端口转发 (Local Port Forwarding)**，**远程端口转发 (Remote Port Forwarding)**以及**动态端口转发 (Dynamic Port Forwarding)**。对于本地 / 远程端口转发，两者的方向恰好相反。**动态端口转发**则可以用于科学上网。

SSH 端口转发也被称作 SSH 隧道 ([SSH Tunnel](http://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html))，因为它们都是通过 SSH 登陆之后，在**SSH 客户端**与**SSH 服务端**之间建立了一个隧道，从而进行通信。SSH 隧道是非常安全的，因为 SSH 是通过加密传输数据的 (SSH 全称为 Secure Shell)。

在本文所有示例中，本地主机 A1 为 SSH 客户端，远程云主机 B1 为 SSH 服务端。从 A1 主机通过 SSH 登陆 B1 主机，指定不同的端口转发选项 (**-L、-R 和 - D**)，即可在 A1 与 B1 之间建立 SSH 隧道，从而进行不同的端口转发。

[![](https://image.fundebug.com/2019-01-11-ssh-tunnel.jpeg)
](https://image.fundebug.com/2019-01-11-ssh-tunnel.jpeg)

### [](#本地端口转发 "本地端口转发")本地端口转发

#### [](#应用场景 "应用场景:")应用场景:

> 远程云主机 B1 运行了一个服务，端口为 3000，本地主机 A1 需要访问这个服务。

示例为一个简单的 Node.js 服务:

var http = require('http');  

var server = http.createServer(function(request, response)  
{  
 response.writeHead(200,  
 {  
 "Content-Type": "text/plain"  
 });  
 response.end("Hello Fundebug\\n");  
});  

server.listen(3000);  

假设云主机 B1 的 IP 为**103.59.22.17**，则该服务的访问地址为:[http://103.59.22.17:3000](http://103.59.22.17:3000/)

#### [](#为啥需要本地端口转发呢？ "为啥需要本地端口转发呢？")为啥需要本地端口转发呢？

> 一般来讲，云主机的防火墙默认只打开了 22 端口，如果需要访问 3000 端口的话，需要修改防火墙。为了保证安全，防火墙需要配置允许访问的 IP 地址。但是，本地公网 IP 通常是网络提供商动态分配的，是不断变化的。这样的话，防火墙配置需要经常修改，就会很麻烦。

#### [](#什么是本地端口转发？ "什么是本地端口转发？")什么是本地端口转发？

所谓本地端口转发，就是**将发送到本地端口的请求，转发到目标端口**。这样，就可以通过访问本地端口，来访问目标端口的服务。使用**-L**属性，就可以指定需要转发的端口，语法是这样的:

\-L 本地网卡地址: 本地端口: 目标地址: 目标端口   

通过**本地端口转发**，可以将发送到本地主机 A1 端口 2000 的请求，转发到远程云主机 B1 的 3000 端口。

# 在本地主机 A1 登陆远程云主机 B1，并进行本地端口转发

ssh -L localhost:2000:localhost:3000 root@103.59.22.17  

这样，在本地主机 A1 上可以通过访问[http://localhost:2000](http://localhost:2000/)来访问远程云主机 B1 上的 Node.js 服务。

# 在本地主机 A1 访问远程云主机 B1 上的 Node.js 服务

curl [http://localhost:2000](http://localhost:2000)  
Hello Fundebug  

实际上，**-L 选项**中的**本地网卡地址**是可以省略的，这时表示 2000 端口绑定了本地主机 A1 的所有网卡：

ssh -L 2000:localhost:3000 root@103.59.22.17  

若本地主机 A2 能够访问 A1，则 A2 也可以通过 A1 访问远程远程云主机 B1 上的 Node.js 服务。

另外，**-L 选项**中的**目标地址**也可以是其他主机的地址。假设远程云主机 B2 的局域网 IP 地址为 192.168.59.100，则可以这样进行端口转发:

ssh -L 2000:192.168.59.100:3000 root@103.59.22.17  

若将 Node.js 服务运行在远程云主机 B2 上，则发送到 A1 主机 2000 端口的请求，都会被转发到 B2 主机上。

### [](#远程端口转发 "远程端口转发")远程端口转发

#### [](#应用场景-1 "应用场景:")应用场景:

> 本地主机 A1 运行了一个服务，端口为 3000，远程云主机 B1 需要访问这个服务。

将前文的 Node.js 服务运行在本地，在本地就可以通过[http://localhost:3000](http://localhost:3000/)访问该服务。

#### [](#为啥需要远程端口转发呢？ "为啥需要远程端口转发呢？")为啥需要远程端口转发呢？

> 通常，本地主机是没有独立的公网 IP 的，它与同一网络中的主机共享一个 IP。没有公网 IP，云主机是无法访问本地主机上的服务的。

#### [](#什么是远程端口转发？ "什么是远程端口转发？")什么是远程端口转发？

所谓远程端口转发，就是**将发送到远程端口的请求，转发到目标端口**。这样，就可以通过访问远程端口，来访问目标端口的服务。使用**-R**属性，就可以指定需要转发的端口，语法是这样的:

\-R 远程网卡地址: 远程端口: 目标地址: 目标端口   

这时，通过**远程端口转发**，可以将发送到远程云主机 B1 端口 2000 的请求，转发到本地主机 A1 端口 3000。

# 在本地主机 A1 登陆远程云主机 B1，并进行远程端口转发

ssh -R localhost:2000:localhost:3000 root@103.59.22.17  

这样，在远程云主机 A1 可以通过访问[http://localhost:2000](http://localhost:2000/)来访问本地主机的服务。

# 在远程云主机 B1 访问本地主机 A1 上的 Node.js 服务

curl [http://localhost:2000](http://localhost:2000)  
Hello Fundebug  

同理，**远程网卡地址**可以省略，**目标地址**也可以是其他主机地址。假设本地主机 A2 的局域网 IP 地址为 192.168.0.100。

ssh -R 2000:192.168.0.100:3000 root@103.59.22.17  

若将 Node.js 服务运行在本地主机 A2 上，则发送到远程云主机 A1 端口 2000 的请求，都会被转发到 A2 主机上。

### [](#动态端口转发 "动态端口转发")动态端口转发

#### [](#应用场景-2 "应用场景:")应用场景:

> 远程云主机 B1 运行了多个服务，分别使用了不同端口，本地主机 A1 需要访问这些服务。

#### [](#为啥需要动态端口转发呢？ "为啥需要动态端口转发呢？")为啥需要动态端口转发呢？

> 一方面，由于防火墙限制，本地主机 A1 并不能直接访问远程云主机 B1 上的服务，因此需要进行端口转发；另一方面，为每个端口分别创建本地端口转发非常麻烦。

#### [](#什么是动态端口转发？ "什么是动态端口转发？")什么是动态端口转发？

对于**本地端口转发**和**远程端口转发**，都存在两个一一对应的端口，分别位于 SSH 的客户端和服务端，而**动态端口转发**则只是绑定了一个**本地端口**，而**目标地址: 目标端口**则是不固定的。**目标地址: 目标端口**是由发起的请求决定的，比如，请求地址为**192.168.1.100:3000**，则通过 SSH 转发的请求地址也是**192.168.1.100:3000**。

\-D 本地网卡地址: 本地端口  

这时，通过**动态端口转发**，可以将在本地主机 A1 发起的请求，转发到远程主机 B1，而由 B1 去真正地发起请求。

ssh -D localhost:2000 root@103.59.22.17  

而在本地发起的请求，需要由 Socket 代理 ([Socket Proxy](https://en.wikipedia.org/wiki/SOCKS)) 转发到 SSH 绑定的 2000 端口。以 Firefox 浏览器为例，配置 Socket 代理需要找到**首选项**>**高级**>**网络**>**连接**->**设置**:

[![](https://image.fundebug.com/2019-01-11-socket-proxy.png)
](https://image.fundebug.com/2019-01-11-socket-proxy.png)

这样的话，Firefox 浏览器发起的请求都会转发到 2000 端口，然后通过 SSH 转发到真正地请求地址。若 Node.js 服务运行在远程云主机 B1 上，则在 Firefox 中访问[localhost:3000](localhost:3000)即可以访问。如果主机 B1 能够访问外网的话，则可以科学上网……

### [](#链式端口转发 "链式端口转发")链式端口转发

**本地端口转发**与**远程端口转发**结合起来使用，可以进行链式转发。假设 A 主机在公司，B 主机在家，C 主机为远程云主机。A 主机上运行了前文的 Node.js 服务，需要在 B 主机上访问该服务。由于 A 和 B 不在同一个网络，且 A 主机没有独立公共 IP 地址，所以无法直接访问服务。

通过本地端口转发，将发送到 B 主机 3000 端口的请求，转发到远程云主机 C 的 2000 端口。

ssh -L localhost:3000:localhost:2000 root@103.59.22.17  

通过远程端口转发，将发送到远程云主机 C 端口 2000 的请求，转发到 A 主机的 3000 端口。

ssh -R localhost:2000:localhost:3000 root@103.59.22.17  

这样，在主机 B 可以通过访问[http://localhost:3000](http://localhost:3000/)来访问主机 A 上的服务。

# 在主机 B 访问主机 A 上的服务

curl [http://localhost:3000](http://localhost:3000)  
Hello Fundebug  

### [](#参考链接 "参考链接")参考链接

-   [SSH PortForwarding](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding?action=fullsearch&value=linkto%3A%22SSH%2FOpenSSH%2FPortForwarding%22&context=180)
-   [SSH 隧道的原理和实现](http://www.pchou.info/linux/2015/11/01/ssh-tunnel.html)

### 关于 Fundebug

[Fundebug](https://www.fundebug.com/)专注于 JavaScript、微信小程序、微信小游戏、支付宝小程序、React Native、Node.js 和 Java 线上应用实时 BUG 监控。 自从 2016 年双十一正式上线，Fundebug 累计处理了 50 亿 + 错误事件，付费客户有阳光保险、达令家、核桃编程、荔枝 FM、微脉等众多品牌企业。欢迎大家[免费试用](https://www.fundebug.com/team/create)！

[![](https://static.fundebug.cn/wechat_slogan.png)
](https://static.fundebug.cn/wechat_slogan.png) 
 [https://blog.fundebug.com/2017/04/24/ssh-port-forwarding/](https://blog.fundebug.com/2017/04/24/ssh-port-forwarding/)
