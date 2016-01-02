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

getApps() .subscribeOn(Schedulers.io())
.subscribe(new Observer<AppInfo>() { [...]
```
`Schedulers.io()`

































































