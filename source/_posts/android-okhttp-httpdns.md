---
title: android网络编程高可用实践：Okhttp 接入HttpDNS
tags:
  - okhttp
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
date: 2020-5-21 19:50:46
desc:
keywords: android网络编程高可用,安卓网络优化dns,OkHttp加入HttpDns
---

安卓网络请求中经常用到OkHttp库，用到域名请求API，使用到域名则必须与DNS交互，使用DNS域名解析则绕不开运营商的LocalDNS问题，域名污染、域名劫持问题，这也是很多场景客户那里打不开，我这里好好的情况的一种典型解释。上古的方法是客户端发起一次Http请求，服务端响应之后客户端根据httpStatus判断如果是某种情况，则更换IP或域名请求其他服务器，但是这样一次完整的请求实在太浪费时间，想一下你的http请求默认超时多少秒？(OkHttp默认10_000毫秒,黄花菜都热了)。

<!--more-->

以下内容为转载，后续将更新项目库中关于OkHttp配合HttpDNS提升APP可用性。

HttpDns 是什么？
HTTPDNS 利用 HTTP 协议与 DNS 服务器交互，代替了传统的基于 UDP 协议的 DNS 交互，绕开了运营商的 Local DNS，有效防止了域名劫持，提高域名解析效率。 链接：[HttpDns 原理是什么](http://www.linkedkeeper.com/detail/blog.action?bid=171)
简单理解，就是客户端把HTTP请求的域名发送到HttpDns服务商，HttpDns会把解析的IP地址传回客户端，来达到绕开运营商的 [Local DNS](https://blog.csdn.net/kim_weir/article/details/78465641)，有效防止了域名劫持，提高域名解析效率的目的。

下面我就不写了，快速通道：
[OkHttp接入HttpDNS，最佳实践](https://blog.csdn.net/Lee_Swifter/article/details/78236173)
[Android OkHttp实现HttpDns的最佳实践](https://blog.csdn.net/sbsujjbcy/article/details/51612832)
[OKHttp 如何支持 HttpDns](https://blog.csdn.net/zhuoxiuwu/article/details/50756379)
[HTTPS IP直连问题小结](https://blog.csdn.net/leelit/article/details/77829196)
[基于OkHttp3 加入HttpDns功能](https://www.jianshu.com/p/44122b58a8fe)
[okhttp https ip直连设置Host](https://blog.csdn.net/y505772146/article/details/80679686)

[HttpDNS功能说明及实现](https://www.jianshu.com/p/15ff2aeb5b5b)
[OkHttpDNS实现原理](http://guofeng007.com/2018/01/12/okhttpdns/)

资源：
[DNSPOD | D+](https://www.dnspod.cn/httpdns)
[阿里云 HTTPDNS](https://www.aliyun.com/product/httpdns)
SDK：
[七牛-安卓版(支持D+企业版加密功能)](https://github.com/qiniu/happy-dns-android)
[七牛-OC版(支持D+企业版加密功能)](https://github.com/qiniu/happy-dns-objc)
[新浪-安卓版(支持D+企业版加密功能)](https://github.com/CNSRE/HTTPDNSLib)
七牛接入：

**implementation ‘com.qiniu:happy-dns:0.2.13’**



okhttp设置dns

```
private static class HttpDns implements Dns{
private DnsManager dnsManager;
public HttpDns(){
	try {
		IResolver[] resolvers = new IResolver[1];
		resolvers[0] = new Resolver(getByName(“119.29.29.29”));
		dnsManager = new DnsManager(NetworkInfo.normal, resolvers);
	} catch (UnknownHostException e) {
		e.printStackTrace();
	}
}

@Override
public List<InetAddress> lookup(String hostname) throws UnknownHostException {
	Log.d(“HttpDns”,”lookup==”+hostname);
	if (dnsManager == null) //当构造失败时使用默认解析方式
	return Dns.SYSTEM.lookup(hostname);

	try {
		String[] ips = dnsManager.query(hostname); //获取HttpDNS解析结果
		if (ips == null || ips.length == 0) {
			return Dns.SYSTEM.lookup(hostname);
		}

		List<InetAddress> result = new ArrayList<>();
		for (String ip : ips) { //将ip地址数组转换成所需要的对象列表
			result.addAll(Arrays.asList(getAllByName(ip)));
			Log.d(“HttpDns”,””+ip);
		}
		return result;
	} catch (IOException e) {
		e.printStackTrace();
	}
	//当有异常发生时，使用默认解析
	return Dns.SYSTEM.lookup(hostname);

	}
}

OkHttpClient mOkHttpClient = new OkHttpClient().newBuilder() .dns(new HttpDns()) .build();


```



**文章引用内容：**

1. [Okhttp 接入HttpDNS（支持http/https，）达到IP直连](https://blog.csdn.net/K_Hello/article/details/83992599)
2. [Android 网络优化，使用 HTTPDNS 优化 DNS，从原理到 OkHttp 集成](https://www.cnblogs.com/plokmju/p/okhttp_httpdns.html)
3. [OkHttp接入HttpDNS，最佳实践](https://www.jianshu.com/p/6bd131de81d3)