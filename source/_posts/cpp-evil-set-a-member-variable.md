---
title: 邪恶C++之设置成员变量的坑
tags:
  - null
  - null
categories:
  - null
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2025-09-08 10:43:54
desc:
keywords:
---


<!--more-->

有这样的代码：

```
class Thread_Video : public QObject {
    Q_OBJECT

public:
    explicit Thread_Video(QObject *parent = nullptr);
    ~Thread_Video();

    void setSelectedROI(cv::Rect selectedROI){        
        this->selectedROI = selectedROI;
    }

private:
    cv::Rect selectedROI;
	
}	
```

主要是设置成员变量的方法: <b>setSelectedROI</b>，看起来没问题，但是实际运行的时候发现，如果有多个Thread_Video对象，使用setSelectedROI进行私有变量selectedROI设置的时候，只有第一个Thread_Video对象的selectROI对象被设置成功了，其他的Thread_Video对象的selectedROI私有变量的值都是0。


才疏学浅只找到了解决方法， 但是不知道原因。


解决方法，形参不要和私有变量名字重复，不清楚是变量拷贝问题还是作用域问题：

```
void setSelectedROI(cv::Rect roi){        
        this->selectedROI = roi;
}
```

血泪的两小时教训。。。
