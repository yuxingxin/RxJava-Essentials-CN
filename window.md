# Window

RxJava的`window()`函数和`buffer()`很像，但是它发射的时Observable而不是列表。下图展示了`window()`如何缓存3个数据项并把它们作为一个新的Observable发射出去。

![](chapter5_13.png)