---
layout: post
title: Android Debugging Tool Monkey 101
---



### Background



Android 的开发者设计了多种多样的调试工具（如：adb，fastboot，AVD），其中部分以命令行工具的形式收录在 [Android SDK](https://developer.android.com/studio/releases/platform-tools)中。测试人员在测试过程中，可以利用这些工具，辅助对 Android app 的测试。

[adb](https://developer.android.com/studio/command-line/adb)，就是这样一种调试用的命令行工具。adb 采用客户端/服务端架构，通过命令行中的 adb 命令唤起客户端进程，向 Android 设备发送指令；客户端进程会检测并创建（如果无） 一个运行于 PC 后台的服务器进程与设备建立通讯；设备上的 adbd（后台驻留程序），在 Bridge 建立后，则会执行收到的命令。

adb 本身只是一个「桥」，但许多命令都被封装在 adb 内。尤其是可以通过 `adb shell `访问设备上的终端，并以此为入口访问 Monkey 等实用工具。本文会附带谈及如何在 PC 上搭建一个可以运行 adb 的最小环境。

<br>

<!--excerpt-->

Monkey 对应「猴子测试」，一种没有测试用例、没有测试计划、没有测试需求的测试场景，应用于开发后期对 app 进行的压力测试。它的设计初衷是，一只没有人类智慧的猴子不会根据测试用例顺着业务流逐层向下，而是会随便乱点乱戳。当用户输入 `adb shell monkey` 指令后，真机或模拟器上的 monkey 程序就会向指定的包，以一种可被重复的方式发送指定 Seed 数的伪随机操作流。这些操作包括切换程序、点按、手势、调节音量、唤起语音助手（若有实体语音助手键或设置了长按 home 键唤起）、截图等。

「无限猴子定理」指出，「让一只猴子在打字机上随机地按键，当按键时间达到无穷时，几乎必然能够打出任何给定的文字，比如莎士比亚的全套著作」，同样是猴子，Monkey 提供了一种几乎可以覆盖所有可能引起应用 crash 操作的工具。因为 test case 中的异常流设计可能不全面，甚至产品需求中的业务流程也会有遗漏。猴子测试中的异常情况，可以对这两种情况起到补充的作用，发现 QA 流程预期之外的 bug，从而让 app 的运行情况在预期之内。

<br>

<br>

### Configuration 

<br>

 *一台装有 Android platform-tools 的电脑：*

1.下载 Android Studio。在 SDK manager 里下载 platform-tools。

2.或下载 platform-tools。

(Windows) 将压缩包内文件夹 \platform-tools 解压到特定位置，并添加 YOURDIRECTORY\platform-tools 到 PATH 系统变量的值中，重启 Windows。即可在 cmd/Windows Terminal 中直接使用 adb。

(macOS) 将压缩包内文件夹 /platform-tools 解压到特定位置，修改所用 shell 的 $PATH。

<br>

*一台打开了开发者模式的 Android 装置：*

前往**设置 - 关于**，点击 Build 号 7 次，返回上一级页面，显示**开发者模式**为打开成功。

<br>

*启用 adb 调试：*

- Android 11+， 无线 adb

**开发者模式-无线调试**，启用后屏幕上会出现 Wi-Fi 配对密钥、连接的 IP 地址和端口（与旧版本 Android 不同，11+ 版本的端口随机生成）。在终端内运行 `adb pair ipaddr:port`，如 `adb pair 192.168.1.130:37099`，连接后运行 `adb devices`，看见装置在列表中即为成功。

- Android 10 及更低版本，无线 adb

连接电脑和装置，终端中运行 `adb tcpip 5555`，在网络设置中获得装置的 IP 地址。随后在终端中运行 `adb connect YOURIPADDRESS:5555`，并运行 `adb devices` 检查。

- USB 连接装置与电脑（任意 Android 版本）

<br>

<br>

### Usage

<br>

在配置好环境，并连接上设备后，我们就可以使用 Monkey 工具进行测试。

<br>

首先获得包名。

运行 `adb shell pm list package -3 | grep KEYWORD`，可以获得目前 KEYWORD app 对应的包名。获得包名后，即可运行 Monkey。如 `adb shell monkey -p com.your.app --throttle 100-s 43686 50000`，详细的参数见[表](https://developer.android.com/studio/test/monkey?hl=zh-cn)。

<br>

在本人使用过程中，一般会使用如下参数：
`adb shell monkey -p com.my.app --throttle 100 -s 43686 -v 1 50000 --pct-touch 30 --pct-motion 20 --pct-trackball 20 --pct-appswitch 10 --pct-syskeys  10 --pct-anyevent 10`

参考上表，不难发现，此段 monkey 命令的参数指的是：

1. 通过 -p com.my.app 指定对 com.my.app 进行测试
2. 通过 --throttle 100 指定在事件中插入 100ms 的间隔
3. 通过 -s 43686 指定随机事件数生成器种子值为 43686（注意此值应尽量在一段时间内保持一致）
4. 通过 -v 1 指定 VERBOSE 啰嗦模式参数为 1，提供有关测试在运行时的更多详细信息
5. 通过 50000 指定发送 50000 个伪随机事件
6. --pct 选项分别分配了 30% 轻触事件、20% 动作事件、20% 轨迹球事件、10% 应用切换、10% 系统按键事件与 10% 的其他事件。

<br>

需要注意，由于 Monkey 的机制是发送伪随机的控制流，而非完全随机。故当 seed 控制为一致的数值时，Monkey 在每次运行时都将会生成相同的事件序列。这有助于复现缺陷，或是使用应用商店审核时的参数去针对性地修复缺陷。但当一定数值的 seed 稳定通过后，应当更换 seed 再次测试，以提高通过应用商店使用随机种子值测试的概率。--throttle 亦需要根据当前 app 开发优化程度进行调整，如线上环境网络响应速度都要好于测试环境，则测试时 --throttle 需要适当调高数值。

<br>在测试过程中，手机会在 Monkey 的控制下自动进行操作，若想终止，需新建终端窗口，运行` adb shell ps | grep monkey`，取得 monkey 的 pid 进程号，并运行` kill *PIDNUMBER`*。

<br>

可以将 Monkey 的测试日志保存到指定位置。如（Linux & macOS）：`monkey -p com.my.app -s 666 500 > ~/log.txt`。如此 monkey 就会将测试日志保存在用户文件夹下的 log.txt 中。

通过的，日志底部显示 #Monkey finished。未通过的，在日志底部会有 aborted due to an error 等提示。日志中 Crash 和 ANR 错误的格式与原因定位，可参见：https://www.cnblogs.com/Chilam007/p/10941092.html