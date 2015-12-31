# StartWith

我们已经学到如何连接多个Observables并追加指定的值到一个发射序列里。RxJava的`startWith()`是`concat()`的对应部分。当`concat()`向发射数据的Observable追加数据时， `startWith()`传递一个参数来发射一个数据序列，    