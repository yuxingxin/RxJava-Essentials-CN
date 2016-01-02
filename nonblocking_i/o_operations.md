# 非阻塞I/O操作

现在我们知道如何在一个指定I/O调度器上来调度一个任务，我们可以修改`storeBitmap()`函数并再次检查`StrictMode`的不合规做法。为了这个例子，我们可以在新的`blockingStoreBitmap()`函数中重排代码。

```java
private static void blockingStoreBitmap(Context context, Bitmap bitmap, String filename) {
    FileOutputStream fOut = null; 
    try {
        fOut = context.openFileOutput(filename, Context.MODE_PRIVATE);
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, fOut); 
        fOut.flush();
        fOut.close();
    } catch (Exception e) {
        throw new RuntimeException(e);
    } finally { 
        try {
            if (fOut != null) {
                fOut.close();
            }
        } catch (IOException e) {
            throw new RuntimeException(e); 
        }
    } 
}
```
现在我们可以使用`Schedulers.io()`创建非阻塞的版本：

```java
public static void storeBitmap(Context context, Bitmap bitmap, String filename) {
    Schedulers.io().createWorker().schedule(() -> {
        blockingStoreBitmap(context, bitmap, filename);
    }); 
}
```

每次我们调用`storeBitmap()`，RxJava利用创建所有它需要的在来自I/O线程池里指定的I/O线程执行我们的任务。



























