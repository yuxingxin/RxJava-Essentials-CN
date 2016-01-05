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

这个调度器时用于I/O操作。它基于根据需要，增长或缩减来自适应的线程池。我们将使用它来修复我们之前看到的`StrictMode`违规做法。由于它专用于I/O操作，所以并不是RxJava的默认方法；正确的使用它是由开发者决定的。

重点需要注意的是线程池是无限制的，大量的I/O调度操作将创建许多个线程并占用内存。一如既往的是，我们需要在性能和简捷两者之间找到一个有效的平衡点。

## Schedulers.computation()

这个是计算工作默认的调度器，它与I/O操作无关。它也是许多RxJava方法的默认调度器：`buffer()`,`debounce()`,`delay()`,`interval()`,`sample()`,`skip()`。

## Schedulers.immediate()

这个调度器允许你立即在当前线程执行你指定的工作。它是`timeout()`,`timeInterval()`,以及`timestamp()`方法默认的调度器。

## Schedulers.newThread()

这个调度器正如它所看起来的那样：它为指定任务启动一个新的线程。

## Schedulers.trampoline()

当我们想在当前线程执行一个任务时，并不是立即，我们可以用`.trampoline()`将它入队。这个调度器将会处理它的队列并且按序运行队列中每一个任务。它是`repeat()`和`retry()`方法默认的调度器。



































