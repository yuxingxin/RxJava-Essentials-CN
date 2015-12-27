# Subject = Observable + Observer

`subject`是一个神奇的对象，它可以是一个Observable同时也可以是一个Observer：它作为连接这两个世界的一座桥梁。一个主题可以订阅一个Observable，就像一个观察者，并且它可以发射新的数据，或者传递它接受到的数据，就像一个Observable。很明显，作为一个Observable，Observers或者主题都可以订阅它。

一旦主题订阅了Observable，它将会触发Observable开始发射。如果原始的Observable是冷的，这将会对订阅一个热的Observable变量产生影响。

RxJava提供四种不同的主题：
* PublishSubject
* BehaviorSubject
* ReplaySubject.
* AsyncSubject

### PublishSubject

Publish是一个Subject基类。让我们看看用PublishSubject实现传统的Observable Hello World:
```java
PublishSubject<String> stringPublishSubject = PublishSubject.create();
Subscription subscriptionPrint = stringPublishSubject.subscribe(new Observer<String>() {
    @Override
    public void onCompleted() {
        System.out.println("Observable completed");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Oh,no!Something wrong happened!");                
    }

    @Override
    public void onNext(String message) {
        System.out.println(message);
    }
});
stringPublishSubject.onNext("Hello World");
```

