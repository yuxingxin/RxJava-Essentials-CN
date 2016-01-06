# StartWith

我们已经学到如何连接多个Observables并追加指定的值到一个发射序列里。RxJava的`startWith()`是`concat()`的对应部分。正如`concat()`向发射数据的Observable追加数据那样，在Observable开始发射他们的数据之前， `startWith()`通过传递一个参数来先发射一个数据序列。  

![](../images/chapter6_13.png)