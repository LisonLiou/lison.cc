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

1. ![Linux 内核下载](https://www.kernel.org/)
2. ![Linux 内核动手编译实用指南 ](https://linux.cn/article-16252-1.html)
3. ![Linux 内核编译与安装（完整过程）](https://blog.csdn.net/shiftrain/article/details/118575854)
4. ![OpenHarmony鸿蒙内核Liteos-a最小系统移植教程(IMX6ULL开发板)](https://www.bilibili.com/video/BV1Mf4y1a7PZ/?p=3&spm_id_from=pageDriver&vd_source=8b0631edf0e9985e7c1716dd9cb4ee12)
5. ![OpenHarmony教程](https://www.openharmony.cn/download/)
6. ![Rockchip快速上手OpenHarmony L2](https://gitee.com/caesar-wang/openharmony-rockchip)
7. ![caesar wang / kernel-4.19 Rockchip Kernel4.19的开发，目前主要支持 RK3566/RK3568/RV1126/1109/RK3399/RK3326/rk3288/PX30/RK3399Pro等芯片 ](https://gitee.com/caesar-wang/kernel-4.19)
8. 