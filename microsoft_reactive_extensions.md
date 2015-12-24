# Microsoft Reactive Extensions

函数响应式编程是一个来自90年代后期受微软的一名计算机科学家Erik Meijer启发的思想，用来设计和开发微软的Rx库。

Rx 是微软.NET的一个响应式扩展。Rx借助可观测的序列提供一种简单的方式来创建异步的，基于事件驱动的程序。开发者可以使用Observables模拟异步数据流，使用LINQ语法查询Observables，并且很容易管理调度程序的并发。

Rx创造了众所周知的概念，例如**push approach** ，易于实现和消费。在响应式的世界里，我们不能一味的等待一个函数返回的结果，一个网络调用，或者一个数据库查询返回的结果而假装用户不关注或者是抱怨它。每时每刻我们都在等待某些事情，我们失去了并行处理其他事情的机会，提供一个好的用户体验，使我们的软件免受次序链的影响，阻断编程。

下表列出的与.NET 枚举相关的.NET Observable

| .NET Observable| 一个返回值| 多个返回值  |
| ------------- |:-------------:| -----:|
| Pull/Synchronous/Interactive|`T`| `IEnumerable<T>` |
| Push/Asynchronous/Reactive| `Task<T>`|`IObservable<T>`|


