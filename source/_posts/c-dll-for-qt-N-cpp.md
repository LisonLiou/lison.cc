---
title: Qt、C++与C#动态调用C语言封装的dll
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
keywords: C++调用C语言动态库dll、Qt调用C语言动态库dll、C#动态调用C语言dll
---

RT
本文记录使用C封装动态库dll，并使用C++、Qt及C#进行dll库的显式调用方式。
C语言封装dll与C++有不同，只做记录。文章包含dll项目源码与C++调用方式及Qt调用方式。
本文项目使用VS2019编译。项目地址：[https://github.com/LisonLiou/dll_master](https://github.com/LisonLiou/dll_master)
<!--more-->


### 1. C语言dll项目master.h与master.c文件

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

### 3. Qt调用方式

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

### 4. C#调用方式
```

        [DllImport("kernel32.dll")]
        private extern static IntPtr LoadLibrary(String path);//path 就是dll路径 返回结果为0表示失败。
        [DllImport("kernel32.dll")]
        private extern static IntPtr GetProcAddress(IntPtr lib, String funcName);//lib是LoadLibrary返回的句柄，funcName 是函数名称 返回结果为0标识失败。
        [DllImport("kernel32.dll")]
        private extern static bool FreeLibrary(IntPtr lib);

        //声明委托
        //[UnmanagedFunctionPointerAttribute(CallingConvention.Cdecl)]
        [UnmanagedFunctionPointerAttribute(CallingConvention.StdCall)]
        delegate int divide(int arg);

        static void Main(string[] args)
        {
            string dllPath = Environment.CurrentDirectory + "\\cdll\\Dll_Master.dll";

            IntPtr hLib = LoadLibrary(dllPath);//加载函数
            IntPtr apiFunction1 = GetProcAddress(hLib, "divide");//获取函数地址

            int i = Marshal.GetLastWin32Error();
            if (apiFunction1.ToInt32() == 0)//0表示函数没找到
                return;

            //获取函数接口，相当于函数指针
            divide div = Marshal.GetDelegateForFunctionPointer(apiFunction1, typeof(divide)) as divide;

            Console.WriteLine(div(22));

            // //释放句柄
            FreeLibrary(hLib);

            Console.Read();
        }

```

### 5. dll项目生成事件之后的宏示例

用于在dll编译成功后将文件拷贝到引用dll的项目的输出路径，具体可根据情况自行编写自定义build步骤bash

```
xcopy $(LocalDebuggerWorkingDirectory)master.h  D:\repos\c\Dll_Master_Test\cdll /e/h/y/f 
&& xcopy $(TargetPath)  D:\repos\c\Dll_Master_Test\cdll /e/h/y/f 
&& xcopy $(TargetPath)  D:\repos\c\Dll_Master_TestQt\cdll /e/h/y/f 
&& xcopy $(TargetPath)  D:\repos\c\Dll_Master_TestCSharp\cdll /e/h/y/f

```


#### 注意事项:

1. dll文件存放位置需与调用方可执行文件在相同路径，具体看代码
2. [System.BadImageFormatException:“试图加载格式不正确的程序的解决办法](https://blog.csdn.net/qq_33286695/article/details/99968924#:~:text=System.BadImageFormatException%3A%20%E8%AF%95%E5%9B%BE%E5%8A%A0%E8%BD%BD%E6%A0%BC%E5%BC%8F%E4%B8%8D%E6%AD%A3%E7%A1%AE%E7%9A%84%E7%A8%8B%E5%BA%8F%E3%80%82%20%28%E5%BC%82%E5%B8%B8%E6%9D%A5%E8%87%AA%20HRESULT%3A0x8007000B%29,%E7%AC%AC%E4%B8%80%E6%AD%A5%EF%BC%9A%E9%A1%B9%E7%9B%AE%E5%8F%B3%E9%94%AE%E5%B1%9E%E6%80%A7-%3E%E9%A1%B9%E7%9B%AE%E8%AE%BE%E8%AE%A1%E5%99%A8-%3E%E7%94%9F%E6%88%90-%3E%E5%B9%B3%E5%8F%B0-%3E%E6%8A%8A%E2%80%99%E9%BB%98%E8%AE%A4%E8%AE%BE%E7%BD%AE%20%28%E4%BB%BB%E4%BD%95%20CPU%29%27%E6%94%B9%E4%B8%BAx86%E3%80%82%20%E5%9B%A0%E4%B8%BA%E2%80%99%E4%BB%BB%E4%BD%95%20CPU%E2%80%99%E7%9A%84%E7%A8%8B%E5%BA%8F%E5%9C%A864%E4%BD%8D%E7%9A%84%E6%9C%BA%E5%99%A8%E4%B8%8A%E5%B0%B1%E4%BC%9A%E7%94%A8%E8%BF%90%E8%A1%8C%E4%B8%BA64%E4%BD%8D%EF%BC%8C%E8%80%8C64%E7%A8%8B%E5%BA%8F%E6%98%AF%E4%B8%8D%E8%83%BD%E5%8A%A0%E8%BD%BD32%E4%BD%8Ddll%E7%9A%84) 因dll是32位程序，C#调用方可能默认为x64，需要将生成的目标平台改为x86，与dll的目标平台保持一致
