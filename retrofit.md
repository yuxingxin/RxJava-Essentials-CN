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
