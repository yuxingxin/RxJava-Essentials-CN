# 过滤序列

RxJava让我们使用`filter()`方法来过滤我们观测序列中不想要的值，在上一章中，我们在几个例子中使用了安装的应用列表，但是我们只想展示以字母`C`开头的已安装的应用该怎么办呢？在这个新的例子中，我们将使用同样的列表，但是我们会过滤它，把合适的谓词传给`filter()`函数来得到我们想要的值。

上一章中`loadList()`函数可以改成这样：
```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    Observable.from(apps)
            .filter((appInfo) ->
            appInfo.getName().startsWith("C"))
            .subscribe(new Observable<AppInfo>() {

                @Override
                public void onCompleted() {
                    mSwipeRefreshLayout.setRefreshing(false);
                }

                @Override
                public void onError(Throwable e) {
                    Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
                    mSwipeRefreshLayout.setRefreshing(false);
                }

                @Override
                public void onNext(AppInfo appInfo) {
                    mAddedApps.add(appInfo); 
                    mAdapter.addApplication(mAddedApps.size() - 1,appInfo);
                }
            });
}
```
我们从上一章中的`loadList()`函数中添加下面一行：
```java
.fliter((appInfo -> appInfo.getName().startsWith("C"))
```
创建Observable完以后，我们从发出的每个元素中过滤掉开头字母不是C的。为了让这里更清楚一些，我们用Java 7的语法来实现：
```java
.filter(new Func1<AppInfo,Boolean>(){
    @Override
    public Boolean call(AppInfo appInfo){
        return appInfo.getName().startsWith("C");
    }
})
```

我们传一个新的`Func1`对象给`filter()`函数，




























