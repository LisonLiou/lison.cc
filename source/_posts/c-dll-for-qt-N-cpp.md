---
title: Qt与C++调用C语言封装的dll
tags:
  - 动态库dll
categories:
  - CPP
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2022-06-06 20:12:13
desc:
keywords: C++调用C语言动态库dll、Qt调用C语言动态库dll
---

RT
本文记录使用C封装动态库dll，并使用C++与Qt进行动态库的显式调用方式。
C语言封装dll与C++有不同，只做记录。文章包含dll项目源码与C++调用方式及Qt调用方式。
本文项目使用VS2019编译。
<!--more-->


### 1. dll项目master.h与master.c文件

```
// master.h
#ifndef MASTER_H
#define MASTER_H

#define MASTER_API __declspec(dllexport)

#ifdef __cplusplus
extern "C"
{
#endif // __cplusplus

MASTER_API int divide(int param);


#ifdef __cplusplus
}
#endif // __cplusplus


#endif // !

```

```
// master.c
#include "master.h"

int divide(int param)
{
	return param / 2;
}

```

### 2. C++调用方式

```
#include <iostream>
#include <Windows.h>
#include "cdll/master.h"

typedef int __declspec(dllimport)(*FUNC_divide)(int arg);

int main()
{
	HINSTANCE hDll = LoadLibrary(L"cdll\\Dll_Master.dll");

	if (hDll == NULL) {
		std::cout << "dll load failed " << GetLastError();
	}
	else {
		FUNC_divide eval;
		eval = (FUNC_divide)GetProcAddress(hDll, "divide");
		int ret = eval(13);

		std::cout << "Hello World! " << ret << "\n";

		FreeLibrary(hDll);
	}
}
```

###3. Qt调用方式

```
#include <QtCore/QCoreApplication>
#include <QDebug>
#include <QLibrary>

typedef int (*FUNC_divide)(int arg);

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    QString pathDll = "cdll/Dll_Master.dll";
    QLibrary master(pathDll);
    if (master.load()) {

        qDebug() << "ffffffffff";

        FUNC_divide divide = (FUNC_divide)master.resolve("divide");
        qDebug() << "Hello world "<<divide(19);

        master.unload();
    }
    else {
        qDebug() << "dll load failed";
    }

    return a.exec();
}
```

### 4. dll项目生成事件之后的宏示例，用于在dll编译成功后将文件拷贝到引用dll的项目的输出路径，具体可根据情况自行编写自定义build步骤bash

```
xcopy $(LocalDebuggerWorkingDirectory)master.h  D:\repos\c\Dll_Master_Test\cdll /e/h/y/f && xcopy $(TargetPath)  D:\repos\c\Dll_Master_Test\cdll /e/h/y/f && xcopy $(TargetPath)  D:\repos\c\Dll_Master_TestQt\cdll /e/h/y/f && xcopy $(TargetPath)  D:\repos\c\Dll_Master_TestCSharp\cdll /e/h/y/f

```

