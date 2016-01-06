# 避免阻塞I/O的操作

阻塞I/O的操作会导致App必须等待结果返回（阻塞结束）才能进行下一步操作。在UI线程上执行一个阻塞操作会将UI强行卡住，直接造成很糟糕的用户体验。

我们激活`StrictMode`后，我们开始收到了关于我们的App错误操作磁盘I/O的不良信息。

```java
D/StrictMode  StrictMode policy violation; ~duration=998 ms: android.os.StrictMode$StrictModeDiskReadViolation: policy=31 violation=2
at android.os.StrictMode$AndroidBlockGuardPolicy.onReadFromDisk (StrictMode.java:1135)
at libcore.io.BlockGuardOs.open(BlockGuardOs.java:106) at libcore.io.IoBridge.open(IoBridge.java:393)
at java.io.FileOutputStream.<init>(FileOutputStream.java:88) 
at android.app.ContextImpl.openFileOutput(ContextImpl.java:918) 
at android.content.ContextWrapper.openFileOutput(ContextWrapper. java:185)
at com.packtpub.apps.rxjava_essentials.Utils.storeBitmap (Utils.java:30)
```
上一条信息告诉我们`Utils.storeBitmap()`函数执行完耗时998ms：在UI线程上近1秒的不必要的工作和App上近1秒不必要的迟钝。这是因为我们以阻塞的方式访问磁盘。我们的`storeBitmap()`函数包含了：
```java
FileOutputStream fOut = context.openFileOutput(filename, Context.MODE_PRIVATE);
```
它直接访问智能手机的固态存储然后就慢了。我们该如何提高访问速度呢？`storeBitmap()`函数保存了已安装App的图标。他的返回值类型为`void`，因此在执行下一个操作前我们毫无理由去等待直到它完成。我们可以启动它并让它执行在不同的线程。近几年来Android的线程管理发生了许多变化，导致App出现诡异的行为。我们可以使用`AsyncTask`，但是我们要避免掉入前几章里的`onPre... onPost...doInBackGround`地狱。下面我们将换用RxJava的方式。调度器万岁！


























