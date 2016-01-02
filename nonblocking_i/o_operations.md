# 非阻塞I/O操作

现在我们知道如何在一个指定I/O调度器上来调度一个任务，我们可以修改`storeBitmap()`函数并再次检查`StrictMode`的违规做法。为了这个例子，我们可以在新的`blockingStoreBitmap()`函数中重排代码。

