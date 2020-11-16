---
title: Android WebView JS 与 Java Native 相互调用示例
tags:
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
date: 2017-4-11 11:12:08
desc: Android WebView调用js, Android WebView js与java本地相互调用
keywords: Android WebView调用js, Android WebView js与java本地相互调用
---

直接上代码，有注释，学习Android过程记录。

Android Activity代码

<!--more-->

```
，并暴露对象名为ntv，js使用window.ntv.functionName()方式调用
        wv.addJavascriptInterface(new JsCall(), "ntv");

        ws = wv.getSettings();
        ws.setJavaScriptEnabled(true);

        //不使用缓存
        ws.setCacheMode(WebSettings.LOAD_NO_CACHE);
        //支持缩放
        ws.setSupportZoom(true);
        //自适应屏幕
        ws.setUseWideViewPort(true);
        ws.setLoadWithOverviewMode(true);
    }

    //js调用的类及方法
    class JsCall {
        @JavascriptInterface
        public String tost(String msg) {
            Toast.makeText(activity, "toast: " + msg, Toast.LENGTH_SHORT).show();
            return "JsCalled Native: " + msg;
        }
    }

    //回退栈（WebView页面）
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            if (wv.canGoBack()) {
                wv.goBack();//返回上一浏览页面
                return true;
            } else {
                finish();//关闭Activity
            }
        }
        return super.onKeyDown(keyCode, event);
    }

    //ActionBar菜单
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.main, menu);
        return super.onCreateOptionsMenu(menu);
    }

    //ActionBar菜单响应
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.menu_item:
                Toast.makeText(this, "Menu Item", Toast.LENGTH_SHORT).show();
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
}
```

布局文件LinearLayout布局，orentation=vertical，一个EditText，一个Button一个WebView，长什么样子的话想象一下就好了。

html文件，同样非常简单

```
<html>
<head>
	<title>DEMO TEST</title>
	<script type="text/javascript">
		function fill(val)
		{
			var txt=document.getElementById('txtinput');
			txt.value=val;
			alert(val);
		}
		function native()
		{
			var txt=document.getElementById('txtinput');
			alert(window.ntv.tost(txt.value));
		}
	</script>

	<style type="text/css">

body{
	font-size: 20px;
}
	</style>
</head>
<body>
	<input type="text" id="txtinput" /><br />
	<button onclick="native()">CALL JAVA NATIVE</button>
</body>
```

参考资料：[developer.android.com WebView](https://developer.android.com/reference/android/webkit/WebView.html)

源码下载：[WebViewNJs](http://git.oschina.net/lison/webviewnjs)