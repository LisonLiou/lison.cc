# ANR产生的原因及其定位分析



> ### ANR产生的原因：
>
> - **<u>KeyDispatchTimeout</u>** View的按键事件或者触摸事件在特定的时间（5秒）内无法得到响应。
> - **<u>BroadcastTimeout</u>** 原因是BroadcastReceiver的onReceive()函数运行在主线程中，在特定的时间（10秒）内无法完成处理。
> - **<u>ServiceTimeout</u>** 原因是Service的各个生命周期函数在特定时间（20秒）内无法完成处理。

### 典型的ANR问题场景

- 应用程序UI线程存在耗时操作，例如在UI线程中进行网络请求，数据库操作或者文件操作等，可能会导致UI线程无法及时处理用户输入等。当然在Android 4.0之后，如果在UI线程中进行网络操作，将会抛出NetworkOnMainThreadException异常。
- 应用程序的UI线程等待子线程释放某个锁，从而无法处理用户的输入。
- 耗时的动画需要大量的计算工作，可能导致CPU负载过重。

### ANR的定位和分析

见：/data/anr/traces.txt ，关于此文件的详细介绍此处不展开。



### ANR的避免和检测

#### **<u>StrictMode</u>** 严格模式StrictMode是Android SDK提供的一个用来检测代码中是否存在违规操作的工具类，StrictMode主要检测两大类问题：

- 线程策略ThreadPolicy
  - detectCustomSlowCalls 检测自定义耗时操作
  - detectDiskReads 检测是否存在磁盘读取操作
  - detectDiskWrites 检测是否存在磁盘写入操作
  - detectNetwork 检测是否存在网络操作

- 虚拟机策略VmPolicy
  - detectActivityLeaks 检测是否存在Activity泄漏
  - detectLeakedClosableObjects 检测是否存在未关闭的Closable对象泄漏
  - detectLeakedSqlLiteObjects 检测是否存在Sqlite对象泄漏
  - setClassInstanceLimit 检测类实例个数是否超过限制

可以看到，其中的ThreadPolicy可以用来检测可能存在的主线程耗时操作，解决这些检测到的问题能够减少应用发生ANR的概率。需要注意的是，我们只能在Debug版本中使用它，正式版需要关闭。StrictMode的使用方法如下：

``` java
@override
protected void onCreate(Bundle savedInstanceState){
    if(BuildConfig.DEBUG){
        //开启线程模式
        StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder().detectAll().penaltyLog().build());
    	
        //开启虚拟机模式
        StrictMode.setVmPolicy(new VmPolicy.Builder().detectAll().penaltyLog().build());
    }
    super.onCreate(savedInstanceState);
}
```

上面的初始化代码调用penaltyLog()表示在logcat中打印日志，调用detectAll方法表示启动所有的检测策略，我们也可以根据应用的具体需求只开启某些策略，语句如下：

``` java
StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder()
                           .detectDiskReads()
                           .detectDiskWrites()
                           .detectNetwork()
                           .penaltyLog()
                           .build())
    
StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder()
                      .detectActivityLeaks()
                      .detectLeakedSqlLiteObjects()
                      .detectLeakedClosableObjects()
                      .penaltyLog()
                      .build())
```



### BlockCanary

BlockCanary是一个非嵌入式的性能监控函数库，它的用法和LeakCanary类似，只不过后者监控应用的内存泄漏，而BlockCanary主要用来监控应用主线程的卡顿。它的基本原理是利用主线程的消息队列处理机制，通过对比消息分发开始和结束时的时间点来判断是否超过设定的时间，如果是，则判断为主线程卡顿。代码如下：

``` java
dependencies{
    compile 'com.github.moduth:blockcanary-android:1.2.1'
    
    //仅在debug包启用BlockCanary进行卡顿监控和提示的话，可以这么用
    debugCompile 'com.github.moduth:blockcanary-android:1.2.1'
    releaseCompile 'com.github.moduth:blockcanary-android:1.2.1'
}
```

然后在Application类中进行配置和初始化即可：

``` java
public class DemoApplication extends Application{
    @override
    public void onCreate(){
        //在主进程初始化调用
        BlockCanary.install(this, new AppBlockCanaryContext()).start();
    }
}

public class AppBlockCanaryContext extends BlockCanaryContext{
    // 实现各种上下文，包括应用标识符、用户uid、网络类型、卡慢判断阈值、Log保存位置等
}
```

