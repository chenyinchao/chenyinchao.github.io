---
title: Rxhttp请求log打印，及异常分类处理
date: 2020-10-29 01:22:53
---

### 给 Okhttp 添加拦截器，LoggingInterceptor

```java
在BaseApplication中，我们初始化Okhttp增加一个LoggingInterceptor拦截器，专门处理每个请求
new OkHttpClient.Builder().addInterceptor(new LoggingInterceptor())

LoggingInterceptor.java类，打印响应时间，header，body，url，请求方式等一切response数据，输出到本地log日志中保存
public Response intercept(@NotNull Chain chain) throws IOException {
        // 请求发起的时间
        long t1 = System.nanoTime();
        // 这个chain里面包含了request和response，所以你要什么都可以从这里拿
        Request request = chain.request();
        Response response = chain.proceed(request);
        // 收到响应的时间
        long t2 = System.nanoTime();
        // 当前时间
        Calendar calendar = Calendar.getInstance();
        SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss:SSS", Locale.getDefault());
        try {
            String result = getResult(response.body(), isOnResultDecoder(response));
            StringBuilder builder =
                new StringBuilder().append("\n\n").append("\n" + dateFormat.format(calendar.getTime()) + "\n")
                    .append(String.format("====== start 响应时间：%.1fms ", (t2 - t1) / 1e6d)).append("Method=")
                    .append(request.method()).append(" Code=").append(response.code()).append(" =======>" + "\n")
                    .append(getEncodedUrlAndParams(request)).append("\n").append(response.headers()).append("\n")
                    .append(result).append("\n" + "<===== end ============================================" + "\n\n");
            LogUtils.i(builder.toString());
        } catch (Exception e) {
            LogUtils.e("Request end Log printing failed", e);
        }
        return response;
    }
```

### 处理请求回调异常

ObservableLife 类

```java
 public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError) {
        return subscribe(onNext, onError, Functions.EMPTY_ACTION, Functions.emptyConsumer());
    }
```

LambdaObserver 类

```java
 @Override
    public void onError(Throwable t) {
        if (!isDisposed()) {
            lazySet(DisposableHelper.DISPOSED);
            try {
                onError.accept(t);
            } catch (Throwable e) {
                Exceptions.throwIfFatal(e);
                RxJavaPlugins.onError(new CompositeException(t, e));
            }
        } else {
            RxJavaPlugins.onError(t);
        }
    }
```

扩展 subscribe 方法里的 onError 接口，当出现异常时，会调用默认的 accept 方法，当 accept 方法里面处理不了，则抛出 异常到 throw (Exception)throwable; 即在全局异常中捕获

```java
public interface OnError extends Consumer<Throwable> {
    @Override
    default void accept(Throwable throwable) throws Exception {
        // 网络接口异常
        LogUtils.i("throwable name: " + throwable.getClass().getName());
        if (throwable instanceof SocketException || throwable instanceof InterruptedIOException) {
            ToastUtils.showShort("请检查网络或服务器连接！");
            // 后端接口请求异常，Http 状态码 小于200或者大于等于300时,或者ResponseBody等于null，抛出此异常
        } else if (throwable instanceof HttpStatusCodeException) {
            String statusCode = ((HttpStatusCodeException)throwable).getStatusCode();
            switch (statusCode) {
                case "403":
                    // token 过期
                    ToastUtils.showShort("403 Forbidden 请重新登录!");
                    break;
                default:
                    ToastUtils.showShort("接口网络请求异常，statusCode： " + statusCode);
                    break;
            }
        } else if (throwable instanceof ParseException) {
            String requestResult = ((ParseException)throwable).getRequestResult();
            switch (requestResult) {
                // 0，result返回null
                case "0":
                    ToastUtils.showShort("数据解析不正确，Gson解析失败");
                    break;
                // 1，isSuccess返回false
                case "1":
                    // todo 内部错误场景
                    onError(throwable);
                    break;
                // 2，data字段为null
                case "2":
                    ToastUtils.showShort("接口返回data为null");
                    break;
                default:
                    break;
            }
        } else {
            if (throwable instanceof Exception) {
                // 在全局异常中捕获
                throw (Exception)throwable;
            }
        }
    }

    // 异常接口回调
    void onError(Throwable throwable) throws Exception;
}
```
