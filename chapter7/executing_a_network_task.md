# 执行网络任务

在当今99%的移动应用中网络都是必不可缺的一部分：总是需要连接远程服务器来检索App需要的信息。

作为网络访问的第一个案例，我们将创建下面这样一个场景:

* 加载一个进度条。
* 用一个按钮开始文件下载。
* 下载过程中更新进度条。
* 下载完后开始视频播放。

我们的用户界面非常简单，我们只需要一个有趣的进度条和一个下载按钮。

![](../images/chapter7_5.png)

首先，我们创建`mDownloadProgress`

```java
private PublishSubject<Integer> mDownloadProgress = PublishSubject.create();
```
这个主题我们用来管理进度的更新，它和`download`函数协同工作。
```java
private boolean downloadFile(String source, String destination) {
    boolean result = false;
    InputStream input = null; 
    OutputStream output = null; 
    HttpURLConnection connection = null;
    try {
        URL url = new URL(source);
        connection = (HttpURLConnection) url.openConnection(); 
        connection.connect();
        if (connection.getResponseCode() != HttpURLConnection.HTTP_OK) {
            return false;
        }
        int fileLength = connection.getContentLength();
        input = connection.getInputStream();
        output = new FileOutputStream(destination);
        byte data[] = new byte[4096];
        long total = 0;
        int count;
        while ((count = input.read(data)) != -1) {
            total += count;
            if (fileLength >0) {
                int percentage = (int) (total * 100 / fileLength);
                mDownloadProgress.onNext(percentage);
            }
            output.write(data, 0, count); 
        }
        mDownloadProgress.onCompleted(); 
        result = true;
    } catch (Exception e) { 
        mDownloadProgress.onError(e);
    } finally { 
        try {
            if (output != null) { 
                output.close();
            }
            if (input != null) {
                input.close(); 
            }
        } catch (IOException e) {    
            mDownloadProgress.onError(e);
        }
        if (connection != null) {
            connection.disconnect();
            mDownloadProgress.onCompleted();
        }
    }
    return result;
}
```
上面的这段代码将会触发`NetworkOnMainThreadException`异常。我们可以创建RxJava版本的函数进入我们挚爱的响应式世界来解决这个问题：

```java
private Observable<Boolean> obserbableDownload(String source, String destination) {
    return Observable.create(subscriber -> {
        try {
            boolean result = downloadFile(source, destination); 
            if (result) {
                subscriber.onNext(true);
                subscriber.onCompleted(); 
            } else {
                subscriber.onError(new Throwable("Download failed."));
            }
        } catch (Exception e) { 
            subscriber.onError(e);
        } 
    });
}
```
现在我们需要触发下载操作，点击下载按钮:
```java
@OnClick(R.id.button_download)
void download() {
    mButton.setText(getString(R.string.downloading));
    mButton.setClickable(false);
    mDownloadProgress.distinct()
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Observer<Integer>() {
    
        @Override
        public void onCompleted() {  
            App.L.debug("Completed");
        }
    
        @Override
        public void onError(Throwable e) {
            App.L.error(e.toString()); 
        }
        
        @Override
        public void onNext(Integer progress) {
            mArcProgress.setProgress(progress);
        } 
    });
    
    String destination = "sdcardsoftboy.avi";
    obserbableDownload("http://archive.blender.org/fileadmin/movies/softboy.avi", destination)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(success -> {
            resetDownloadButton();
            Intent intent = new Intent(android.content.Intent.ACTION_VIEW);
            File file = new File(destination);
            intent.setDataAndType(Uri.fromFile(file),"video/avi");
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK); 
            startActivity(intent);
        }, error -> {
            Toast.makeText(getActivity(), "Something went south", Toast.LENGTH_SHORT).show();
            resetDownloadButton();
        });
}
```
我们使用Butter Knife的注解`@OnClick`来绑定按钮的方法并更新按钮信息和点击状态：我们不想让用户点击多次从而触发多次下载事件。

然后，我们创建一个subscription来观察下载进度并相应的更新进度条。很明显，我们订阅在主线程是因为进度条是UI元素。

```java
obserbableDownload("http://archive.blender.org/fileadmin/movies/softboy.avi", "sdcardsoftboy.avi";)
```
这是一个下载Observable。网络调用是一个I/O任务，理应使用I/O调度器。当下载完成，就会在`onNext()` 中启动视频播放器，并且播放器将会在目标路径找到下载的文件.。

下图展示了下载进度和视频播放器选择对话框：

![](../images/chapter7_6.png)


























