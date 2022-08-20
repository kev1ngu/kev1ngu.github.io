---
layout: post
title: 自建 Assetto Corsa 服务器
---


<br>

### 你需要什么
- 一台 VPS 或者充当服务器角色的 host
- 一个购买过 Asstto Corsa 的 Steam 账号

### 快速搭建步骤
<br>
<br>

####准备 VPS<br>
打开 AC 或者 Content Manager，已经能看到世界各地的公开比赛服务器。这些服务器大多在欧美地区，按照内地的网络环境，加入往往有较大的延时。在模拟赛车中，有时候 100 ms 的延迟已经会导致一次严重的赛道事故（尤其是 Monza T1 这种地方），不少服务器会针对高延迟用户做出踢出、封禁等动作，通常需要使用加速器等魔法才可以愉快地加入国际服务器。
    <!--excerpt-->
那么我自己搭建服务器玩 AC 的目的，一来是可以选用自己心仪的赛道 mod 和车辆 mod，二是国内的车友可能不精通网络技术，用国内机房可以避免上述的高 ping 问题。基于第二条，建议使用周边城市的机房。至于性能，国内的云平台的入门服务器已经可以支撑一场比赛，也就是说，搭建一年 AC 服务器的成本可以压缩到五十元（活动时可以更低）左右。

购买好 VPS 之后，首先需要对实例的防火墙进行设置。进到实例的管理后台，添加*入方向*的 ``UDP（9600）和 TCP（9600 & 8081）``端口许可。
<br>
#### 配置服务端 Assetto Corsa<br>
VPS 端的 OS 我选择的是 Ubuntu。一年几十块的服务器，算力少得可怜。Linux 可以更稳定，节省系统开销。不同的 distribution 操作大同小异。如果涉及到不同的包管理工具，需要对命令稍作改动。

用 SSH 登录（默认不使用 root 用户）进 host 之后，首先需要安装 ``zlib1g:i386`` 和 ``lib32gccl`` 这两个库（嗯……Steam 是 32 位的）。而我们的 OS 现在基本都是 64-bit 了，这就需要 dpkg 添加一下 32-bit 的架构：<br>
``dpkg --add-architecture i386``<br>
``sudo apt-get update``<br>
``sudo apt-get install zlib1g:i386``<br>
``sudo apt-get install lib32gcc1`` <br>


创建文件夹供 Steam 使用，并赋予权限：<br>
``sudo mkdir /home/steam``<br>
``sudo chmod -R 755 /home/steam``<br>

``ls -la /home/steam`` 检查一下是否已经有了正确的权限，我们继续：
``cd /home/steam``<br>

下载 SteamCMD for Linux，解压并执行：<br>
``wget http://media.steampowered.com/client/steamcmd_linux.tar.gz``<br>
``tar -xvf steamcmd_linux.tar.gz ``<br>
``rm steamcmd_linux.tar.gz  ``<br>
``./steamcmd.sh +@sSteamCmdForcePlatformType windows``<br>

这个时候就进入到 SteamCMD：<br>
Steam> login <username> <br>
Steam> passwd<br>
Steam> verification_code<br>
<br>
Steam> force_install_dir ./assetto<br>
Steam> app_update 302550<br>  
Steam> exit<br>

这个时候，Assetto Corsa Dedicated Server（``App ID: 302550``，也就是你 app_update 后面输的参数）便安装好了，路径 ``/home/steam/asstto``。

检查你 PC 的游戏安装路径，里面 [assetto-corsa-installation]/server 和 VPS 上 /home/steam/assetto 的内容是一样的，而服务器的比赛、车辆、天气及各项细节设定，就在 ./asstto/cfg 的 两个 .ini 配置文件里。你可以在本地修改完这两个文件后，把 /cfg 的配置文件和 /content 中你想要的赛道车辆 mod 上传到 VPS（AC 会从服务器拉取 mod，以避免联机的各方存在本地 /content 文件夹中不包含这些 mod 的情况发生）。
<br>
#### 启动服务端 AC Server & CM 添加服务器<br>
全部关闭服务器防火墙还是有一些风险，推荐和之前安全组的设置一样，仅关闭特定端口：
``sudo ufw allow 9600``<br>
``sudo ufw allow 9600/udp``<br>
``sudo ufw allow 8081``<br>

启动服务器端 AC：
``/home/steam/assetto/acServer``<br>

log 不报错一般即为启动成功，这时可以去 Content Manager 里面添加我们的服务器：
打开 CM，随便点击一个服务器，右下角设为 favourite，上方即出现 favorite tab。
进入后下方出现 + 后（这个位置非常难找，我找了足足半个小时，比让 acServer 跑起来的时间还长），输入你的 ``<IP>:8081``，即可搜索到搭建的服务器。

<br>
<br>
Enjoy & Never Stop Driving!

<br>
<br><br>
<br>
*References:*
<br>[ASSETTO CORSA Dedicated Server manual](https://www.assettocorsa.net/forum/index.php?faq/assetto-corsa-dedicated-server-manual.28/)
<br>[配置神力科莎Linux服务器](https://www.cnblogs.com/alsritter/p/13208723.html)