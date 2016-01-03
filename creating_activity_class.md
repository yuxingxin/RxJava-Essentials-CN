# 创建Activity类

我们将在`onCreate()`方法里创建`SwipeRefreshLayout`和`RecyclerView`；我们有一个`refreshList()`方法来处理用户列表的获取和展示，`showRefreshing()`方法来管理进度条和`RecyclerView`的显示。

我们的`refreshList()`函数看起来如下：
```java
private void refreshList() { 
    showRefresh(true);
    mSeApiManager.getMostPopularSOusers(10)
        .subscribe(users -> { 
            showRefresh(false);
            mAdapter.updateUsers(users);
        }, error -> { 
            App.L.error(error.toString());
            showRefresh(false);
        });
}
```
我们显示了进度条，从StackExchange API 管理器观测用户列表。一旦获取到列表数据，我们开始展示它并更新`Adapter`的内容并让`RecyclerView`显示为可见。

























