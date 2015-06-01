---
layout: post
title: "0编程基础设置路由器自动翻墙纪录-autoddvpn"
guid: urn:uuid:3b13a1f0-0826-11e5-b939-0800200c9a66
categories: geek
tagline: "电子产品这件事，就和小时候拆闹钟是一样的，重要的是过程，只求最后组装的时候不多出零件来。"
tags: [路由器, 翻墙, 编程]
---

2010年的时候，我买了一个 LINKSYS WRT54G 路由器，具体版本不记得了。这是我第一次接触智能路由器，后来懂得了自动翻墙这个玩法，于是糊里糊涂的就把 autoddvpn 设置好了。当时好像花了一下午加晚上的时间，毕竟手残脑笨。

虽然路由器翻墙的效率比较低，比电脑上要慢不少，但是手上这个 Netgear R6300V2 的性能比之前好了许多，而且路由器翻墙不求多高的效率，贵在方便，于是趁周末，就把这件事再折腾一下。

网上 [autoddvpn的教程](https://code.google.com/p/autoddvpn/wiki/jffs) 非常详细，但是大学时候我的C语言考试是蒙过去的，刚及格。复制、粘贴、删除文件、新建文件夹的命令一个也知道。教程里一个简单的命令，每次都要上网查半天，为了以后着想，我把这次的过程尽可能详细地记录下来，供以后照抄。也希望能帮助到其他像我一样不会代码却想设置路由器翻墙的人。


1. 首先，telnet进入路由器，并输入账号密码：（账号为root，密码即web登陆的密码）

```
DD-WRT v24-sp2 kongac (c) 2015 NewMedia-NET GmbH
Release: 05/22/15 (SVN revision: 26970M)
Liudaodao_HOME login: root
Password:
```
```
==========================================================

 ____  ___    __        ______ _____         ____  _  _
 | _ \| _ \   \ \      / /  _ \_   _| __   _|___ \| || |
 || | || ||____\ \ /\ / /| |_) || |   \ \ / / __) | || |_
 ||_| ||_||_____\ V  V / |  _ < | |    \ V / / __/|__   _|
 |___/|___/      \_/\_/  |_| \_\|_|     \_/ |_____|  |_|

                       DD-WRT v24-sp2
                   http://www.dd-wrt.com

==========================================================


BusyBox v1.23.2 (2015-05-22 16:00:37 CEST) built-in shell (ash)
```


2. 连续输入cd ..（注意有空格）返回上级目录，直到根目录。

```
root@Liudaodao_HOME:~# cd ..
root@Liudaodao_HOME:/tmp# cd ..
root@Liudaodao_HOME:/# cd ..
```
3. 然后创建并进入 /jffs/pptp 目录

```
root@Liudaodao_HOME:/# mkdir /jffs/pptp
root@Liudaodao_HOME:/# cd /jffs/pptp
root@Liudaodao_HOME:/jffs/pptp
```
4. 下载所需文件，一共三个 run.sh vpndown.sh vpnup.sh

```
root@Liudaodao_HOME:/jffs/pptp# wget http://autoddvpn.googlecode.com/svn/trunk/pptp/jffs/run.sh
Connecting to autoddvpn.googlecode.com (74.125.203.82:80)
root@Liudaodao_HOME:/jffs/pptp# for i in vpnup vpndown; do wget http://autoddvpn.googlecode.com/svn/trunk/$i.sh;done;
```
5. 赋予文件可执行权限

```
root@Liudaodao_HOME:/jffs/pptp# chmod a+x *.sh
```
6. 输入 ls －l ，查看文件是否下载到指定位置，权限是否正确（是否可执行）

```
root@Liudaodao_HOME:/jffs/pptp# ls -l
-rwxr-xr-x    1 root     root          1263 Jun 17  2014 run.sh
-rwxr-xr-x    1 root     root        106050 Jun 17  2014 vpndown.sh
-rwxr-xr-x    1 root     root        144003 Jun 17  2014 vpnup.sh
```
7. 最后输入命令，设置 rc_startup （我不懂什么意思，抄原文的）

>$ nvram set rc_startup='/jffs/pptp/run.sh'

>$ nvram commit

>$ reboot

```
root@Liudaodao_HOME:/jffs/pptp# nvram set rc_startup='/jffs/pptp/run.sh'
root@Liudaodao_HOME:/jffs/pptp# nvram commit
root@Liudaodao_HOME:/jffs/pptp# reboot
root@Liudaodao_HOME:/jffs/pptp# Connection closed by foreign host.
ADRESTY:~ adresty$
```
但是重启路由之后我发现有些网站上不去。从上面第6项可以看到， vpndown.sh 和 vpnup.sh 的创建日期是 2014.06.17，已经有点过时了，于是我从 [chnroutes项目](https://code.google.com/p/chnroutes/,"chnroutes") 下载了每24小时就会更新一次的最新路由表。下载 android 适用的，其他的和路由器用的格式不一样。

只好重新进入路由器，

```
root@ING_HOME:~# cd ..
root@ING_HOME:/tmp# cd ..
root@ING_HOME:/# cd /jffs/pptp
```
删除过时的文件，

```
root@ING_HOME:/jffs/pptp# rm -r vpndown.sh vpnup.sh
```
由于不知道怎么把电脑上的文件弄进路由器，我只能先把最新的 .sh 文件上传到Github，然后再用教程里面的方法下载到路由器上，

```
root@ING_HOME:/jffs/pptp# wget https://raw.githubusercontent.com/adresty/adresty.github.com/master/media/vpndown.sh
Connecting to raw.githubusercontent.com (103.245.222.133:443)
vpndown.sh           100% |*******************************|   263k  0:00:00 ETA
root@ING_HOME:/jffs/pptp# wget https://raw.githubusercontent.com/adresty/adresty.github.com/master/media/vpnup.sh
Connecting to raw.githubusercontent.com (103.245.222.133:443)
vpnup.sh             100% |*******************************|   318k  0:00:00 ETA
```
最后赋予可执行权限，并检查，

```
root@ING_HOME:/jffs/pptp# chmod a+x *.sh
root@ING_HOME:/jffs/pptp# ls -l
-rwxr-xr-x    1 root     root          1263 Jun 17  2014 run.sh
-rwxr-xr-x    1 root     root        270242 May 31 21:50 vpndown.sh
-rwxr-xr-x    1 root     root        325681 May 31 21:50 vpnup.sh
root@ING_HOME:/jffs/pptp#
```

打完收工。
