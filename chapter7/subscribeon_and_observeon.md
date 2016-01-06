# SubscribeOn and ObserveOn

我们学到了如何在一个调度器上运行一个任务。但是我们如何利用它来和Observables一起工作呢？RxJava提供了`subscribeOn()`方法来用于每个Observable对象。`subscribeOn()`方法用`Scheduler`来作为参数并在这个Scheduler上执行Observable调用。

在“真实世界”这个例子中，我们调整`loadList()`函数。首先，我们需要一个新的`getApps()`方法来检索已安装的应用列表：

```java
private Observable<AppInfo> getApps() { 
    return Observable.create(subscriber -> {
        List<AppInfo> apps = new ArrayList<>();
        SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
        Type appInfoType = new TypeToken<List<AppInfo>>(){}.getType();
        String serializedApps = sharedPref.getString("APPS", "");
        if (!"".equals(serializedApps)) {
            apps = new Gson().fromJson(serializedApps,appInfoType); 
        }
        for (AppInfo app : apps) {
            subscriber.onNext(app);
        }
        subscriber.onCompleted(); 
    });
}
```
`getApps()`方法返回一个`AppInfo`的Observable。它先从Android的SharePreferences读取到已安装的应用程序列表。反序列化，并一个接一个的发射AppInfo数据。使用新的方法来检索列表，`loadList()`函数改成下面这样：
```java
private void loadList() {
    mRecyclerView.setVisibility(View.VISIBLE);
    getApps().subscribe(new Observer<AppInfo>() {
        @Override
        public void onCompleted() {
            mSwipeRefreshLayout.setRefreshing(false);
            Toast.makeText(getActivity(), "Here is the list!", Toast.LENGTH_LONG).show();
        }
        
        @Override
        public void onError(Throwable e) {
            Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
            mSwipeRefreshLayout.setRefreshing(false);
        }
        
        @Override
        public void onNext(AppInfo appInfo) {
            mAddedApps.add(appInfo);
                mAdapter.addApplication(mAddedApps.size() - 1, appInfo);
        } 
    });
}
```
如果我们运行代码，`StrictMode`将会报告一个不合规操作，这是因为`SharePreferences`会减慢I/O操作。我们所需要做的是指定`getApps()`需要在调度器上执行：

```java

getApps().subscribeOn(Schedulers.io())
        .subscribe(new Observer<AppInfo>() { [...]
```
`Schedulers.io()`将会去掉`StrictMode`的不合规操作，但是我们的App现在崩溃了是因为：
```java
at rx.internal.schedulers.ScheduledAction.run(ScheduledAction.jav a:58)
at java.util.concurrent.Executors$RunnableAdapter.call(Executors. java:422)
at java.util.concurrent.FutureTask.run(FutureTask.java:237) 
at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutu reTask.access$201(ScheduledThreadPoolExecutor.java:152)
at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutu reTask.run(ScheduledThreadPoolExecutor.java:265)
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolEx ecutor.java:1112)
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolE xecutor.java:587)
at java.lang.Thread.run(Thread.java:841) Caused by:
    android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.

```
Only the original thread that created a view hierarchy can touch its views.

我们再次回到Android的世界。这条信息简单的告诉我们我们试图在一个非UI线程来修改UI操作。意思是我们需要在I/O调度器上执行我们的代码。因此我们需要和I/O调度器一起执行代码，但是当结果返回时我们需要在UI线程上操作。RxJava让你能够订阅一个指定的调度器并观察它。我们只需在`loadList()`函数添加几行代码，那么每一项就都准备好了：

```java
getApps()
.onBackpressureBuffer()
.subscribeOn(Schedulers.io())
.observeOn(AndroidSchedulers.mainThread())
.subscribe(new Observer<AppInfo>() { [...]
```
`observeOn()`方法将会在指定的调度器上返回结果：如例子中的UI线程。`onBackpressureBuffer()`方法将告诉Observable发射的数据如果比观察者消费的数据要更快的话，它必须把它们存储在缓存中并提供一个合适的时间给它们。做完这些工作之后，如果我们运行App，就会出现已安装的程序列表：

![](../images/chapter7_2.png)

































































