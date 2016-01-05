# Observable

当我们异步执行一些复杂的事情，Java提供了传统的类，例如Thread、Future、FutureTask、CompletableFuture来处理这些问题。当复杂度提升，这些方案就会变得麻烦和难以维护。最糟糕的是，它们都不支持链式调用。

RxJava Observables被设计用来解决这些问题。它们灵活，且易于使用，也可以链式调用，并且可以作用于单个结果程序上，更有甚者，也可以作用于序列上。无论何时你想发射单个标量值，或者一连串值，甚至是无穷个数值流，你都可以使用Observable。

Observable的生命周期包含了三种可能的易于与Iterable生命周期事件相比较的事件，下表展示了如何将Observable async/push 与 Iterable sync/pull相关联起来。

| Event| Iterable(pull)|Observable(push)|
| ------------- |:-------------:| -----:|
| 检索数据|`T next()`| `onNext(T)` |
| 发现错误| `throws Exception`|`onError(Throwable)`|
| 完成    |`!hasNext()`|`onCompleted()`|

使用Iterable时，消费者从生产者那里以同步的方式得到值，在这些值得到之前线程处于阻塞状态。相反，使用Observable时，生产者以异步的方式把值推给观察者，无论何时，这些值都是可用的。这种方法之所以更灵活是因为即便值是同步或异步方式到达，消费者在这两种场景都可以根据自己的需要来处理。

为了更好地复用Iterable接口，RxJava Observable类扩展了GOF观察者模式的语义。引入了两个新的接口：
* onCompleted() 即通知观察者Observable没有更多的数据。
* onError() 即观察者有错误出现了。


### 热Observables和冷Observables

从发射物的角度来看，有两种不同的Observables:热的和冷的。一个"热"的Observable典型的只要一创建完就开始发射数据，因此所有后续订阅它的观察者可能从序列中间的某个位置开始接受数据（有一些数据错过了）。一个"冷"的Observable会一直等待，直到有观察者订阅它才开始发射数据，因此这个观察者可以确保会收到整个数据序列。

### 创建一个Observable

在接下来的小节中将讨论Observables提供的两种创建Observable的方法。

#### Observable.create()

create()方法使开发者有能力从头开始创建一个Observable。它需要一个OnSubscribe对象,这个对象继承Action1,当观察者订阅我们的Observable时，它作为一个参数传入并执行call()函数。
```java
Observable.create(new Observable.OnSubscribe<Object>(){
        @Override
        public void call(Subscriber<? super Object> subscriber) {
            
        }
});
```
Observable通过使用subscriber变量并根据条件调用它的方法来和观察者通信。让我们看一个“现实世界”的例子：
```java
Observable<Integer> observableString = Observable.create(new Observable.OnSubscribe<Integer>() {
        @Override
        public void call(Subscriber<? super Integer> observer) {
            for (int i = 0; i < 5; i++) {
                observer.onNext(i);
            }
            observer.onCompleted();
        }
});

Subscription subscriptionPrint = observableString.subscribe(new Observer<Integer>() {
    @Override
    public void onCompleted() {
        System.out.println("Observable completed");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Oh,no! Something wrong happened！");
    }

    @Override
    public void onNext(Integer item) {
        System.out.println("Item is " + item);
    }
});
```
例子故意写的简单，是因为即便是你第一次见到RxJava的操作，我想让你明白接下来要发生什么。

我们创建一个新的`Observable<Integer>`,它执行了5个元素的for循环，一个接一个的发射他们，最后完成。

另一方面，我们订阅了Observable，返回一个Subscription
。一旦我们订阅了，我们就开始接受整数，并一个接一个的打印出它们。我们并不知道要接受多少整数。事实上，我们也无需知道是因为我们为每种场景都提供对应的处理操作：
* 如果我们接收到了整数，那么就打印它。
* 如果序列结束，我们就打印一个关闭的序列信息。
* 如果错误发生了，我们就打印一个错误信息。

#### Observable.from()

在上一个例子中，我们创建了一个整数序列并一个一个的发射它们。假如我们已经有一个列表呢？我们是不是可以不用for循环而也可以一个接一个的发射它们呢？

在下面的例子代码中，我们从一个已有的列表中创建一个Observable序列：
```java
List<Integer> items = new ArrayList<Integer>();
items.add(1);
items.add(10);
items.add(100);
items.add(200);

Observable<Integer> observableString = Observable.from(items);
Subscription subscriptionPrint = observableString.subscribe(new Observer<Integer>() {
    @Override
    public void onCompleted() {
        System.out.println("Observable completed");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Oh,no! Something wrong happened！");
    }

    @Override
    public void onNext(Integer item) {
        System.out.println("Item is " + item);
    }
});
```

输出的结果和上面的例子绝对是一样的。

`from()`创建符可以从一个列表/数组来创建Observable,并一个接一个的从列表/数组中发射出来每一个对象，或者也可以从Java `Future`类来创建Observable，并发射Future对象的`.get()`方法返回的结果值。传入`Future`作为参数时，我们可以指定一个超时的值。Observable将等待来自`Future`的结果；如果在超时之前仍然没有结果返回，Observable将会触发`onError()`方法通知观察者有错误发生了。

#### Observable.just()

如果我们已经有了一个传统的Java函数，我们想把它转变为一个Observable又改怎么办呢？我们可以用`create()`方法，正如我们先前看到的，或者我们也可以像下面那样使用以此来省去许多模板代码：
```java
Observable<String> observableString = Observable.just(helloWorld());

Subscription subscriptionPrint = observableString.subscribe(new Observer<String>() {
    @Override
    public void onCompleted() {
        System.out.println("Observable completed");
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("Oh,no! Something wrong happened!");
    }

    @Override
    public void onNext(String message) {
        System.out.println(message);
    }
});
```

`helloWorld()`方法比较简单，像这样：
```java
private String helloWorld(){
    return "Hello World";
}
```

不管怎样，它可以是我们想要的任何函数。在刚才的例子中，我们一旦创建了Observable，`just()`执行函数，当我们订阅Observable时，它就会发射出返回的值。

`just()`方法可以传入一到九个参数，它们会按照传入的参数的顺序来发射它们。`just()`方法也可以接受列表或数组，就像`from()`方法，但是它不会迭代列表发射每个值,它将会发射整个列表。通常，当我们想发射一组已经定义好的值时会用到它。但是如果我们的函数不是时变性的，我们可以用just来创建一个更有组织性和可测性的代码库。

最后注意`just()`创建符，它发射出值后，Observable正常结束，在上面那个例子中，我们会在控制台打印出两条信息：“Hello World”和“Observable completed”。

#### Observable.empty(),Observable.never(),和Observable.throw()

当我们需要一个Observable毫无理由的不再发射数据正常结束时，我们可以使用`empty()`。我们可以使用`never()`创建一个不发射数据并且也永远不会结束的Observable。我们也可以使用`throw()`创建一个不发射数据并且以错误结束的Observable。


