# Retrofit

Retrofit是Square公司专为Android和Java设计的一个类型安全的REST客户端。它帮助你很容易的和任何REST API交互，它完美的集成R小Java：所有的JSON响应对象都被映射成原始的Java对象，并且所有的网络调用都基于Rxjava Observable这些对象。

使用API文档，我们可以定义我们从服务器接收的JSON响应数据。为了很容易的将JSON响应数据映射为我们的Java代码，我们将使用jsonschema2pojo(http://www.jsonschema2pojo.org)，这个灵活的服务将会生成我们所需要映射JSON响应数据的所有Java类。

当我们把所有的Java model准备好后，我们就可以开始建立Retrofit。Retrofi使用标准的Java接口来映射API路由。例如例子中，我们将使用来自API的一个路由，下面是我们Retrofit的接口：
```java
public interface StackExchangeService {
    @GET("2.2users?order=desc&sort=reputation&site=stackoverflow")
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
```
为了简化例子，我们不再将这个类设计为它本应该设计为的单例。使用依赖注入解决方案，如Dagger2将会使代码质量更高。

创建`RestAdapter`类，我们为API客户端建立了几个重要的点。这个例子中，我们设置`endpoint`和`log level`。由于这个例子URL只是硬编码，使用外部资源来像这样存储数据很重要。避免在代码中硬编码字符串是一个好的实践。

Retrofit把`RestAdapter`类和我们的API接口绑定在一起后就创建结束。它返回给我们一个对象用来查询API。我们可以选择直接暴露这个对象，或者以某种方式封装依次来限制访问它。在这个例子中，我们封装它并只暴露`getMostPopularSOusers`方法。这个方法执行查询，让Retrofit解析JSON响应数据。获得用户列表，并返回给订阅者。正如你看到的，使用Retrofit、RxJava和Retrolambda，我们几乎没有模板代码：它非常紧凑并且可读性也高。



















































```
