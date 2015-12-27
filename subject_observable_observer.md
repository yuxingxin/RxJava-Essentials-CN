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

在刚才的例子中，我们创建了一个`PublishSubject`，用`create()`方法发射一个`String`值，然后我们订阅了PublishSubject。此时，没有数据要发送，因此我们的观察者只能等待，没有阻塞线程，也没有消耗资源。就在这准备从主题接收值，如果主题没有发射值那么我们的观察者就会一直在等待。再次声明的是，无需担心：观察者知道在每个场景中该做什么，什么时候我们也不用担心是因为它时响应式的：系统会响应。我们并不关心它什么时候响应。我们只关心它响应时该做什么。