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

为了更好地复用Iterable接口，RxJava Observable类扩展了“四人组”的观察者模式的语义。

