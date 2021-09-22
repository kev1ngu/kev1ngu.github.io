---
layout: post
title: 作为 Mac 游戏容器的 Windows To Go 实践
---



<br>这一篇 blog，我给出以解决我个人需求为目的的一种经济游戏方案。

在某个版本的 macOS Mojave 之后，我的 MacBook Pro 2018 就没法正常启动 CSGO，根据这种[解决方案](https://github.com/ValveSoftware/csgo-osx-linux/issues/2171#issuecomment-539123680)修改之后可以进入游戏。但开始对局后依旧会立刻闪退。我的猜想是从 macOS Mojave 开始， [Apple 开始抛弃 OpenCL/OpenGL](https://apple.slashdot.org/story/18/06/05/1719205/apple-deprecates-opengl-and-opencl-in-macos-1014-mojave)。

<!--excerpt-->

这是很让人悲观的。谁不知道主流游戏大厂并不热衷于支持 Mac 平台。但毕竟很偶尔也能在 Mac 上看到一些大 title，我对一些并不要求高性能的游戏的体验产生了幻想。田忌赛马，在能玩 CSGO on macOS 的年代里，能极快地加载地图一直是一件谈资。

但现在的情况是，我常玩（Steam 200+ 小时）的两款游戏，CSGO 和欧洲卡车模拟 2，这种五百块钱的显卡就能带得动高画质的两款主流游戏。一个无法启动，一个在中画质的夜晚道路上开车灯就能卡到个位数 FPS。我已经不玩 EA、育碧、暴雪或者 Rockstar 的游戏了。已经在两万块的电脑上被迫开中画质了。就这？这真的是可以接受的吗？

我杀苹果一百遍。游戏是不可能不打的，这是我能想到的解决方案：

1. ~~砸掉 MacBook Pro~~

   可以私信获得我的支付宝二维码。

2. ~~去网吧 / 借别人的 PC 玩~~

   放假前我一直是去拿室友的电脑玩。很折腾，很麻烦。

3. ~~组一台 ≈ 三千元的台式机~~

   很烧钱。而且也没有地方放了。

4. ~~eGPU~~

   更烧钱。也占地方。傻子才这样搞。这个方案可笑的地方在于本末倒置。上面几种是「如何找一个正常的环境玩游戏」，eGPU 则试图「把 macOS 变成一个正常的环境」。如果你要玩的游戏在 macOS 上可以流畅运行那显然不用费事了。而对于剩下的那些游戏来说，系统局限性和针对性优化过差，才使得硬件性能完全无法发挥出来。换上 Windows 才是解决的唯一办法。如果 Windows 下还不行那么再上 eGPU 还差不多。

5. Windows To Go (via NVMe SSD)

   给您说说。



[Windows To Go](https://zh.wikipedia.org/wiki/Windows_To_Go)，字面意思，就是一个 On The Go 的 Windows。维基百科说「Mac 不被支持」，这是错的。你完全可以通过一个正常的企业版 Windows 10、[萝卜头的 WTG 辅助工具](https://bbs.luobotou.org/thread-761-1-1.html)或者 [Rufus](rufus.ie) 去制作一个可以在 Mac 上正常使用的 WTG 盘。此外：

1. WTG 支持 UEFI (GPT 格式磁盘) / BIOS (MBR 格式磁盘)。
2. 启动 WTG 时本机磁盘会处于脱机状态（可以绕过此限制）。
3. 对于启用了 UASP 协议的驱动器，断开连接后会直接蓝屏，而不是 60s 的重新连接窗口。
4. 休眠默认禁用（可以绕过此限制）。

看起来很美好，不过你还是需要找一个存放 WTG 的容器。U 盘直接被我抛弃了，一个 CSGO + Windows 10 起码要占用 50G 的容量，而 128 GB 的 U 盘普遍是以 USB Type-A 3.0 速率传输的。主要考虑的还是 USB-C 的移动固态。我选购时，三星 T5 和西部数据的 pSSD 差不多都在 1.2 元/GB 的价格。

但问题来了，pSSD 的写入速度在 500-600 MBps 左右。这是一个很奇怪的数字，我以为移动固态硬盘的速度会在接口上面，也就是 USB 3.1 Gen 2（它的速度上限是 10 Gbps，也就是 1280 MBps）。但三星 T5 写入 540 MBps 的速度只有 3.1 Gen 2 的一半。搜了一下拆解，这采用的是 mSATA 转 USB 3.1 Gen 2 的方案，mSATA 的速度上线是 6 Gbps。如果用 USB 3.1 Gen 1（也就是 USB 3.0）会被转接出的接口速度限制住 (5 Gbps)。所以这些移动固态厂商选择了 mSATA 转 USB 3.1 Gen 2 的方案。而且现在随着存储类价格上涨，pSSD 比我当时看的价格有 100 元左右的涨幅。

所以我的打算是自己买一块 M.2 NVMe 的固态，套上 USB 3.1 Gen 2 的盒子。我选择的是一款 SSK 的金属盒子，￥120。随附  USB-C to USB-C/USB-A 的两根线缆和 3M 的导热贴。持续使用时散热表现尚可，不会像 MBP 本体散热口处那样烫手。<br>

![KEV_1868 copy](https://tva1.sinaimg.cn/large/00831rSTgy1gd1qokzi87j319b0u0kjm.jpg)<br>硬盘方面，即使不看三星 970 Plus Pro 这种，NVMe 的固态也可以跑到 10 Gbps 朝上，不过这样一来接口（USB 3.1 Gen 2, 10 Gbps）又成为了速度的瓶颈。选择太快（=贵）的硬盘就没有必要。我选择的是 WD Black 250GB，￥430。装上盒子后用 BMD 的软件跑了一下读写速度，作为对比的是 MacBook Pro 的 512GB 内建固态。<br>

![Screen Shot 2020-03-12 at 11.45.55 AM](https://tva1.sinaimg.cn/large/00831rSTgy1gd1r0b4rgwj30u00ux1kx.jpg)<br><br>

![Screen Shot 2020-03-12 at 11.43.43 AM](https://tva1.sinaimg.cn/large/00831rSTgy1gd1r0eg6sjj30u00ux1kx.jpg)<br><br>

安装 WTG 的过程已经有很多的参考文档。你完全可以根据少数派的[这篇](https://sspai.com/post/44699)针对 Mac 的 WTG 配置指南来进行操作，但 MacBook Pro 2018 有所特殊，我将根据我的实践简要地复现一下自己的步骤。

1. 准备 Windows 10 的企业版镜像和对应的 Boot Camp 驱动（打开 Boot Camp 助理，顶部菜单 "Action" > "Download Windows Support Software"）。

2. 通过工具刷入 Windows To Go。

3. （这一步仅需要配备 T2 安全芯片的 Mac 操作）重启 Mac，按住 Command+R 进入 Recovery Mode。菜单栏中选取 “实用工具”>“启动安全性实用工具”。把「安全启动」值设为「无安全性」，「外部启动」值设为「允许从外部介质启动」。

4. 重启 Mac，按住 option，选择 EFI boot。插入有线键盘和有线鼠标（如果你熟练掌握 tab 键可以不需要鼠标）。WTG 会自动完成安装并自动重启，第一次重启要按住 option 选择 EFI boot 进入 WTG。之后会默认从上次引导项启动。

   ![image-20200321194022542](https://tva1.sinaimg.cn/large/00831rSTgy1gd1s9ikkeej31ek0u0b2b.jpg)

5. 安装预先准备好的 Boot Camp 驱动。



最崩溃的就是这个 Boot Camp 驱动（6.1 版本）。装到「Apple USB 虚拟主机控制器」会卡住，内建键盘和 Touch Bar 会失灵。[有说法](https://bbs.luobotou.org/thread-44991-1-1.html)是把 drivers/Apple/AppleUSBVHCI 这个文件夹先移走，装完后再单独安装。我大力出奇迹，暴力拔盘，强行重启。其实也可以用有线的外接键盘开出 cmd，"shundown -r -t 5"。

![IMG_4060](https://tva1.sinaimg.cn/large/00831rSTgy1gd1s8kpb4yj316p0u07wi.jpg)

我的 2018 款 MBP 刚入手的时候 macOS 下扬声器有破音（具体我有拍在 vlog 里面），后来被修复了。没有想到在 Windows 下这个 Cirrus Logic 芯片引起的问题还是没有完全解决。Windows Update 推送的 Apple-MEDIA 驱动更新好像也没有什么作用。这个疑似无解，最好插耳机。

看起来没有问题了。于是我开心地打了几天枪，甚至发现可以流畅玩盗版的 Forza Horizon 4。悲惨的事情发生了。在某次启动后，Apple Software Update 检测到了 6.1.10 版本的 Boot Camp 驱动更新，包含了对 Pro Display XDR 的更新 blahblah。

安装到一半死机了。

![image-20200321194121898](https://tva1.sinaimg.cn/large/00831rSTgy1gd1sajtr2kj30w00u01l0.jpg)

强行重启后发现 Touch Bar 不亮了，显卡驱动从红色的 AMD 图标（右键桌面显示 AMD Radeon Settings）变成了 Pro 版本的蓝色 AMD 驱动（右键桌面显示 AMD Radeon Pro and AMD FirePro Advanced Settings）。也不是很影响使用。但我趁半价入正了 Horizon 4，却因为装过盗版没法安装正版。这是很没法接受的事情，所以我用设置里的重置 Windows 功能抹了一遍。可以正常下载了，但 AMD 驱动和 Apple Software Update 不见了，Boot Camp 驱动也装不上。只好重新刷入了崭新的 WTG 环境，并苟在 6.1 版本 BC 驱动。起码可以 1080p 流畅地平线 4 以及中画质稳定 120 fps CSGO。不怕费事的可以试 AMD 给的这个新版 [BC 显卡驱动](https://www.amd.com/zh-hant/support/kb/release-notes/apple-boot-camp)，卵更大的可以试这个[鸡血版民间驱动](https://www.bootcampdrivers.com/)。

现在我可以玩游戏了。





