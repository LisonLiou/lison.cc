---
title: Android Debug Bridge adb - 安卓调试桥 官方文档汉化记录(原创)
tags:
  - 未完待续
  - 不列大标题
categories:
  - android
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2015-12-16 11:46:26
desc: Android Debug Bride简称adb，是一个多用途的命令行工具，能够让你与你的安卓模拟器或者安卓设备进行通讯。它是一个包含三个组件的C-S程序：
keywords: 安卓调试桥文档,Android Debug bridge 文档,adb 中文文档
---

**名词释义：**
**模拟器：指使用ADT或者第三方软件创建的安卓模拟器**
**安卓设备（设备）：指运行安卓系统的硬件、含手机、Pad，TV等**

<!--more-->

Android Debug Bride简称adb，是一个多用途的命令行工具，能够让你与你的安卓模拟器或者安卓设备进行通讯。它是一个包含三个组件的C-S程序：

1. 客户端。运行在你的开发机上。你可以通过shell发送一条adb命令用开调用一个客户端。其他的Android工具例如ADT插件和DDMS也可以创建adb客户端。
2. 服务端。运行在你的开发机的后台进程中，服务端管理客户端与运行在模拟器或设备上的adb守护进程的通信工作。
3. 守护进程。是一个运行在每个模拟器或者设备上的后台进程。

可以在<sdk>/plateform-tools目录找到adb工具。

当你启动一个adb客户端的时候，客户端会首先检查是否已经有adb服务端进程在运行。如果没有，就开启服务进程。当服务进程开启之后，它会绑定本地TCP的5037端口并且监听来自adb客户端的命令（所有的adb客户端都使用5037端口与服务端进行通信）。

然后服务端与所有正在运行的模拟器/设备建立连接。服务端通过扫描模拟器/设备的开放端口（5555-5585范围内的奇数端口）来定位设备。当服务端发现adb守护进程时，就与这个端口建立连接。需要注意的是模拟器/设备获取的是一对连续的端口号（偶数端口号用于控制台连接，奇数端口号用于adb连接）示例：

模拟器1  控制台端口号：5554
模拟器1  adb端口号：5555
模拟器2  控制台端口号：5556
模拟器2  adb端口号：5557

如上所述，模拟器已经与adb的5555端口建立了连接，与控制台监听5554端口是一样的。

一旦服务器与所有模拟器建立连接，就可以通过adb命令访问这些设备。基于服务端管理着所有模拟器/设备并且处理多个adb客户端的命令，你可以通过任意一个adb客户端控制任意的模拟器/设备（或者通过脚本）。

**Enabling adb Debugging 开启adb调试
**

------

为使用usb连接的设备使用开启adb，需要在设备的系统设置->开发者选项中开启USB调试。

在Android4.2或者更高版本的系统中，开发者选项默认是隐藏的。显示的话，需要依次点击设置->关于手机，连续点击内部版本号（Build Number）七次。然后返回上一个界面的底部会出现开发者选项。

在有些设备中，开发者选项界面可能所在的位置或者名称会有不同。

注意：当用电脑连接一个运行着Android4.4.2或者更高版本的系统，系统会弹出一个对话框（显示RSA key）询问是否允许通过这台电脑进行调试。这个安全机制确保用户的设备不会被USB调试或者执行其他adb命令，除非用户点击对话框的确认按钮。这需要你的adb版本是1.0.31（在SDK Plateform-tools r16.0.01或者更高版本中）为了调试运行Android4.2.2或更高版本的系统。
更多关于连接一个通过USB连接的设备，请阅读Using Hardware Devices。

**Syntax 语法
**

------

你可以从开发机上通过一个命令行工具发送一条adb命令（或者一个脚本），用法如下：

```
adb [-d|-e|-s <serialNumber>] <command>
```

如果当前只连接了一台模拟器/设备，adb命令就会发送到这台默认的设备。如果有多个模拟器/设备在运行并且已连接，就需要使用-d、-e、或者-s选项来指定目标设备并且执行命令。

**Commands 命令
**

------

下面这张表列出了所有adb支持的命令，以及他们的解释及用途。
表1 可用的adb命令

| 类别                   | 命令/选项                                                    | 描述                                                         | 注释                                                         |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Target Device          | `-d`                                                         | 向唯一连接的USB设备执行一条adb命令                           | 如果连接多台设备将返回错误                                   |
| `-e`                   | 向唯一连接的模拟器执行一条adb命令                            | 如果正在运行多个模拟器将返回错误                             |                                                              |
| `-s `                  | 向指定的模拟器/设备执行一条adb命令，模拟器名称例如：emulator-5556 | See [Directing Commands to a Specific Emulator/Device Instance](#directingcommands). |                                                              |
| General                | `devices`                                                    | 打印所有已经附加的模拟器/设备列表                            | See [Querying for Emulator/Device Instances](#devicestatus) for more information. |
| `help`                 | 打印adb支持的命令列表                                        |                                                              |                                                              |
| `version`              | 打印adb版本号                                                |                                                              |                                                              |
| Debug                  | `logcat [option] [filter-specs]`                             | 将log data输出到屏幕                                         |                                                              |
| `bugreport`            | 将系统信息，状态，logcat数据输出到屏幕                       |                                                              |                                                              |
| `jdwp`                 | 在一个给定的设备上打印可用的JDWP进程列表                     | 可以使用端口转发 jdwp:<pid> port 转发指定的连接到一个特定的JDWP进程，例如： `adb forward tcp:8000 jdwp:472` `jdb -attach localhost:8000` |                                                              |
| Data                   | `install `                                                   | 将一个安卓应用程序（指定apk文件的全路径）发送到模拟器/设备   |                                                              |
| `pull `                | 从模拟器/设备拷贝一个指定的文件到开发机。                    |                                                              |                                                              |
| `push `                | 将一个指定的文件拷贝至模拟器/设备上。                        |                                                              |                                                              |
| Ports and Networking   | `forward `                                                   | 将本地socket连接的指定端口转发至指定的远程模拟器/设备端口    | 指定端口可以使用这些格式：`tcp:``local:``dev:``jdwp:`        |
| `ppp [parm]...`        | 通过USB运行PPP（点对点协议）：`` — the tty for PPP stream. For example `dev:/dev/omap_csmi_ttyl`.`[parm]... ` — zero or more PPP/PPPD options, such as `defaultroute`, `local`, `notty`, etc.注意不要自动启动一个PPP连接 |                                                              |                                                              |
| Scripting              | `get-serialno`                                               | 打印adb示例的序列号串                                        | 查看 [Querying for Emulator/Device Instances](#devicestatus) 获取更多信息 |
| `get-state`            | 打印模拟器/设备的adb状态                                     |                                                              |                                                              |
| `wait-for-device`      | 在设备上线前阻止命令执行。                                   | 可以向adb预置这条命令，这种情况下，adb会等待模拟器/设备连接后才执行adb命令，示例：`adb wait-for-device shell getprop`注意这条命令不会导致adb一只等待整个系统启动完毕。因此，你不应该预置一条需要等待整个系统完全启动的命令。举例来说，安装命令需要Android Package Manager启动，而Android Package Manager需要整个系统完全启动，类似于这样的命令`adb wait-for-device install .apk`会发送安装命令当模拟器或设备连接到adb服务端，但之前Android系统已经完全启动，因此会得到一个错误的结果。 |                                                              |
| Server                 | `start-server`                                               | 检查adb服务端进程是否正在运行，如果没有运行，则运行它。      |                                                              |
| `kill-server`          | 结束adb服务端进程                                            |                                                              |                                                              |
| Shell                  | `shell`                                                      | 在目标模拟器/设备上开启一个远端shell                         | 查看 [Issuing Shell Commands](#shellcommands) 获取更多内容   |
| `shell [shellCommand]` | 在目标模拟器/设备上发送一个shell命令执行后退出。             |                                                              |                                                              |

要不先到这儿。

这不是一个翻译计划，也没有很多小组成员，只有一个惺忪着睡眼的程序猿。旨在将自己用到的阅读到的感觉比较有用的官方文档翻译一下供自己或者后来的盆友看，鉴于水平有限，文中不免有些生涩及直译的成分，不及与错误之处倘若有高手看到还请不吝指正。