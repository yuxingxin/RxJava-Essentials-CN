# 再多几个例子

在这一节中，我们将基于RxJava的`just()`,`repeat()`,`defer()`,`range()`,`interval()`,和`timer()`方法展示一些例子。

## just()

假如我们只有3个独立的AppInfo对象并且我们想把他们转化为Observable并填充到RecyclerView的item中：
```java
List<AppInfo> apps = ApplicationsList.getInstance().getList();

AppInfo appOne = apps.get(0);

AppInfo appTwo = apps.get(10);

AppInfo appThree = apps.get(24);

loadApps(appOne,appTwo,appThree);

```

我们可以像我们之前的例子那样检索列表并提取出这三个元素。然后我们将他们传到这个`loadApps()`函数里面：
```java
private void loadApps(AppInfo appOne,AppInfo appTwo,AppInfo appThree) {
    mRecyclerView.setVisibility(View.VISIBLE);
    Observable.just(appOne,appTwo,appThree)
            .subscribe(new Observable<AppInfo>() {

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
                    mAdapter.addApplication(mAddedApps.size() - 1,appInfo);
                }
            });
}
```

正如你看到的，代码和之前的例子很像。这种方法让我们有机会来考虑一下代码的复用。

你可以将一个函数作为参数传给`just()`方法，





















