# Observable

当我们异步执行一些复杂的事情，Java提供了经典的类，例如Thread、Future、FutureTask、CompletableFuture来解决这些问题。当复杂度提升，这些方案就会变得麻烦和难以维护。它们的大多数都不支持链式调用。

RxJava Observables被设计用来解决这些问题。它们灵活，易使用，也可以链式调用，并且可以对单个结果程序起作用，更有甚者，也可以对序列起作用。无论何时你想发射单个标量值，或者一连串值，甚至时无限的数值流，你都可以使用Observable。

Observable的生命周期包含了三种可能的易于Iterable生命周期事件相比较的事件，下表展示了如何将Observable async/push 与 Iterable sync/pull相关联起来。

| Event| Iterable(pull)|Observable(push)|
| ------------- |:-------------:| -----:|
| 检索数据|`T next()`| `onNext(T)` |
| 发现错误| `throws Exception`|`onError(Throwable)`|
| 完成    |`!hasNext()`|`onCompleted()`|

