# *map家族

RxJava提供了几个mapping函数：`map()`,`flatMap()`,`concatMap()`,`flatMapIterable()`以及`switchMap()`.所有这些函数都应用一个可观测序列，变换它发射的值，用一种新的形式返回他们。让我们用合适的真实世界的例子一个个的看下它们。

## Map

RxJava的`map`函数接收一个指定的`Func`对象然后将它应用到每一个由Observable发射的值上。下图展示了如何将一个乘法函数应用到每个发出的值上以此创建一个新的Observable来发射转换的数据。

![](chapter5_1.png)

考虑我们已安装的应用列表。我们怎么才能够显示同样的列表，但是所有的名字都是小写。