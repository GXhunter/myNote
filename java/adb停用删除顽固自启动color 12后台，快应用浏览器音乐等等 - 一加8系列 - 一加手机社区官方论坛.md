# adb停用删除顽固自启动color 12后台，快应用浏览器音乐等等 - 一加8系列 - 一加手机社区官方论坛
\| _本帖最后由 allenencx 于 2022-4-11 11:57 编辑_  

最新情况：**更新 color 12 系统 部分后台自启动 app。**   

**如果有大神知道自带（主题商店）怎么删除的方法告诉我一下。**   

adb 不需要 root，卸载这些 app，不会影响日常升级。  

除此之外，**系统软件慎重删除。**  
有需求的人可以试试。觉得无所谓的。也谢谢观看。  

《一》指令前序：  
自行与 app 的英文名称 com 那一段相结合，  
注意**0** com 与 r com 之间是有英文空格的：  

(例子：钱包 com.finshell.wallet)  
删除钱包 adb shell pm uninstall -k --user 0 com.finshell.wallet  

停用钱包  
adb shell pm disable-user com.finshell.wallet  

启用钱包  
adb shell pm enable-user com.finshell.wallet  

——————————————————————————————————  
删除应用 adb shell pm uninstall -k --user 0  

启用 应用  
adb shell pm enable-user  

停用应用  
adb shell pm disable-user  

查看手机所有已装应用  
adb shell pm list package  
——————————  
《二》步骤：  

1\. 电脑端 安装奇兔刷机（你找到到其他 adb 对话框也行），  
2. 打开手机 usb 调试，链接手机，手机提示，允许调试.  
接着拒绝安装奇兔手机助手。  
3. 手机链接完成之后 ，奇兔界面右下角有个 adb 命令行。点开。  
![](https://static.oneplus.cn/data/attachment/forum/202109/29/093926q1v1zj1sknuu355m.png)

**1.png** _(66.22 KB, 下载次数: 1)_

[下载附件](https://www.oneplusbbs.com/forum.php?mod=attachment&aid=NjI0NzU1MHw0MmQxNTJhM3wxNjUxODE3MzA4fDB8NjAwMzQwNXxkYmUzZjFlYQ%3D%3D&nothumb=yes)

2021-9-29 09:39 上传

![](https://static.oneplus.cn/data/attachment/forum/202109/29/094103en83zeensjj7cnre.png?x-oss-process=image/resize,w_768)

**2.png** _(220.8 KB, 下载次数: 1)_

[下载附件](https://www.oneplusbbs.com/forum.php?mod=attachment&aid=NjI0NzU1NXwxNjBlMjY2YnwxNjUxODE3MzA4fDB8NjAwMzQwNXxkYmUzZjFlYQ%3D%3D&nothumb=yes)

2021-9-29 09:41 上传

4.ctrl+c 复制指令，ctrl+v 黏贴到 adb 指令对话框内，按 enter 回车.  

![](https://static.oneplus.cn/data/attachment/forum/202109/29/094330k6m6056ih6wiir0h.png)

**22.png** _(35.43 KB, 下载次数: 2)_

[下载附件](https://www.oneplusbbs.com/forum.php?mod=attachment&aid=NjI0NzU2MHxkYzY5N2IyZXwxNjUxODE3MzA4fDB8NjAwMzQwNXxkYmUzZjFlYQ%3D%3D&nothumb=yes)

2021-9-29 09:43 上传

![](https://static.oneplus.cn/data/attachment/forum/202109/29/094259o88f4hy24a28hhpf.png)

**4.png** _(40.26 KB, 下载次数: 1)_

[下载附件](https://www.oneplusbbs.com/forum.php?mod=attachment&aid=NjI0NzU1N3xlMzkzNmY5ZXwxNjUxODE3MzA4fDB8NjAwMzQwNXxkYmUzZjFlYQ%3D%3D&nothumb=yes)

2021-9-29 09:42 上传

操作删除指令， 显示 Success 就是成功了。  
操作停用指令，显示 new state: disabled-user 就是成功了。  

《3》完整指令  
————————————  
（以下 app. 停用之后是无法启用，但软件商店会检测到，软件升级之后，会再次被启用）  
停用应用双开  
adb shell pm disable-user com.oplus.multiapp  

停用速览（影响：桌面编辑，无法进入插件界面选择）  
adb shell pm disable-user com.coloros.assistantscreen  

停用智能驾驶（影响：日常驾车使用地图会有点不方便）  
adb shell pm disable-user com.coloros.smartdrive  

停用手机管家（影响：杀毒，垃圾清理，安全支付，安全事件不能使用，**但屏幕会跟手**）  
adb shell pm disable-user com.coloros.phonemanager  

删除手机管家  
adb shell pm uninstall -k --user 0 com.coloros.phonemanager  

停用支付安全（影响：没有支付安全提示）  
adb shell pm disable-user com.coloros.securepay  

**—————————删除应用———————**  
删除快应用（影响：速览负一屏快递信息以及一些卡片功能不能用，日历桌面图标不能更新显示，不能手机内查看云备份，不能使用丫鸭）  
adb shell pm uninstall -k --user 0 com.nearme.instant.platform  

删除自带浏览器（影响：暂无）  
adb shell pm uninstall -k --user 0 com.heytap.browser  

删除自带钱包（影响：无法用钱包里的公交卡）  
adb shell pm uninstall -k --user 0 com.finshell.wallet  

删除 sim 卡程序（影响：暂无）  
adb shell pm uninstall -k --user 0 com.android.stk  

删除 oppo 后台广告（影响：暂无）  
adb shell pm uninstall -k --user 0 com.opos.ads  

删除一加自带百度输入法（不建议删除 自带的输入法响应最稳定最快）  
adb shell pm uninstall -k --user 0 com.baidu.input_yijia  

删除主题商店（无法删除无法停用）  
adb shell pm uninstall -k --user 0 com.heytap.themestore  
停用 adb shell pm disable-user com.heytap.themestore  

删语音助手（非小布助手，小布助手包含一系列 app, 语音助手是其中一个）（影响：暂时无）  
adb shell pm uninstall -k --user 0 com.heytap.speechassist  

删除手机搬家备份（影响：本地备份无法使用，云备份缺少部分选项）  
adb shell pm uninstall -k --user 0 com.oneplus.backuprestore  

删除 color 视频（一加部分机型是没有的）（影响：暂无）  
adb shell pm uninstall -k --user 0 com.heytap.yoli:push  

乐滑锁屏（影响：暂无）  
adb shell pm uninstall -k --user 0 com.heytap.pictorial  

用户体验计划（影响：暂无）  
adb shell pm uninstall -k --user 0 com.oneplus.statistics.rom  
停用 adb shell pm disable-user com.oplus.statistics.rom  

删除 color 音乐（影响：暂无）  
adb shell pm uninstall -k --user 0 com.heytap.music  

删除设备快连（自家手环耳机等快连）  
adb shell pm uninstall -k --user 0 com.heytap.accessory  

删除儿童空间（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.childrenspace  

删除耳机返听（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.karaoke  

删除远程守护服务（影响：color 系手机远程协助不能用）  
adb shell pm uninstall -k --user 0 com.coloros.remoteguardservice  
停用 adb shell pm disable-user com.coloros.remoteguardservice  

删除跨屏互联（影响：暂无）  
adb shell pm uninstall -k --user 0 com.oplus.synergy  

删除小布扫一扫（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.ocrscanner  

删除小布识屏（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.karaokedirectui  

删除智慧能力服务（影响：暂无）  
adb shell pm uninstall -k --user 0 com.oplus.deepthinker  
**删除百变引擎**（影响：暂无）  
(主题商店安装的主题，会要求再下载一个主题插件 (后台叫多彩引擎)，安装多彩引擎会直接安装百变引擎，如果喜欢主题商店更换主题字体壁纸的，建议不用删除这个，百变引擎也是游戏助手（后台叫应用增强服务）连带唤醒的其中一个，涉及新版助手的主题图案更换，这几个都可以连带唤醒主题商店)  
adb shell pm uninstall -k --user 0 com.oplus.uiengine  

删除安全事件（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.securityguard  
删除全局搜索（影响：桌面下滑出现搜索的操作，失效）  
adb shell pm uninstall -k --user 0 com.heytap.quicksearchbox  

删除智能驾驶（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.smartdrive  

删除屏幕共享（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.sharescreen  

删除应用时间使用（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.digitalwellbeing  

删除小布识屏（影响：暂无）  
adb shell pm uninstall -k --user 0 com.coloros.directui  

谷歌系列：（不知道安卓 12 有什么更改的，到时候再更新）  

删除 Google One Time Init  
adb shell pm uninstall -k --user 0 com.google.android.onetimeinitializer  

删除 Google Play Services for AR  
adb shell pm uninstall -k --user 0 com.google.ar.core  

删除 Google Play 服务  
adb shell pm uninstall -k --user 0 com.google.android.gms  

删除 Google Play 服务更新程序  
adb shell pm uninstall -k --user 0 com.android.vending  

删除 Google 备份传输  
adb shell pm uninstall -k --user 0 com.google.android.backuptransport  

删除 Google 服务框架（建议保留，删了可能在旧系统升级新系统的手机中，启动多个后台导致占用更多）  
adb shell pm uninstall -k --user 0 com.google.android.gsf  

删除 Google 合作伙伴设置  
adb shell pm uninstall -k --user 0 com.google.android.partnersetup  

删除 Google 通讯录同步  
adb shell pm uninstall -k --user 0 com.google.android.syncadapters.contacts  

 \| 
 [https://www.oneplusbbs.com/thread-6003405-1.html](https://www.oneplusbbs.com/thread-6003405-1.html)
