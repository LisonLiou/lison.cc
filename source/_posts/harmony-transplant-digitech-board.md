---
title: 鸿蒙系统移植rk3288过程记录
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


<!--more-->

我的熟悉路径
1. 能够编译linux4.19内核，生成img文件，并在虚拟机上更新（如何）
2. 能够编译安卓系统，并能够刷在手机上（红米6）
3. 能够编译鸿蒙系统，移植在rk3288芯片上

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
10. 