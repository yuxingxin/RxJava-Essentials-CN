# Subject = Observable + Observer

`subject`是一个神奇的对象，它可以是一个Observable同时也可以是一个Observer：它作为连接这两个世界的一座桥梁。一个Subject可以订阅一个Observable，就像一个观察者，并且它可以发射新的数据，或者传递它接受到的数据，就像一个Observable。很明显，作为一个Observable，观察者们或者其它Subject都可以订阅它。

一旦Subject订阅了Observable，它将会触发Observable开始发射。如果原始的Observable是“冷”的，这将会对订阅一个“热”的Observable变量产生影响。

RxJava提供四种不同的Subject：
* PublishSubject
* BehaviorSubject
* ReplaySubject.
* AsyncSubject

### PublishSubject

Publish是Subject的一个基础子类。让我们看看用PublishSubject实现传统的Observable `Hello World`:
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

在刚才的例子中，我们创建了一个`PublishSubject`，用`create()`方法发射一个`String`值，然后我们订阅了PublishSubject。此时，没有数据要发送，因此我们的观察者只能等待，没有阻塞线程，也没有消耗资源。就在这随时准备从subject接收值，如果subject没有发射值那么我们的观察者就会一直在等待。再次声明的是，无需担心：观察者知道在每个场景中该做什么，我们不用担心什么时候是因为它是响应式的：系统会响应。我们并不关心它什么时候响应。我们只关心它响应时该做什么。

最后一行代码展示了手动发射字符串“Hello World”,它触发了观察者的`onNext()`方法，让我们在控制台打印出“Hello World”信息。

让我们看一个更复杂的例子。话说我们有一个`private`声明的Observable，外部不能访问。Observable在它生命周期内发射值，我们不用关心这些值，我们只关心他们的结束。

首先，我们创建一个新的PublishSubject来响应它的`onNext()`方法，并且外部也可以访问它。

```java
final PublishSubject<Boolean> subject = PublishSubject.create();
        
subject.subscribe(new Observer<Boolean>() {
    @Override
    public void onCompleted() {
        
    }

    @Override
    public void onError(Throwable e) {

    }

    @Override
    public void onNext(Boolean aBoolean) {
        System.out.println("Observable Completed");
    }
});
```
然后，我们创建“私有”的Observable，只有subject才可以访问的到。
```java
Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override
    public void call(Subscriber<? super Integer> subscriber) {
        for (int i = 0; i < 5; i++) {
            subscriber.onNext(i);
        }
        subscriber.onCompleted();
    }
}).doOnCompleted(new Action0() {
    @Override
    public void call() {
        subject.onNext(true);
    }
}).subscribe();
```
`Observable.create()`方法包含了我们熟悉的for循环，发射数字。`doOnCompleted()`方法指定当Observable结束时要做什么事情：在subject上发射true。最后，我们订阅了Observable。很明显，空的`subscribe()`调用仅仅是为了开启Observable，而不用管已发出的任何值，也不用管完成事件或者错误事件。为了这个例子我们需要它像这样。

在这个例子中，我们创建了一个可以连接Observables并且同时可被观测的实体。当我们想为公共资源创建独立、抽象或更易观测的点时，这是极其有用的。

### BehaviorSubject

简单的说，BehaviorSubject会首先向他的订阅者发送截至订阅前最新的一个数据对象（或初始值）,然后正常发送订阅后的数据流。

```java
BehaviorSubject<Integer> behaviorSubject = BehaviorSubject.create(1);
```
在这个短例子中，我们创建了一个能发射整形(Integer)的BehaviorSubject。由于每当Observes订阅它时就会发射最新的数据，所以它需要一个初始值。
### ReplaySubject

ReplaySubject会缓存它所订阅的所有数据,向任意一个订阅它的观察者重发:
```java
ReplaySubject<Integer> replaySubject = ReplaySubject.create();
```

### AsyncSubject

当Observable完成时AsyncSubject只会发布最后一个数据给已经订阅的每一个观察者。

```java
AsyncSubject<Integer> asyncSubject = AsyncSubject.create();
```