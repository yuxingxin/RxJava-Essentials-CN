# Schedulers

调度器以一种最简单的方式将多线程用在你的Apps的中。它们时RxJava重要的一部分并能很好地与Observables协同工作。它们无需处理实现、同步、线程、平台限制、平台变化而可以提供一种灵活的方式来创建并发程序。

RxJava提供了5种调度器：

* `.io()`
* `.computation()`
* `.immediate()`
* `.newThread()`
* `.trampoline()`

让我们一个一个的来看下它们：

## Schedulers.io()

这个调度器时用于I/O操作。它基于根据需要来自适应的线程池。