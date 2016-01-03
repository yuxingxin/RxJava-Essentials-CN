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

为了展示用户的头像，我们将使用Sergey Tarasevich (https://github.com/nostra13/Android-Universal- ImageLoader)写的`Universal Image Loader`。UIL是非常有名的并且被测试出很好用的图片管理库。我们也可以使用Square公司的Picasso，Glide或者Facebook公司的Fresco。这只是根据你自己的爱好。重要的是无须重复造轮子：库能够方便开发者的生活并让他们更快速实现目标。

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

![](chapter8_1.png)




















































