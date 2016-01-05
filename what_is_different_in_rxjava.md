# RxJava的与众不同之处

从纯Java的观点看，RxJava Observable类源自于经典的Gang Of Four的观察者模式。

它添加了三个缺少的功能：

* 生产者在没有更多数据可用时能够发出信号通知：onCompleted()事件。
* 生产者在发生错误时能够发出信号通知：onError()事件。
* RxJava Observables 能够组合而不是嵌套，从而避免开发者陷入回调地狱。


Observables和Iterables共用一个相似的API：我们在Iterable可以执行的许多操作也都同样可以在Observables上执行。当然，由于Observables流的本质，没有如Iterable.remove()这样相应的方法。

| Pattern| 一个返回值| 多个返回值  |
| ------------- |:-------------:| -----:|
| Synchronous|`T getData()`| `Iterable<T>` |
| Asynchronous| `Future<T> getData()`|`Observable<T> getData()`|

从语义的角度来看，RxJava就是.NET Rx。从语法的角度来看，Netflix考虑到了对应每个Rx方法,保留了Java代码规范和基本的模式。
