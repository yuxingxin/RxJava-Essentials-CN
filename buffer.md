# Buffer

RxJava中的`buffer()`函数将源Observable变换一个新的Observable，这个新的Observable每次发射一组列表值而不是一个一个发射。

![](chapter5_10.png)

上图中展示了`buffer()`如何将`count`作为一个参数来指定有多少数据项包含在发射的列表中。