# StrictMode

为了获得更多出现在代码中的关于公共问题的信息，我们激活了`StrictMode`模式。

`StrictMode`帮助我们侦测敏感的活动，如我们无意的在主线程执行磁盘访问或者网络调用。正如你所知道的，在主线程执行繁重的或者长时的任务是不可取的。因为Android应用的主线程时UI线程，它被用来处理和UI相关的操作：这也是获得更平滑的动画体验和响应式App的唯一方法。

为了在我们的App中激活`StrictMode`，我们只需要在`MainActivity`中添加几行代码，即`onCreate()`方法中这样：
```java
@Override
public void onCreate() { 
    super.onCreate();
    if (BuildConfig.DEBUG) {
        StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder().detectAll().penaltyLog().build()); 
        StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder().detectAll().penaltyLog().build());
    } 
}
```
我们并不想它总是激活着，因此我们只在debug构建时使用。这种配置将报告每一种关于主线程用法的违规做法，并且这些做法都可能与内存泄露有关：`Activities`、`BroadcastReceivers`、`Sqlite`等对象。

选择了`penaltyLog()`，当违规做法发生时，`StrictMode`将会在logcat打印一条信息。



























