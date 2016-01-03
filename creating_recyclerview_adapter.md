# 创建RecyclerView Adapter

我们从REST API获取到数据后，我们需要把它绑定View上，并用一个适配器填充列表。我们的RecyclerView适配器是标准的。它继承于`RecyclerView.Adapter`并指定它自己的`ViewHolder`：
