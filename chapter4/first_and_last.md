# First and last

下图展示了如何从一个从可观测源序列中创建只发射第一个元素的序列。

![](../images/chapter4_8.png)

`first()`方法和`last()`方法很容易弄明白。它们从Observable中只发射第一个元素或者最后一个元素。这两个都可以传`Func1`作为参数，：一个可以确定我们感兴趣的第一个或者最后一个的谓词：

下图展示了`last()`应用在一个完成的序列上来创建一个仅仅发射最后一个元素的新的Observable。

![](../images/chapter4_9.png)

与`first()`和`last()`相似的变量有：`firstOrDefault()`和`lastOrDefault()`.这两个函数当可观测序列完成时不再发射任何值时用得上。在这种场景下，如果Observable不再发射任何值时我们可以指定发射一个默认的值