# Merge

在异步的世界经常会创建这样的场景，我们有多个来源但是只想有一个结果：多输入，单输出。RxJava的`merge()`方法将帮助你把两个甚至更多的Observables合并到他们发射的数据里。下图给出了把两个序列合并在一个最终发射的Observable。

![](chapter6_1.png)