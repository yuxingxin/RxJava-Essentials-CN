# Join

前面两个方法，`zip()`和`merge()`方法作用在发射数据的范畴内，在决定如何操作值之前有些场景我们需要考虑时间的。RxJava的`join()`函数基于时间窗口将两个Observables发射的数据结合在一起。

![](chapter6_6.png)