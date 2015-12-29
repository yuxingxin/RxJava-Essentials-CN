# Skip and SkipLast

下图中展示了如何使用`skip(2)`来创建一个不发射前两个元素而是发射它后面的那些数据的序列。

![](chapter4_10.png)

`skip()`和`skipLast()`函数与`take()`和`takeLast()`相对应。它们用整数N作参数，从根本上来说，它们不让Observable发射前N个或者后N个值。