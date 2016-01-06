# 创建RecyclerView Adapter

我们从REST API获取到数据后，我们需要把它绑定View上，并用一个适配器填充列表。我们的RecyclerView适配器是标准的。它继承于`RecyclerView.Adapter`并指定它自己的`ViewHolder`：
```java
public static class ViewHolder extends RecyclerView.ViewHolder {
    @InjectView(R.id.name) TextView name;
    @InjectView(R.id.city) TextView city;
    @InjectView(R.id.reputation) TextView reputation;
    @InjectView(R.id.user_image) ImageView user_image;
    public ViewHolder(View view) { 
        super(view);
        ButterKnife.inject(this, view); 
    }
}
```
我们一旦收到来自API管理器的数据，我们可以设置界面上所有的标签：`name`,`city`和`reputation`。

为了展示用户的头像，我们将使用Sergey Tarasevich写的[Universal Image Loader][1]。实践证明，UIL是非常有名的好用的图片管理库。我们也可以使用Square公司的Picasso，Glide或者Facebook公司的Fresco。取决于你自己的喜好。最关键的是无需重复造轮子：库能够方便开发者并让他们更快速实现目标。

在我们的适配器中，我们可以这样：
```java
@Override
public void onBindViewHolder(SoAdapter.ViewHolder holder, int position) {
    User user = mUsers.get(position);
    holder.setUser(user); 
}
```
在`ViewHolder`，我们可以这样：
```java
public void setUser(User user) { 
    name.setText(user.getDisplayName());
    city.setText(user.getLocation());
    reputation.setText(String.valueOf(user.getReputation()));
    
    ImageLoader.getInstance().displayImage(user.getProfileImage(), user_image);
}
```
此时，我们可以允许代码获得一个用户列表，正如下图所示：

![](../images/chapter8_1.png)

### 检索天气预报

我们加大难度，将当地城市的天气加入列表中。**OpenWeatherMap**是一个灵活公共在线天气API，我们可以查询许多有用的预报信息。

和往常一样，我们将使用Retrofit映射到API然后通过RxJava来访问它。至于StackExchange API，我们将创建`interface`，`RestAdapter`和一个灵活的管理器：

```java
public interface OpenWeatherMapService {
    @GET("data2.5/weather")
    Observable<WeatherResponse> getForecastByCity(@Query("q") String city);
}
```
这个方法用城市名字作为参数提供当地的预报信息。我们像下面这样将接口和`RestAdapter`类绑定在一起：
```java
RestAdapter restAdapter = new RestAdapter.Builder()
        .setEndpoint("http://api.openweathermap.org")
        .setLogLevel(RestAdapter.LogLevel.BASIC)
        .build();
mOpenWeatherMapService = restAdapter.create(OpenWeatherMapService.class);
```
像以前一样，我们只有两件事需要立马去做：设置API端口和log级别。

`OpenWeatherMapApiManager`类将提供下面的方法：
```java
public Observable<WeatherResponse> getForecastByCity(String city) {
    return mOpenWeatherMapService.getForecastByCity(city)
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread());
}
```
现在，我们有了用户列表，我们可以根据城市名来查询OpenWeatherMap获得天气预报信息。下一步是修改我们的`ViewHolder`类来为每位用户展示相应的天气图标。

我们使用这些工具方法先验证用户主页信息并获得一个合法的城市名字：
```java
private boolean isCityValid(String location) {
    int separatorPosition = getSeparatorPosition(location);
    return !"".equals(location) && separatorPosition > -1; 
}

private int getSeparatorPosition(String location) { 
    int separatorPosition = -1;
    if (location != null) {
        separatorPosition = location.indexOf(","); 
    }
    return separatorPosition; 
}

private String getCity(String location, int position) {
    if (location != null) {
        return location.substring(0, position); 
    } else {
        return ""; 
    }
}
```
借助一个有效的城市名，我们可以用下面命令来获得我们所需要天气的所有数据：
```java
OpenWeatherMapApiManager.getInstance().getForecastByCity(city)
```
用天气响应的结果，我们可以获得天气图标的URL：
```java
getWeatherIconUrl(weatherResponse);
```
用图标URL，我们可以检索到图标本身：
```java
private Observable<Bitmap> loadBitmap(String url) {
    return Observable.create(subscriber -> {
        ImageLoader.getInstance().displayImage(url,city_image, new ImageLoadingListener() { 
            @Override
            public void onLoadingStarted(String imageUri, View view) {
            }
            
            @Override
            public void onLoadingFailed(String imageUri, View view, FailReason failReason) {
                subscriber.onError(failReason.getCause()); 
            }
            
            @Override
            public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
                subscriber.onNext(loadedImage);
                subscriber.onCompleted(); 
            }
            
            @Override
            public void onLoadingCancelled(String imageUri, View view) {
                subscriber.onError(new Throwable("Image loading cancelled"));
            }
         });
    });
}
```
这个`loadBitmap()`返回的Observable可以链接前面一个，并且最后我们可以为这个任务返回一个单独的Observable：
```java
if (isCityValid(location)) {
    String city = getCity(location, separatorPosition);
    OpenWeatherMapApiManager.getInstance().getForecastByCity(city)
        .filter(response -> response != null)
        .filter(response -> response.getWeather().size() > 0)
        .flatMap(response -> {
            String url = getWeatherIconUrl(response);
            return loadBitmap(url);
        })
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Observer<Bitmap>() {
    
        @Override
        public void onCompleted() {
        }
        
        @Override
        public void onError(Throwable e) {
            App.L.error(e.toString()); 
        }
        
        @Override
        public void onNext(Bitmap icon) {
            city_image.setImageBitmap(icon); 
        }
    });
}
```
运行代码，我们可以在下面列表中为每个用户获得新的天气图标：
![](../images/chapter8_2.png)

### 打开网站

使用用户主页包含的信息，我们将会创建一个`onClick`监听器来导航到用户web页面，如果有的话，否则打开在Stack Overflow上的个人主页。

为了实现它，我们简单实现`Activity`类的接口，用来在适配器触发Android的`onClick`事件。

我们的`Adapter ViewHolder`指定这个接口：
```java
public interface OpenProfileListener {
    public void open(String url); 
}
```

`Activity`实现它：
```java
[...] implements SoAdapter.ViewHolder.OpenProfileListener { [...]
    mAdapter.setOpenProfileListener(this); 
[...]

@Override
public void open(String url) {
    Intent i = new Intent(Intent.ACTION_VIEW);
    i.setData(Uri.parse(url)); 
    startActivity(i);
}
```
`Activity`收到URL并用外部Android浏览器打开它。我们的`ViewHolder`负责在用户列表的每个卡片上创建`OnClickListener`并检查我们是打开Stack Overflow用户主页还是外部个人站：

```java
mView.setOnClickListener(view -> { 
    if (mProfileListener != null) {
        String url = user.getWebsiteUrl();
        if (url != null && !url.equals("") && !url.contains("search")) {
            mProfileListener.open(url); 
        } else {
            mProfileListener.open(user.getLink()); 
        }
    }
)}；
```
一旦我们点击了，我们将直接重定向到预期的网站。在Android上，我们可以用RxAndroid的一种特殊形式（ViewObservable）以更加响应式的方式实现同样的结果。
```java
ViewObservable.clicks(mView)
    .subscribe(onClickEvent -> {
    if (mProfileListener != null) {
        String url = user.getWebsiteUrl();
        if (url != null && !url.equals("") && !url.contains("search")) { 
            mProfileListener.open(url);
        } else {
            mProfileListener.open(user.getLink());
        } 
    }
});
```
上面两块代码片段是等价的，你可以选择最喜欢的方式来实现。


[1]: https://github.com/nostra13/Android-Universal-ImageLoader
















































