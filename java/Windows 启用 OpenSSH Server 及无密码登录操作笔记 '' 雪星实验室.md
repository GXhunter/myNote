# Windows 启用 OpenSSH Server 及无密码登录操作笔记 '' | 雪星实验室
## 一、快速配置 OpenSSH Server 的代码

环境简述： 控制端 A：可以是 Windows 或 Linux 受控端 B：Windows 10，准备安装 OpenSSH Server

### 1. 受控端 B 安装 OpenSSH Server

在受控端上 按 Win + X A 打开管理员权限的 Powershell 窗口，执行：

     Get-WindowsCapability -Online | ? Name -like 'OpenSSH*'

    Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0


    Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0







    notepad "C:\ProgramData\ssh\sshd_config" 

### 2. 本机生成密钥并将公钥送与受控端 SSHD 认证

在本机用户文件夹，执行：

然后把公钥扔到受控端上，也就是 控制端: `~/.ssh/id_rsa.pub` 变成 受控端: `~/.ssh/authorized_keys` 即可。这里因为指令集不一样所以 `ssh-copy-id` 无效，就只好手动复制一下了。即执行指令：

    scp .ssh\id_rsa.pub user@host:.ssh\authorized_keys2

至此完成了配置，再尝试进行 ssh 连接就可以无密码连上去了。

### 3. Windows OpenSSH Server 下的 SFTP 配置，以 vscode 工程为例

在本机依然可以用 ~ 作为相对用户目录。

`.vscode/sftp.config`

    {
        "name": "user@host",
        "host": "host",
        "protocol": "sftp",
        "port": 22,
        "username": "user",
        "privateKeyPath": "~/.ssh/id_rsa",
        "remotePath": "C:/Path/To/Your/Project",
        "uploadOnSave": true
    }

r

## [](#%E4%BA%8C%E3%80%81%E4%B8%BA%E4%BB%80%E4%B9%88%EF%BC%9F)二、为什么？

### [](#openssh-server-%E9%BB%98%E8%AE%A4%E8%B7%AF%E5%BE%84%E9%97%AE%E9%A2%98)OpenSSH Server 默认路径问题

Linux 下的 sshd 通常会采用 ~/.ssh/authorized_keys 来认证传入 sshd 的连接。

而 Windows 下似乎 OpenSSH Server 并不运行在普通用户权限下，于是 AuthorizedKeysFile 也就自然而然的没有放到用户的文件夹里。

找到 OpenSSH Server 的配置文件文件，便得知了这一点。

`C:\ProgramData\ssh\sshd_config`

     Match Group administrators
           AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys

也就是在默认情况下 Windows 安装的 OpenSSH Server 的认证文件路径被放在了这里： `C:\ProgramData\ssh\administrators_authorized_keys`。 要解决这个问题，比较简单的方案是直接把后面这 2 行配置去掉然后它就会选择回我们比较习惯的用户目录下的 `.ssh/authorized_keys`。那么问题就全部解决了。像这样：

`C:\ProgramData\ssh\sshd_config`

### [](#%E5%8F%A6%E4%B8%80%E6%9D%A1%E5%87%BA%E8%B7%AF)另一条出路

或者如果你想按它的默认路径放置的话（即 `__PROGRAMDATA__/ssh/administrators_authorized_keys`）， 那么还需要修正一下你放上去的 `administrators_authorized_keys` 的权限。这个方案的权限的问题雪得猜测是来源于 ProgramData 是个系统管理的文件夹，普通进程需要申请权限才能读写，

即修复方法如下：在受控端上 按 Win + X A 打开管理员权限的 Powershell 窗口，执行：

     cd C:\ProgramData\ssh
    icacls administrators_authorized_keys /inheritance:r
    icacls administrators_authorized_keys /grant SYSTEM:`(F`)
    icacls administrators_authorized_keys /grant BUILTIN\Administrators:`(F`)
    net stop sshd
    net start sshd

## [](#%E4%B8%89%E3%80%81%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE)三、参考文献

1.  [Windows Server 2019 および Windows 10 用 OpenSSH のインストール](https://docs.microsoft.com/ja-jp/windows-server/administration/openssh/openssh_install_firstuse)
2.  [ssh - Public Key Authentication Windows Port of OpenSSH - Server Fault](https://serverfault.com/questions/873064/public-key-authentication-windows-port-of-openssh) 
    [https://lab.snomiao.com/SNOLAB-481C](https://lab.snomiao.com/SNOLAB-481C)
