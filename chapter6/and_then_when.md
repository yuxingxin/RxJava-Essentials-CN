# And,Then和When

在将来还有一些`zip()`满足不了的场景。如复杂的架构，或者是仅仅为了个人爱好，你可以使用And/Then/When解决方案。它们在RxJava的joins包下，使用Pattern和Plan作为中介，将发射的数据集合并到一起。

![](../images/chapter6_11.png)

我们的`loadList()`函数将会被修改从这样：
```java
private void loadList(List<AppInfo> apps) {

    mRecyclerView.setVisibility(View.VISIBLE);

    Observable<AppInfo> observableApp = Observable.from(apps);
    
    Observable<Long> tictoc = Observable.interval(1, TimeUnit.SECONDS);
    
    Pattern2<AppInfo, Long> pattern = JoinObservable.from(observableApp).and(tictoc); 
    
    Plan0<AppInfo> plan = pattern.then(this::updateTitle);
    
    JoinObservable
        .when(plan)
        .toObservable()
        .observeOn(AndroidSchedulers.mainThread())
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
                int position = mAddedApps.size() - 1;
                mAdapter.addApplication(position, appInfo); mRecyclerView.smoothScrollToPosition(position);
            } 
        });
}
```
和通常一样，我们有两个发射的序列，`observableApp`，发射我们安装的应用列表数据，`tictoc`每秒发射一个`Long`型整数。现在我们用`and()`连接源Observable和第二个Observable。

```java
JoinObservable.from(observableApp).and(tictoc);
```
这里创建一个`pattern`对象，使用这个对象我们可以创建一个`Plan`对象:"我们有两个发射数据的Observables,`then()`是做什么的？"
```java
pattern.then(this::updateTitle);
```
现在我们有了一个`Plan`对象并且当plan发生时我们可以决定接下来发生的事情。
```java
.when(plan).toObservable()
```
这时候，我们可以订阅新的Observable，正如我们总是做的那样。

























