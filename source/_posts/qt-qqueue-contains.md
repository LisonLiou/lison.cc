---
title: Qt使用QQueue的contains方法的正确知识
tags:
  - 容器类
categories:
  - Qt
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2022-06-14 13:50:21
desc:
keywords:
---
### 从一段报错开始
```
D:\Qt\5.15.2\msvc2019\include\QtCore\qlist.h:1090: error: C2678: 二进制“==”: 没有找到接受“T”类型的左操作数的运算符(或没有可接受的转换)
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore/qmetaobject.h(200): note: 可能是“bool operator ==(const QMetaMethod &,const QMetaMethod &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qevent.h(876): note: 或    “bool operator ==(QPointingDeviceUniqueId,QPointingDeviceUniqueId) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtGui/qevent.h(842): note: 或    “bool operator ==(QKeySequence::StandardKey,QKeyEvent *)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qevent.h(841): note: 或    “bool operator ==(QKeyEvent *,QKeySequence::StandardKey)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qvector2d.h(211): note: 或    “bool operator ==(const QVector2D &,const QVector2D &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qsurfaceformat.h(165): note: 或    “bool operator ==(const QSurfaceFormat &,const QSurfaceFormat &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qquaternion.h(185): note: 或    “bool operator ==(const QQuaternion &,const QQuaternion &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qvector4d.h(236): note: 或    “bool operator ==(const QVector4D &,const QVector4D &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qvector3d.h(236): note: 或    “bool operator ==(const QVector3D &,const QVector3D &)”
C:\Program Files (x86)\Windows Kits\10\include\10.0.19041.0\shared\guiddef.h(192): note: 或    “bool operator ==(const GUID &,const GUID &)”
D:\Qt\5.15.2\msvc2019\include\QtGui/qcursor.h(124): note: 或    “bool operator ==(const QCursor &,const QCursor &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qrect.h(860): note: 或    “bool operator ==(const QRectF &,const QRectF &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qrect.h(454): note: 或    “bool operator ==(const QRect &,const QRect &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qsize.h(350): note: 或    “bool operator ==(const QSizeF &,const QSizeF &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qsize.h(175): note: 或    “bool operator ==(const QSize &,const QSize &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qmargins.h(372): note: 或    “bool operator ==(const QMarginsF &,const QMarginsF &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qmargins.h(135): note: 或    “bool operator ==(const QMargins &,const QMargins &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(279): note: 或    “bool operator ==(int,qfloat16) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(279): note: 或    “bool operator ==(qfloat16,int) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(268): note: 或    “bool operator ==(float,qfloat16) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(268): note: 或    “bool operator ==(qfloat16,float) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(267): note: 或    “bool operator ==(double,qfloat16) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(267): note: 或    “bool operator ==(qfloat16,double) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(266): note: 或    “bool operator ==(long double,qfloat16) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(266): note: 或    “bool operator ==(qfloat16,long double) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qfloat16.h(253): note: 或    “bool operator ==(qfloat16,qfloat16) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtNetwork/qhostaddress.h(165): note: 或    “bool operator ==(QHostAddress::SpecialAddress,const QHostAddress &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qvariant.h(611): note: 或    “bool operator ==(const QVariant &,const QVariantComparisonHelper &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qhash.h(139): note: 或    “bool operator ==(const QHashDummyValue &,const QHashDummyValue &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qpoint.h(357): note: 或    “bool operator ==(const QPointF &,const QPointF &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qpoint.h(165): note: 或    “bool operator ==(const QPoint &,const QPoint &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1983): note: 或    “bool operator ==(const QByteArray &,const QStringRef &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1976): note: 或    “bool operator ==(const QStringRef &,const QByteArray &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1967): note: 或    “bool operator ==(QLatin1String,QStringView) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1960): note: 或    “bool operator ==(QStringView,QLatin1String) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1952): note: 或    “bool operator ==(QChar,QStringView) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1945): note: 或    “bool operator ==(QStringView,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1937): note: 或    “bool operator ==(QStringView,QStringView) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1929): note: 或    “bool operator ==(QLatin1String,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1910): note: 或    “bool operator ==(const QStringRef &,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1891): note: 或    “bool operator ==(const QString &,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1872): note: 或    “bool operator ==(const QStringRef &,QLatin1String) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1865): note: 或    “bool operator ==(QLatin1String,const QStringRef &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1836): note: 或    “bool operator ==(const QStringRef &,const QString &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1829): note: 或    “bool operator ==(const QString &,const QStringRef &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1817): note: 或    “bool operator ==(const QStringRef &,const QStringRef &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1439): note: 或    “bool operator ==(const char *,QLatin1String)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1382): note: 或    “bool operator ==(QLatin1String,QLatin1String) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1374): note: 或    “bool operator ==(const QString &,QString::Null)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1372): note: 或    “bool operator ==(QString::Null,const QString &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1370): note: 或    “bool operator ==(QString::Null,QString::Null)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1918): note: 或    “bool operator ==(QChar,QLatin1String) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1899): note: 或    “bool operator ==(QChar,const QStringRef &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1880): note: 或    “bool operator ==(QChar,const QString &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(2004): note: 或    “bool operator ==(const char *,const QStringRef &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(1426): note: 或    “bool operator ==(const char *,const QString &)”
D:\Qt\5.15.2\msvc2019\include\QtCore/qstring.h(806): note: 或    “bool operator ==(const QString &,const QString &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qbytearray.h(800): note: 或    “bool operator ==(const QByteArray::FromBase64Result &,const QByteArray::FromBase64Result &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qbytearray.h(688): note: 或    “bool operator ==(const char *,const QByteArray &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qbytearray.h(686): note: 或    “bool operator ==(const QByteArray &,const char *) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qbytearray.h(684): note: 或    “bool operator ==(const QByteArray &,const QByteArray &) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qchar.h(648): note: 或    “bool operator ==(std::nullptr_t,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qchar.h(646): note: 或    “bool operator ==(QChar,std::nullptr_t) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qchar.h(637): note: 或    “bool operator ==(QChar,QChar) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qchar.h(68): note: 或    “bool operator ==(QLatin1Char,char) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qchar.h(61): note: 或    “bool operator ==(char,QLatin1Char) noexcept”
D:\Qt\5.15.2\msvc2019\include\QtCore/qlist.h(1090): note: 尝试匹配参数列表“(T, const T)”时
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore/qlist.h(1086): note: 在编译 类 模板 成员函数“bool QList<T>::contains_impl(const T &,QListData::NotArrayCompatibleLayout) const”时
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore/qlist.h(1081): note: 查看对正在编译的函数 模板 实例化“bool QList<T>::contains_impl(const T &,QListData::NotArrayCompatibleLayout) const”的引用
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore/qlist.h(872): note: 在编译 类 模板 成员函数“QList<T>::~QList(void)”时
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore\qqueue.h(63): note: 查看对正在编译的函数 模板 实例化“QList<T>::~QList(void)”的引用
        with
        [
            T=Msg
        ]
D:\Qt\5.15.2\msvc2019\include\QtCore\qqueue.h(50): note: 查看对正在编译的 类 模板 实例化“QList<T>”的引用
        with
        [
            T=Msg
        ]
D:\repos\qt\SCLIOS_ZCHG9_GLSBLJZ\Form_GuLunShiBuLanJiZu.h(172): note: 查看对正在编译的 类 模板 实例化“QQueue<Msg>”的引用
```
<!--more-->

### 报错位置代码行

```
QQueue<Msg> msgQueue;
```

瞪大眼睛看了又看，就这一行声明，能有什么错？开玩笑。。。


### Msg代码
```
typedef struct Message{
    unsigned int Id;
    QString MsgContent;
    MsgType MsgType;
}Msg;
```

瞪大眼睛看了又看，就这几行结构体声明，能有什么错？还开玩笑。。。

### 扩大范围，使用Msg的地方
```
if(msgQueue.contains(msg)) return;

msgQueue.enqueue(msg);

emit signal_msg_got();
```

if处使用了contains判断，contains根据什么判断？详情请按F1得到的答复如下，[原文地址](https://doc.qt.io/qt-5/qlist.html#contains)
```
bool QList::contains(const T &value) const
Returns true if the list contains an occurrence of value; otherwise returns false.

This function requires the value type to have an implementation of operator==().
```

人家说了contains得使用==双等号操作符进行判断，那么修改之后的Msg结构如下

```
typedef struct Message{
    unsigned int Id;
    QString MsgContent;
    MsgType MsgType;

    bool operator==(const Message &m){    //用于contains比较
        return m.Id==Id&&m.MsgContent==MsgContent&&m.MsgType==MsgType;
    }
}Msg;
```

再点小锤子，不报错了吧。为什么要用小锤子呢，之前是铁匠吗？？



