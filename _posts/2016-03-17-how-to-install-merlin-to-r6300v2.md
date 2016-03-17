---
layout: post
title: "How to install Merlin to R6300V2"
category: "DOc"
tags: [router]
---

固件下载，请访问[KoolShare论坛](http://koolshare.cn/forum-72-1.html)

下面是具体步骤:

1. 恢复出厂设置

   - 等待电源灯常绿
   - 按住reset键至少**7秒**
   - 等待电源灯常绿
   
2. 用电脑通过**网线**链接路由器，并**关掉电脑的无线连接**

3. 清除浏览器缓存

4. 输入网址http://192.168.1.1 或者 http://www.routerlogin.net， 输入用户名/密码登录

5. 点击Advanced > Administration > Router Update

6. 刷过渡固件
   - DD-WRT: [Instruction](http://www.dd-wrt.com/wiki/index.php/Netgear_R6300v2)
   
7. 通过ssh链接路由器，输入用户名/密码通过以下指令检测是否可以刷Meilin

   ```bash
   nvram get boardnum
   # boardnum=679
   nvram get boardtype
   # boardtype=0x0646
   nvram get boardrev
   # boardrev=0x1110
   ```
   如果以上指令返回正确值，则进行下一步，否则**刷回官方固件，重刷过渡固件**

8. 按第一步，恢复出厂设置

9. 进入DD/TT Web界面，升级Merlin 1.2固件： `R6300V2_merlin_1.2.trx `

10. 按第一步，恢复出厂设置

11. 进入Meilin Web界面，刷其它版本的Meilin固件

注意：

> 每次刷入新固件，都需要**恢复出厂设置**！

> Merlin不支持直接刷到dd或者tt，要刷回原厂固件的， 需要Merlin刷回原厂的trx固件
