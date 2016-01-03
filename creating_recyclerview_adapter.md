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