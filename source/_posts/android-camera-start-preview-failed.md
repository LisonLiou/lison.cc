---
title: Android Camera： start preview failed; API10. Android 2.3 startPreview失败解决方法
tags:
  - android camera
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
date: 2016-3-11 12:06:10
desc: Android 2.3机器运行报错：Unexpected error initializing camera java.lang.RuntimeException： startPreview failed
keywords: start preview failed,startPreview失败解决方法
---

代码中使用了开源项目([OSChina 客户端2.0](http://www.oschina.net/app))的源码，zxing做二维码扫描识别。搬过来之后在Android 2.3机器运行报错：
Unexpected error initializing camera java.lang.RuntimeException: startPreview failed

<!--more-->

startPreview失败报错详细如下：

```
Unexpected error initializing camera
 java.lang.RuntimeException: startPreview failed
     at android.hardware.Camera.startPreview(Native Method)
     at com.dtr.zxing.camera.CameraManager.startPreview(CameraManager.java:149)
     at com.dtr.zxing.utils.CaptureActivityHandler.<init>(CaptureActivityHandler.java:57)
     at com.dtr.zxing.activity.CaptureActivity.initCamera(CaptureActivity.java:285)
     at com.dtr.zxing.activity.CaptureActivity.surfaceCreated(CaptureActivity.java:179)
     at android.view.SurfaceView.updateWindow(SurfaceView.java:574)
     at android.view.SurfaceView.updateWindow(SurfaceView.java:411)
     at android.view.SurfaceView.dispatchDraw(SurfaceView.java:355)
     at android.view.ViewGroup.drawChild(ViewGroup.java:1644)
     at android.view.ViewGroup.dispatchDraw(ViewGroup.java:1373)
     at android.view.View.draw(View.java:6883)
     at android.view.ViewGroup.drawChild(ViewGroup.java:1646)
     at android.view.ViewGroup.dispatchDraw(ViewGroup.java:1373)
     at android.view.View.draw(View.java:6883)
     at android.widget.FrameLayout.draw(FrameLayout.java:357)
     at android.view.ViewGroup.drawChild(ViewGroup.java:1646)
     at android.view.ViewGroup.dispatchDraw(ViewGroup.java:1373)
     at android.view.View.draw(View.java:6883)
     at android.support.v7.widget.ActionBarOverlayLayout.draw(ActionBarOverlayLayout.java:442)
     at android.view.ViewGroup.drawChild(ViewGroup.java:1646)
     at android.view.ViewGroup.dispatchDraw(ViewGroup.java:1373)
     at android.view.ViewGroup.drawChild(ViewGroup.java:1644)
     at android.view.ViewGroup.dispatchDraw(ViewGroup.java:1373)
     at android.view.View.draw(View.java:6883)
     at android.widget.FrameLayout.draw(FrameLayout.java:357)
     at com.android.internal.policy.impl.PhoneWindow$DecorView.draw(PhoneWindow.java:1921)
     at android.view.ViewRoot.draw(ViewRoot.java:1526)
     at android.view.ViewRoot.performTraversals(ViewRoot.java:1262)
     at android.view.ViewRoot.handleMessage(ViewRoot.java:1863)
     at android.os.Handler.dispatchMessage(Handler.java:99)
     at android.os.Looper.loop(Looper.java:130)
     at android.app.ActivityThread.main(ActivityThread.java:3687)
     at java.lang.reflect.Method.invokeNative(Native Method)
     at java.lang.reflect.Method.invoke(Method.java:507)
     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:878)
     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:636)
     at dalvik.system.NativeStart.main(Native Method)
```

找到了一些文章，有的说Camera的open与startPreview不能多次调用，需要设置标记，查看OSChina的源码之后发现有标记，不是这个。

后来找到说低版本的SDK需要setType一下，设置成：SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS，尝试后依然没有解决问题。

最后是借鉴了[这篇文章](http://codego.net/373180/)（不知道是不是原始链接）解决了问题，holderView要放在holder的allCallback前面，完整如下：

```
@Override
    protected void onResume() {
        super.onResume();

        // CameraManager must be initialized here, not in onCreate(). This is
        // necessary because we don't
        // want to open the camera driver and measure the screen size if we're
        // going to show the help on
        // first launch. That led to bugs where the scanning rectangle was the
        // wrong size and partially
        // off screen.
        cameraManager = new CameraManager(getApplication());

        handler = null;

        if (isHasSurface) {
            // The activity was paused but not stopped, so the surface still
            // exists. Therefore
            // surfaceCreated() won't be called, so init the camera here.
            initCamera(scanPreview.getHolder());
        } else {
            // Install the callback and wait for surfaceCreated() to init the
            // camera.

            if (Build.VERSION.SDK_INT < 11)
                scanPreview.getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);

            scanPreview.getHolder().addCallback(this);
        }

        inactivityTimer.onResume();
    }
```

就是这两句：

**if(Build.VERSION.SDK_INT<11)
****scanPreview.getHolder().setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);**

**scanPreview.getHolder().addCallback(this);**

OSChina客户端的2.0支持的最低版本是15，已经不再支持API10，不能PullRequest一下了。