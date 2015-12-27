# Observable

当我们异步执行一些复杂的事情，Java提供了经典的类，例如Thread、Future、FutureTask、CompletableFuture来解决这些问题。当复杂度提升，这些方案就会变得麻烦和难以维护。它们的大多数都不支持链式调用。

RxJava Observables被设计用来解决这些问题。它们灵活，易使用，也可以链式调用，并且可以对单个结果程序起作用，更有甚者，也可以对序列起作用。无论何时你想发射单个标量值，或者一连串值，甚至时无限的数值流，你都可以使用Observable。

Observable的生命周期包含了三种可能的易于Iterable生命周期事件相比较的事件，下表展示了如何将Observable async/push 与 Iterable sync/pull相关联起来。

| Event| Iterable(pull)|Observable(push)|
| ------------- |:-------------:| -----:|
| 检索数据|`T next()`| `onNext(T)` |
| 发现错误| `throws Exception`|`onError(Throwable)`|
| 完成    |`!hasNext()`|`onCompleted()`|

使用Iterable时，消费者从生产者那里以同步的方式得到值，在这些值得到之前线程处于阻塞状态。相反，使用Observable时，生产者以异步的方式把值推给观察者，无论何时，这些值都是可用的。这种方法之所以更灵活是因为即便值是同步或异步方式到达，消费者在这两种场景都可以根据自己的需要来处理。

为了更好地复用Iterable接口，RxJava Observable类扩展了GOF观察者模式的语义。引入了两个新的接口：
* onCompleted() 即通知观察者Observable没有更多的数据。
* onError() 即观察者有一个错误出现了。


### 热Observables和冷Observables

从发射物的角度来看，有两种不同的Observables:热的和冷的。一个"热"的Observable典型的只要一创建完就开始发射数据，因此所有后续订阅它的观察者可能从序列中间的某个位置开始接受数据（有一些数据错过了）。一个"冷"的Observable会一直等待，直到有观察者订阅它才开始发射数据，因此这个观察者可以确保会收到整个数据序列。

### 创建一个Observable

在接下来的小节中将讨论Observables提供的两种创建Observable的方法。

#### Observable.create()

create()方法使开发者有能力从零开始创建一个Observable。它需要一个OnSubscribe对象,这个对象继承Action1,当观察者订阅我们的Observable时，它作为一个参数传入并执行call()函数。

