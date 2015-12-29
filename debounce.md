# Debounce

`debounce()`函数过滤掉由Observable发射的速率过快的数据；如果在一个指定的时间间隔过去了仍旧没有发射一个，那么它将发射最后的那个。

就像`sample()`和`timeout()`函数一样，`debounce()`使用`TimeUnit`对象指定时间间隔。

