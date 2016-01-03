# 处理耗时的任务

我们已经知道如何处理缓慢的I/O操作。让我们看一个与I/O无关的耗时的任务。例如，我们修改`loadList()`函数并创建一个新的`slow`函数发射我们已安装的app数据。

```java
private Observable<AppInfo> getObservableApps(List<AppInfo> apps) {
    return Observable .create(subscriber -> {
        for (double i = 0; i < 1000000000; i++) {
            double y = i * i;
        }
        for (AppInfo app : apps) {
            subscriber.onNext(app);
        }
        subscriber.onCompleted(); 
    });
}
```

正如你看到的，这个函数执行了一些毫无意义的计算，只是针对这个例子消耗时间，然后从`List<AppInfo>`对象中发射我们的`AppInfo`数据，现在，我们重排`loadList()`函数如下：

```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    getObservableApps(apps)
        .subscribe(new Observer<AppInfo>() {
            @Override
            public void onCompleted() {
                mSwipeRefreshLayout.setRefreshing(false);
                Toast.makeText(getActivity(), "Here is the list!", Toast.LENGTH_LONG).show();
            }
            
            @Override
            public void onError(Throwable e) {
                Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show(); mSwipeRefreshLayout.setRefreshing(false);
}
@Override
public void onNext(AppInfo appInfo) { mAddedApps.add(appInfo); mAdapter.addApplication(mAddedApps.size() - 1, appInfo);
} });
}
```


























