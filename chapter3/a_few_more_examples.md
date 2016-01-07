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
            .subscribe(new Observer<AppInfo>() {

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

你可以将一个函数作为参数传给`just()`方法，你将会得到一个已存在代码的原始Observable版本。在一个新的响应式架构的基础上迁移已存在的代码，这个方法可能是一个有用的开始点。


## repeat()

假如你想对一个Observable重复发射三次数据。例如，我们用`just()`例子中的Observable：

```java
private void loadApps(AppInfo appOne,AppInfo appTwo,AppInfo appThree) {
    mRecyclerView.setVisibility(View.VISIBLE);
    Observable.just(appOne,appTwo,appThree)
            .repeat(3)
            .subscribe(new Observer<AppInfo>() {

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
正如你看到的，我们在`just()`创建Observable后追加了`repeat(3)`，它将会创建9个元素的序列，每一个都单独发射。

## defer()

有这样一个场景，你想在这声明一个Observable但是你又想推迟这个Observable的创建直到观察者订阅时。看下面的`getInt()`函数：
```java
private Observable<Integer> getInt(){
    return Observable.create(subscriber -> {
        if(subscriber.isUnsubscribed()){
            return;
        }
        App.L.debug("GETINT");
        subscriber.onNext(42);
        subscriber.onCompleted();
    });
}
```

这比较简单，并且它没有做太多事情，但是它正好为我们服务。现在，我们可以创建一个新的Observable并且应用`defer()`:

```java
Observable<Integer> deferred = Observable.defer(this::getInt);
```
这次，`deferred`存在，但是`getInt()` `create()`方法还没有调用:logcat日志也没有“GETINT”打印出来:

```java
deferred.subscribe(number -> {
    App.L.debug(String.valueOf(number));
});
```
但是一旦我们订阅了，`create()`方法就会被调用并且我们也可以在logcat日志中得到下卖弄两个：GETINT和42。

## range()

你需要从一个指定的数字X开始发射N个数字吗？你可以用`range`:
```java
Observable.range(10,3)
    .subscribe(new Observer<Integer>() {

        @Override
        public void onCompleted() {
            Toast.makeText(getActivity(), "Yeaaah!", Toast.LENGTH_LONG).show();
        }

        @Override
        public void onError(Throwable e) {
            Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onNext(Integer number) {
            Toast.makeText(getActivity(), "I say " + number, Toast.LENGTH_SHORT).show();
        }
    });
```

`range()`函数用两个数字作为参数：第一个是起始点，第二个是我们想发射数字的个数。


## interval()

`interval()`函数在你需要创建一个轮询程序时非常好用。
```java
Subscription stopMePlease = Observable.interval(3,TimeUnit.SECONDS)
    .subscribe(new Observer<Integer>() {

        @Override
        public void onCompleted() {
            Toast.makeText(getActivity(), "Yeaaah!", Toast.LENGTH_LONG).show();
        }

        @Override
        public void onError(Throwable e) {
            Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onNext(Integer number) {
            Toast.makeText(getActivity(), "I say " + number, Toast.LENGTH_SHORT).show();
        }
    });
```
`interval()`函数的两个参数：一个指定两次发射的时间间隔，另一个是用到的时间单位。


## timer()

如果你需要一个一段时间之后才发射的Observable，你可以像下面的例子使用`timer()`：

```java
Observable.timer(3,TimeUnit.SECONDS)
    .subscribe(new Observer<Long>() {

        @Override
        public void onCompleted() {
            
        }

        @Override
        public void onError(Throwable e) {
            
        }

        @Override
        public void onNext(Long number) {
            Log.d("RXJAVA", "I say " + number);
        }
    });
```
它将3秒后发射0,然后就完成了。让我们使用`timer()`的第三个参数，就像下面的例子：
```java
Observable.timer(3,3,TimeUnit.SECONDS)
    .subscribe(new Observer<Long>() {

        @Override
        public void onCompleted() {
            
        }

        @Override
        public void onError(Throwable e) {
            
        }

        @Override
        public void onNext(Long number) {
            Log.d("RXJAVA", "I say " + number);
        }
    });
```
用这个代码，你可以创建一个以初始值来延迟（上一个例子是3秒）执行的`interval()`版本，然后每隔N秒就发射一个新的数字（前面的例子是3秒）。

















