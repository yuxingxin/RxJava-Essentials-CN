# Merge

在”异步的世界“中经常会创建这样的场景，我们有多个来源但是又只想有一个结果：多输入，单输出。RxJava的`merge()`方法将帮助你把两个甚至更多的Observables合并到他们发射的数据项里。下图给出了把两个序列合并在一个最终发射的Observable。

![](../images/chapter6_1.png)

正如你看到的那样，发射的数据被交叉合并到一个Observable里面。注意如果你同步的合并Observable，它们将连接在一起并且不会交叉。

像往常一样，我们用我们的App和已安装的App列表来创建了一个“真实世界”的例子。为此我们还需要第二个Observable。我们可以创建一个单独的应用列表然后让它逆序排列。当然这没有实际的意义，只是为了这个例子。对于第二个列表，我们的`loadList()`函数像下面这样：
```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    List reversedApps = Lists.reverse(apps);
    Observable<AppInfo> observableApps =Observable.from(apps);
    Observable<AppInfo> observableReversedApps =Observable.from(reversedApps);
    Observable<AppInfo> mergedObserbable = Observable.merge(observableApps,observableReversedApps);
    
    mergedObserbable.subscribe(new Observer<AppInfo>(){
        @Override
        public void onCompleted() {
            mSwipeRefreshLayout.setRefreshing(false);
            Toast.makeText(getActivity(), "Here is the list!", Toast.LENGTH_LONG).show();
        }
        
        @Override
        public void onError(Throwable e) {
            Toast.makeText(getActivity(), "One of the two Observable threw an error!", Toast.LENGTH_SHORT).show();
            mSwipeRefreshLayout.setRefreshing(false);
        }
        
        @Override
        public void onNext(AppInfoappInfo) {
            mAddedApps.add(appInfo);
            mAdapter.addApplication(mAddedApps.size() - 1, appInfo);
        } 
    });
}
```

我们创建了Observable和observableApps数据项以及新的observableReversedApps逆序列表。使用`Observable.merge()`，我们可以创建新的`Observable MergedObservable`，它在单个可观测序列中发射源Observables发出的所有数据。

正如你能看到的,每个方法签名都是一样的，因此我们的观察者无需在意任何不同就可以复用代码。结果如下：
![](../images/chapter6_2.png)

注意错误时的toast消息，你可以认为每个Observable抛出的错误都将会打断合并。如果你需要避免这种情况，RxJava提供了`mergeDelayError()`，它能从一个Observable中继续发射数据即便是其中有一个抛出了错误。当所有的Observables都完成时，`mergeDelayError()`将会发射`onError()`，如下图所示：

![](../images/chapter6_3.png)
























