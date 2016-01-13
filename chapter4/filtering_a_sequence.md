# 过滤序列

RxJava让我们使用`filter()`方法来过滤我们观测序列中不想要的值，在上一章中，我们在几个例子中使用了已安装的应用列表，但是我们只想展示以字母`C`开头的已安装的应用该怎么办呢？在这个新的例子中，我们将使用同样的列表，但是我们会过滤它，通过把合适的谓词传给`filter()`函数来得到我们想要的值。

上一章中`loadList()`函数可以改成这样：
```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    Observable.from(apps)
            .filter((appInfo) ->
            appInfo.getName().startsWith("C"))
            .subscribe(new Observer<AppInfo>() {

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

我们传一个新的`Func1`对象给`filter()`函数，即只有一个参数的函数。`Func1`有一个`AppInfo`对象来作为它的参数类型并且返回`Boolean`对象。只要条件符合`filter()`函数就会返回`true`。此时，值会发射出去并且所有的观察者都会接收到。

正如你想的那样，从一个我们得到的可观测序列中创建一个我们需要的序列`filter()`是很好用的。我们不需要知道可观测序列的源或者为什么发射这么多不同的数据。我们只是想要这些元素的子集来创建一个可以在应用中使用的新序列。这种思想促进了我们编码中的分离性与抽象性。

`filter()`函数最常用的用法之一时过滤`null`对象：
```java
.filter(new Func1<AppInfo,Boolean>(){
    @Override
    public Boolean call(AppInfo appInfo){
        return appInfo != null;
    }
})
```
这看起来简单，对于简单的事情有许多模板代码，但是它帮我们免去了在`onNext()`函数调用中再去检测`null`值，让我们把注意力集中在应用业务逻辑上。

下图展示了过滤出的C字母开头的已安装的应用列表。

![](../images/chapter4_1.png)




























