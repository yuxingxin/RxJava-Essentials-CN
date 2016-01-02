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
        appInfoType);
        apps = new Gson().fromJson(serializedApps, }
        for (AppInfo app : apps) { subscriber.onNext(app);
        }
        }
subscriber.onCompleted(); 
});
```