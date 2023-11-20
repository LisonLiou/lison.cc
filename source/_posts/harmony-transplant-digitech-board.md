---
title: Blind Man的鸿蒙系统移植rk3288过程记录
tags:
  - linux kernel 编译
  - 鸿蒙系统移植
categories:
  - linux操作系统编译移植
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2023-11-16 14:54:26
desc:
keywords:
---

我的熟悉路径(粗粮版)
1. 能够编译linux4.19内核，生成img文件，并在虚拟机上更新（如何）
2. 能够编译安卓系统，并能够刷在手机上（红米6）
3. 能够编译鸿蒙系统，移植在rk3288芯片上

<!--more-->

### todo 
1. 目前首先需要编译linux4.19内核
2. 似乎进行到：F:\rk3288\软件开发文档\RockChip_Uboot开发文档V1.0.pdf 其中的编译步骤
3. 似乎又进行到：F:\rk3288\DLT-RK3288B_V03用户手册.pdf 其中的4.1 编译 Android 5.1 源码
4. 进行到 

```
alias mgcc='make CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu/bin/aarch64-linux-gnu-' ##指定自己的编译器
mgcc ARCH=arm64 rockcchip_linux_defconfig ## config
mgcc ARCH=arm64 rk3399-evb-ind-lpddr4-linux.img -j8 ##这个改成你们自己的路径
```

dtb文件报语法错误，猜测需要重新生成设备树文件？

5. [keystone] - 将上述编译之前的编译调通即可得到.bin和uboot.img文件





### 引用

1. [Linux 内核下载](https://www.kernel.org/)
2. [Linux 内核动手编译实用指南 ](https://linux.cn/article-16252-1.html)
3. [Linux 内核编译与安装（完整过程）](https://blog.csdn.net/shiftrain/article/details/118575854)
4. [OpenHarmony鸿蒙内核Liteos-a最小系统移植教程(IMX6ULL开发板)](https://www.bilibili.com/video/BV1Mf4y1a7PZ/?p=3&spm_id_from=pageDriver&vd_source=8b0631edf0e9985e7c1716dd9cb4ee12)
5. [OpenHarmony教程](https://www.openharmony.cn/download/)
6. [Rockchip快速上手OpenHarmony L2](https://gitee.com/caesar-wang/openharmony-rockchip)
7. [caesar wang / kernel-4.19 Rockchip Kernel4.19的开发，目前主要支持 RK3566/RK3568/RV1126/1109/RK3399/RK3326/rk3288/PX30/RK3399Pro等芯片 ](https://gitee.com/caesar-wang/kernel-4.19)
8. [Linux内核映像vmlinux、Image、zImage、uImage区别](https://zhuanlan.zhihu.com/p/466226177#:~:text=Image%EF%BC%9ALinux%E5%86%85%E6%A0%B8%E7%BC%96%E8%AF%91%E6%97%B6%EF%BC%8C%E4%BD%BF%E7%94%A8objcopy%E5%A4%84%E7%90%86vmlinux%E5%90%8E%E7%94%9F%E6%88%90%E7%9A%84%E4%BA%8C%E8%BF%9B%E5%88%B6%E5%86%85%E6%A0%B8%E6%98%A0%E5%83%8F%E3%80%82%20%E8%AF%A5%E6%98%A0%E5%83%8F%E6%9C%AA%E5%8E%8B%E7%BC%A9%EF%BC%8C%E5%8F%AF%E7%9B%B4%E6%8E%A5%E5%BC%95%E5%AF%BCLinux%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E3%80%82%20RockPI%204A%E5%8D%95%E6%9D%BFLinux%E5%86%85%E6%A0%B8%E7%BC%96%E8%AF%91vmlinux%E5%92%8CImage%E8%BF%87%E7%A8%8B%E5%A6%82%E4%B8%8B%EF%BC%9A%20root%40ubuntu%3A%2Fhome%2Frun%2Fcode%2Frockchip-bsp%23.%2Fbuild%2Fmk-kernel.sh%20rockpi4a%20Building,kernel%20for%20rockpi4a%20board%21%204.4.154%20%23%23%204.4.154%E5%86%85%E6%A0%B8)
9. [什么是uboot？uboot有什么用？](https://blog.csdn.net/sidongzhong/article/details/102598704)
10. [嵌入式Linux移植和Uboot（一）：Bootloader介绍，U-boot介绍（特点，目录结构）](https://zhuanlan.zhihu.com/p/194357198)
11. [Google官方编译安卓系统的准备工作](https://source.android.com/source/building?hl=zh-cn)
12. [Google官方搭建编译环境](https://source.android.com/source/initializing?hl=zh-cn)
13. [ARM-Linux 交叉编译工具链安装](https://blog.csdn.net/Qiuhongim/article/details/124137192#:~:text=%E5%AE%89%E8%A3%85%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E5%B7%A5%E5%85%B7%E9%93%BE%20%281%29%20%E5%B0%86gcc-linaro-12.0.1-2022.02-x86_64_arm-linux-gnueabihf.tar.xz%E7%A7%BB%E5%8A%A8%E5%88%B0,%2Fusr%2Flocal%2Farm%20%E6%96%87%E4%BB%B6%E5%A4%B9%E4%B8%8B%EF%BC%8C%E6%B3%A8%EF%BC%9A%E6%B2%A1%E6%9C%89%E8%AF%A5%E6%96%87%E4%BB%B6%E5%A4%B9%E5%B0%B1%E6%96%B0%E5%BB%BA%E4%B8%80%E4%B8%AA%EF%BC%8C%E8%AF%A5%E6%96%87%E4%BB%B6%E5%A4%B9%E5%9C%A8%E5%90%8E%E9%9D%A2%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E6%97%B6%E4%B9%9F%E4%BC%9A%E7%94%A8%E5%88%B0%E7%9A%84%20%282%29%20%E8%A7%A3%E5%8E%8B%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E5%B7%A5%E5%85%B7%E9%93%BE%E5%8E%8B%E7%BC%A9%E5%8C%85%EF%BC%8C%E6%B3%A8%EF%BC%9A%E8%A7%A3%E5%8E%8B%E5%87%BA%E6%9D%A5%E5%90%8E%E7%9A%84%E6%96%87%E4%BB%B6%E5%A4%B9%E5%AD%98%E6%94%BE%E7%9A%84%E5%B0%B1%E6%98%AF%E4%BA%A4%E5%8F%89%E7%BC%96%E8%AF%91%E5%B7%A5%E5%85%B7%E9%93%BE%E6%96%87%E4%BB%B6)
14. 