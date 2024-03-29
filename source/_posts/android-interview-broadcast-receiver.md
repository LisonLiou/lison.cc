---
title: 安卓基础 - 关于Broadcast 和 Broadcast Receiver
tags:
  - 安卓架构师面试题
categories:
  - 安卓面试
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2021-08-13 17:01:46
desc:
keywords: 安卓大厂面试题、安卓广播广播接收器知识点
---


<!--more-->


## 1. 什么是Broadcast和Broadcast Receiver

Broadcast（广播）是一种广泛在应用程序之间传输信息的机制，而BroadcastReceiver（广播接收器）则是用于接收来自系统和应用的广播对其进行响应的组件。Android提供了一套完整的API，允许应用程序自由的发送和接收广播，其中又用到可以传递信息的Intent。广播其实是一种订阅者模式，所以当然需要先register，register的方式有两种：

1. **静态注册** 在manifest的application节点中添加receiver标签。
   静态注册意味着应用在安装后就开始接收广播，一旦接收广播系统就会打开应用进程，但是如果应用是在stopped状态下就不会接收到广播。

2. **动态注册**

   ```java
   BroadcastReceiver br = new MyBroadcastReceiver();
   IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
   intentFilter.addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED);
   this.registerReceiver(br, filter);
   ```

   记得在onDestroy时调用unregisterReceiver避免内存泄漏。

**广播的发送和接收方法：**

1. **普通广播**
   普通广播是一种完全异步执行的广播，在广播发出后，所有的广播接收器几乎都会在同一时刻接收到这条广播消息，因此他们接收的顺序是不固定的。另外，接收器不能拦截普通广播。

2. **有序广播**
   有序广播是一种同步执行的广播，在广播发出之后，同一时刻只会有一个广播接收器能够收到这条广播，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递，所以此时的广播接收器是有先后顺序的，且优先级（priority）高的广播接收器会先收到广播消息。有序广播可以被接收器截断使得后面的接收器无法接收到它。应用场景比如收到短信，电话拨入。

   ```java
   // 发送一个有序广播，广播接收器不指定权限(null)，接收顺序按照manifest配置的广播接收器的priority优先级
   Intent intent = new Intent("com.example.MY_BROADCAST");
   sendOrderedBroadcast(intent, null);
   ```

   ```java
   // 在Broadcast Receiver的onReceive()中截断广播，不继续发送，则顺序之后的广播接收器将不会收到广播
   abortBroadcast();
   
   //传递结果到下一个广播接收器
   int code = 0;
   String data = "hello";
   Bundle bundle = null;
   setResult(code, data, bundle);
   ```

   

3. **本地广播**

   上述两种广播都属于全局广播，即发出的广播可被其他应用程序接收到，并且我们也可以接收到其他任何应用程序发送的广播。为了能够简单的解决全局广播可能带来的安全性问题，Android引入了一套本地广播机制，使用这个机制发出的广播只能够再应用程序内部进行传递，并且广播接收器也只能接收本应用程序发出的广播。

   API21的Support v4包中新增本地广播 LocalBroadcastManager，用法很简单，只需要把调用context.sendBroadcast、registerReceiver、unregisterReceiver的地方换为LocalBroadcastManager.getInstance(Context context)中对应的函数即可。创建广播的过程和普通广播是一样的，不赘述。

   ```java
   // 创建广播接收器
   receiver = new MyBroadcastReceiver();
   // 注册本地广播
   LocalBroadcastManager.getInstance(ReceiverActivity.this).registerReceiver(receiver, new IntentFilter('com.example.xxx'));
   ```

   ```java
   // 反注册receiver
   LocalBroadcastManager.getInstance(ReceiverActivity.this).unregisterReceiver(receiver);
   ```

   ```java
   // 发送异步广播
   final Intent intent = new Intent();
   intent.setAction("com.example.xxx");
   LocalBroadcastManager.getInstance(ReceiverActivity.this).sendBroadcast(intent);
   ```

   ```java
   LocalBroadcastManager.getInstance(ReceiverActivity.this).sendBroadcastSync(intent);
   ```

   注意一点，本地广播是无法通过静态注册方式来接受的，因为静态注册主要就是为了让程序在未启动的情况下也能收到广播，而发送本地广播时，应用程序肯定已经启动了，也完全不需要使用静态注册的功能。


5. **粘性广播**

   通过Context.sendStickyBroadcast()方法可发送粘性广播，这种广播会一直滞留，当有匹配该广播的接收器被注册后，该接收器就会收到此条广播。发送粘性广播需要**BROADCAST_STICKY**权限。

   ```xml
   <uses-permission android:name="android.permission.BROADCAST_STICKY"/>
   ```

   sendStickyBroadcast()只保留最后一条广播，并且一直保留下去，这样即使已经有广播接收器处理了该广播，一旦又有匹配的广播接收器被注册，该粘性广播仍会被接收。如果只想处理一遍该广播，可通过**removeStickyBroadcast()**方法来实现。

6. **系统广播**

   常用系统广播

    ```java
   intent.action.AIRPLANE_MODE;
   //关闭或打开飞行模式时的广播
   
   Intent.ACTION_BATTERY_CHANGED;
   //充电状态，或者电池的电量发生变化
   //电池的充电状态、电荷级别改变，不能通过组建声明接收这个广播，只有通过Context.registerReceiver()注册
   
   Intent.ACTION_BATTERY_LOW;
   //表示电池电量低
   
   Intent.ACTION_BATTERY_OKAY;
   //表示电池电量充足，即从电池电量低变化到饱满时会发出广播
   
   Intent.ACTION_BOOT_COMPLETED;
   //在系统启动完成后，这个动作被广播一次（只有一次）。
   
   Intent.ACTION_CAMERA_BUTTON;
   //按下照相时的拍照按键(硬件按键)时发出的广播
   
   Intent.ACTION_CLOSE_SYSTEM_DIALOGS;
   //当屏幕超时进行锁屏时,当用户按下电源按钮,长按或短按(不管有没跳出话框)，进行锁屏时,android系统都会广播此Action消息
   
   Intent.ACTION_CONFIGURATION_CHANGED;
   //设备当前设置被改变时发出的广播(包括的改变:界面语言，设备方向，等，请参考Configuration.java)
   
   Intent.ACTION_DATE_CHANGED;
   //设备日期发生改变时会发出此广播
   
   Intent.ACTION_DEVICE_STORAGE_LOW;
   //设备内存不足时发出的广播,此广播只能由系统使用，其它APP不可用？
   
   Intent.ACTION_DEVICE_STORAGE_OK;
   //设备内存从不足到充足时发出的广播,此广播只能由系统使用，其它APP不可用？
   
   Intent.ACTION_DOCK_EVENT;
   //
   //发出此广播的地方frameworks\base\services\java\com\android\server\DockObserver.java
   
   Intent.ACTION_EXTERNAL_APPLICATIONS_AVAILABLE;
   ////移动APP完成之后，发出的广播(移动是指:APP2SD)
   
   Intent.ACTION_EXTERNAL_APPLICATIONS_UNAVAILABLE;
   //正在移动APP时，发出的广播(移动是指:APP2SD)
   
   Intent.ACTION_GTALK_SERVICE_CONNECTED;
   //Gtalk已建立连接时发出的广播
   
   Intent.ACTION_GTALK_SERVICE_DISCONNECTED;
   //Gtalk已断开连接时发出的广播
   
   Intent.ACTION_HEADSET_PLUG;
   //在耳机口上插入耳机时发出的广播
   
   Intent.ACTION_INPUT_METHOD_CHANGED;
   //改变输入法时发出的广播
   
   Intent.ACTION_LOCALE_CHANGED;
   //设备当前区域设置已更改时发出的广播
   
   Intent.ACTION_MANAGE_PACKAGE_STORAGE;
   //
   
   Intent.ACTION_MEDIA_BAD_REMOVAL;
   //未正确移除SD卡(正确移除SD卡的方法:设置--SD卡和设备内存--卸载SD卡)，但已把SD卡取出来时发出的广播
   //广播：扩展介质（扩展卡）已经从 SD 卡插槽拔出，但是挂载点 (mount point) 还没解除 (unmount)
   
   Intent.ACTION_MEDIA_BUTTON;
   //按下"Media Button" 按键时发出的广播,假如有"Media Button" 按键的话(硬件按键)
   
   Intent.ACTION_MEDIA_CHECKING;
   //插入外部储存装置，比如SD卡时，系统会检验SD卡，此时发出的广播?
   Intent.ACTION_MEDIA_EJECT;
   //已拔掉外部大容量储存设备发出的广播（比如SD卡，或移动硬盘）,不管有没有正确卸载都会发出此广播?
   //广播：用户想要移除扩展介质（拔掉扩展卡）。
   Intent.ACTION_MEDIA_MOUNTED;
   //插入SD卡并且已正确安装（识别）时发出的广播
   //广播：扩展介质被插入，而且已经被挂载。
   Intent.ACTION_MEDIA_NOFS;
   //
   Intent.ACTION_MEDIA_REMOVED;
   //外部储存设备已被移除，不管有没正确卸载,都会发出此广播？
   // 广播：扩展介质被移除。
   Intent.ACTION_MEDIA_SCANNER_FINISHED;
   //广播：已经扫描完介质的一个目录
   Intent.ACTION_MEDIA_SCANNER_SCAN_FILE;
   //
   Intent.ACTION_MEDIA_SCANNER_STARTED;
   //广播：开始扫描介质的一个目录
   
   Intent.ACTION_MEDIA_SHARED;
   // 广播：扩展介质的挂载被解除 (unmount)，因为它已经作为 USB 大容量存储被共享。
    Intent.ACTION_MEDIA_UNMOUNTABLE;
   //
   Intent.ACTION_MEDIA_UNMOUNTED
   // 广播：扩展介质存在，但是还没有被挂载 (mount)。
   Intent.ACTION_NEW_OUTGOING_CALL;
   
   Intent.ACTION_PACKAGE_ADDED;
   //成功的安装APK之后
   //广播：设备上新安装了一个应用程序包。
   //一个新应用包已经安装在设备上，数据包括包名（最新安装的包程序不能接收到这个广播）
    Intent.ACTION_PACKAGE_CHANGED;
   //一个已存在的应用程序包已经改变，包括包名
   Intent.ACTION_PACKAGE_DATA_CLEARED;
   //清除一个应用程序的数据时发出的广播(在设置－－应用管理－－选中某个应用，之后点清除数据时?)
   //用户已经清除一个包的数据，包括包名（清除包程序不能接收到这个广播）
   
   Intent.ACTION_PACKAGE_INSTALL;
   //触发一个下载并且完成安装时发出的广播，比如在电子市场里下载应用？
   //
   Intent.ACTION_PACKAGE_REMOVED;
   //成功的删除某个APK之后发出的广播
   //一个已存在的应用程序包已经从设备上移除，包括包名（正在被安装的包程序不能接收到这个广播）
   
   Intent.ACTION_PACKAGE_REPLACED;
   //替换一个现有的安装包时发出的广播（不管现在安装的APP比之前的新还是旧，都会发出此广播？）
   Intent.ACTION_PACKAGE_RESTARTED;
   //用户重新开始一个包，包的所有进程将被杀死，所有与其联系的运行时间状态应该被移除，包括包名（重新开始包程序不能接收到这个广播）
   Intent.ACTION_POWER_CONNECTED;
   //插上外部电源时发出的广播
   Intent.ACTION_POWER_DISCONNECTED;
   //已断开外部电源连接时发出的广播
   Intent.ACTION_PROVIDER_CHANGED;
   //
   
   Intent.ACTION_REBOOT;
   //重启设备时的广播
   
   Intent.ACTION_SCREEN_OFF;
   //屏幕被关闭之后的广播
   
   Intent.ACTION_SCREEN_ON;
   //屏幕被打开之后的广播
   
   Intent.ACTION_SHUTDOWN;
   //关闭系统时发出的广播
   
   Intent.ACTION_TIMEZONE_CHANGED;
   //时区发生改变时发出的广播
   
   Intent.ACTION_TIME_CHANGED;
   //时间被设置时发出的广播
   
   Intent.ACTION_TIME_TICK;
   //广播：当前时间已经变化（正常的时间流逝）。
   //当前时间改变，每分钟都发送，不能通过组件声明来接收，只有通过Context.registerReceiver()方法来注册
   
   Intent.ACTION_UID_REMOVED;
   //一个用户ID已经从系统中移除发出的广播
   //
   
   Intent.ACTION_UMS_CONNECTED;
   //设备已进入USB大容量储存状态时发出的广播？
   
   Intent.ACTION_UMS_DISCONNECTED;
   //设备已从USB大容量储存状态转为正常状态时发出的广播？
   
   Intent.ACTION_USER_PRESENT;
   //
   
   Intent.ACTION_WALLPAPER_CHANGED;
   //设备墙纸已改变时发出的广播
    ```

   

注意事项：不要在广播里添加过多逻辑或者进行任何耗时操作，因为在广播中是不允许开辟线程的，当onReceiver()方法运行较长时间（超过10秒）还没有结束的话，那么程序会报错（ANR），广播更多的时候扮演的是一个打开其他组件的角色，比如启动Service，Notification，Activity等。

```shell
//如何测试广播
adb shell am broadcast -a android.intent.action.BOOT_COMPLETED
```

广播的发送者和接收者事先是不需要知道对方的存在的，这样带来的好处便是，系统各个组件可以松耦合的组织在一起，这样系统就具有高度的可扩展性，容易与其他系统进行集成。

## 2. 为什么安装在SD卡中的应用无法收到开机广播

在Android API Level8之后，程序可以安装在SD卡上（manifest android:installLocation选项）。如果程序安装在SD卡上，系统启动完成发送BOOT_COMPLETED广播之后，SD卡才会挂在，因此程序无法监听到该广播。

**解决办法：** 同时监听开机和SD卡挂在广播（不能只监听挂在就认为系统以开机，因为有的手机没有sd卡）

如何实现一个能同时监听开机BOOT_COMPLETED和挂载MEDIA_MOUNTED的广播接收器呢？理论上只要将MEDIA_MOUNTED的intent-filter和BOOT_COMPLETED的intent-filter放在一起就行了，但是放在同一个intent-filter里，BOOT_COMPLETED会监听不到，需要单独放置再两个intent-filter中：

```xml
<receiver android:name=".Ge">          
    <intent-filter >  
        <action android:name="android.intent.action.BOOT_COMPLETED"/>  
    </intent-filter>  
    <intent-filter >  
        <action android:name="android.intent.action.MEDIA_MOUNTED"/>  
        <action android:name="android.intent.action.MEDIA_UNMOUNTED"/>  
        <data android:scheme="file">  
        </data>  
    </intent-filter>  
</receiver>  
```

**无法接收开机广播的情况还有以下几种：**

1. BOOT_COMPLETED对应的action和uses-permission没有一起添加

2. 应用安装到了SD卡内，安装再sd卡内的应用是收不到BOOT_COMPLETED广播的

3. 系统开启了Fast Boot模式，这种模式下系统启动并不会发送BOOT_COMPLETED广播

4. 应用程序安装后从来没有启动过，这种情况下应用程序接收不到任何广播，包括BOOT_COMPLETED、ACTION_PACKAGE_ADDED、CONNECTIVITY_ACTION等。系统为了加强安全性控制，应用程序安装后或是（设置）应用管理中被强制关闭后处于stopped状态，在这种状态下接收不到任何广播。直到被启动过（用户打开或是其他应用调用）才会脱离这种状态，所以Androiod 3.1之后

   1. 应用程序无法在安装后自己启动
   2. 没有UI的程序必须通过其他应用激活才能启动，如它的Activity、Service、Content Provider被其他应用调用。

5. 针对上述应用处于stopped的情况，在发送广播时，可以使用允许发送给stopped状态下的应用FLAG

   ```java
   // 默认是FLAG_EXCLUDE_STOPPED_PACKAGES
   intent = new Intent(intent);
   // 允许发送给stopped状态下的应用
   intent.addFlags(Intent.FLAG_INCLUDE_STOPPED_PACKAGES);
   ```
   
   

所以为了确保可以接收到BOOT_COMPLETED广播：

1. 安装应用后，首先要启动一次

2. 添加一下权限：

   ```xml
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" /> 
   <uses-permission android:name="android.permission.RESTART_PACKAGES" />
   ```

3.  ```xml
   <receiver android:name=".BootBroadcastReceiver" >  
               <intent-filter>  
                   <action android:name="android.intent.action.BOOT_COMPLETED" />  
     
                   <category android:name="android.intent.category.HOME" />  
               </intent-filter>  
               <intent-filter>  
                   <action android:name="android.intent.action.PACKAGE_ADDED" />  
                   <action android:name="android.intent.action.PACKAGE_REMOVED" />  
                   <action android:name="android.intent.action.PACKAGE_REPLACED" />  
     
                   <data android:scheme="package" />  
               </intent-filter>  
           </receiver>  
   ```


4. ```java
   public class BootBroadcastReceiver extends BroadcastReceiver  
   {  
       @Override  
       public void onReceive(Context context, Intent intent)  
       {  
           //接收广播：系统启动完成后运行程序  
           if (intent.getAction().equals(Intent.ACTION_BOOT_COMPLETED))  
           {  
               Intent ootStartIntent = new Intent(context, Login_Activity.class);  
               ootStartIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);  
               context.startActivity(ootStartIntent);  
           }  
           //接收广播：安装更新后，自动启动自己。        
           if (intent.getAction().equals(Intent.ACTION_PACKAGE_ADDED) || intent.getAction().equals(Intent.ACTION_PACKAGE_REPLACED))  
           {  
               Intent ootStartIntent = new Intent(context, Login_Activity.class);  
               ootStartIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);  
               context.startActivity(ootStartIntent);  
           }  
       }  
   }  
   ```

5. 最后一种情况，看下系统中是否有360管家之类的流氓软件，他们会将一些开机广播给屏蔽掉，加快开机速度，只需将其打开即可。