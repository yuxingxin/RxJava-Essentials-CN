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