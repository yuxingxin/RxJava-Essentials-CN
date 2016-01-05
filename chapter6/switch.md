# Switch

有这样一个复杂的场景就是在一个`subscribe-unsubscribe`的序列里我们能够从一个Observable自动取消订阅来订阅一个新的Observable。

RxJava的`switch()`，正如定义的，将一个发射多个Observables的Observable转换成另一个单独的Observable，后者发射那些Observables最近发射的数据项。

给出一个发射多个Observables序列的源Observable，`switch()`订阅到源Observable然后开始发射由第一个发射的Observable发射的一样的数据。当源Observable发射一个新的Observable时，`switch()`立即取消订阅前一个发射数据的Observable（因此打断了从它那里发射的数据流）然后订阅一个新的Observable，并开始发射它的数据。

![](../images/chapter6_12.png)

