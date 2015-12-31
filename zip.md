# ZIP

我们在处理多源时可能会带来这样一种场景：多从个Observables接收数据，处理它们，然后将它们合并成一个新的可观测序列来使用。RxJava有一个特殊的方法可以完成：`zip()`合并两个或者多个Observables发射出的数据项，根据指定的函数`Func*`变换它们，并发射一个新值。下图展示了`zip()`方法如何处理发射的“numbers”和“letters”然后将它们合并一个新的数据项：

![](chapter6_4.png)

对于“真实世界”的例子来说，我们将使用已安装的应用列表和一个新的动态的Observable来让例子变得有点有趣味。

```java
Observable<Long> tictoc = Observable.interval(1, TimeUnit.SECONDS);
```
`tictoc`Observable变量使用`interval()`函数每秒生成一个Long类型的数据：简单且高效，正如之前所说的，我们需要一个`Func`对象。因为它需要传两个参数，所以是`Func2`:

```java
private AppInfo updateTitle(AppInfoappInfo, Long time) {
    appInfo.setName(time + " " + appInfo.getName());
    return appInfo;
}
```
现在我们的`loadList()`函数变成这样：
```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    Observable<AppInfo> observableApp = Observable.from(apps);
    
    Observable<Long> tictoc = Observable.interval(1, TimeUnit.SECONDS);
    
    Observable.zip(observableApp, tictoc,(AppInfo appInfo, Long time) -> updateTitle(appInfo, time)) .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Observer<AppInfo>() {
        @Override
        public void onCompleted() {
            Toast.makeText(getActivity(), "Here is the list!", Toast.LENGTH_LONG).show();
        }
        
        @Override
        public void onError(Throwable e) {
            mSwipeRefreshLayout.setRefreshing(false);
            Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
        }
        
        @Override
        public void onNext(AppInfoappInfo) {
            if (mSwipeRefreshLayout.isRefreshing()) {
                mSwipeRefreshLayout.setRefreshing(false);
            } 
            mAddedApps.add(appInfo);
            intposition = mAddedApps.size() - 1; mAdapter.addApplication(position, appInfo); mRecyclerView.smoothScrollToPosition(position);
        }
        } });
}
```

