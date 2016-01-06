# Retrofit

Retrofit是Square公司专为Android和Java设计的一个类型安全的REST客户端。它帮助你轻松地与任意REST API交互，并完美兼容RxJava：所有的JSON响应对象都被映射成原始的Java对象，并且所有的网络调用都基于Rxjava Observable这些对象。

使用API文档，我们可以定义我们从服务器接收的JSON响应数据。为了很容易的将JSON响应数据映射为我们的Java代码，我们将使用[jsonschema2pojo][1], 这个服务将灵活地生成所有与JSON响应数据相映射的Java类。

当我们把所有的Java model准备好后，我们就可以开始建立Retrofit。Retrofi使用标准的Java接口来映射API路由。例如例子中，我们将使用来自API的一个路由，下面是我们Retrofit的接口:

```java 
public interface StackExchangeService {
    @GET("/2.2/users?order=desc&sort=reputation&site=stackoverflow")
    Observable<User sResponse> getMostPopularSOusers(@Query("pagesize") int howmany);
}
``` 

`interface`接口只包含一个方法，即`getMostPopularSOusers`。这个方法用整型`howmany`作为一个参数并返回`UserResponse`的Observable。

当我们有了`interface`，我们可以创建`RestAdapter`类，为了更清楚的组织我们的代码，我们创建一个`SeApiManager`函数提供一种更适当的方式来和StackExchange API交互。

```java 
public class SeApiManager {
    private final StackExchangeService mStackExchangeService;

    public SeApiManager() {
        RestAdapter restAdapter = new RestAdapter.Builder()
            .setEndpoint("https://api.stackexchange.com")
            .setLogLevel(RestAdapter.LogLevel.BASIC)
            .build();
        mStackExchangeService = restAdapter.create(StackExchangeService.class);
    }

    public Observable<List<User>> getMostPopularSOusers(int howmany) {
        return mStackExchangeService
        .getMostPopularSOusers(howmany)
        .map(UsersResponse::getUsers)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread());
    }
}
``` 

为了简化例子，我们不再将这个类设计为它本该设计成的单例。使用依赖注入解决方案，如Dagger2，可使代码质量更高。

创建`RestAdapter`类，需要对客户端API设置几个重要的方面。这个例子中，我们设置了`endpoint`和`log level`。由于这个例子中URL只是硬编码，像这样使用外部资源来存储数据很重要。避免在代码中硬编码字符串是一个好的实践。

Retrofit把`RestAdapter`类和我们的API接口绑定在一起后就完成了创建。它返回给我们一个对象用来请求API。我们可以选择直接暴露这个对象，或者以某种封装方式来限制对它的访问。在这个例子中，我们封装它并只暴露`getMostPopularSOusers`方法。这个方法执行查询，使用Retrofit解析JSON响应数据。获得用户列表，并返回给订阅者。如你所见，使用Retrofit、RxJava和Retrolambda，我们几乎没有模板代码：它非常紧凑而且可读性很高。

现在，我们已经有一个API管理者来提供一个响应式的方法，它从远程API获取数据并给I/O调度器，解析映射最后为我们的消费者提供一个简洁的用户列表。


[1]: http://www.jsonschema2pojo.org "官网"














































```
