---
title: Android WebSocket即时通信
date: 2020-05-15 22:45:37
---
```java
public class WebSocketHelper {

    private WebSocketClient mWebSocketClient;

    private WebSocketHelper() {}

    private static WebSocketHelper instance = null;

    public static WebSocketHelper getInstance() {
        if (instance == null) {
            instance = new WebSocketHelper();
        }
        return instance;
    }

    /**
     * 初始化websocket
     * @param address websocket通信，"ws://127.0.0.1:10000"
     */
    public void initSockect(String address) {
        LogUtils.i("initSockect");
        URI uri = URI.create(address);
        if (null == mWebSocketClient) {
            mWebSocketClient = new WebSocketClient(uri) {
                @Override
                public void onOpen(ServerHandshake serverHandshake) {
                    LogUtils.i("onOpen: " + serverHandshake.getHttpStatusMessage());
                }

                @Override
                public void onMessage(String s) {
                    LogUtils.i("onMessage: " + s);
                }

                @Override
                public void onClose(int i, String s, boolean b) {
                    LogUtils.i("onClose: " + s);
                }

                @Override
                public void onError(Exception e) {
                    LogUtils.i("onError: " + e.getMessage());
                    // 确保在同一局域网下
                    ToastUtils.showLong("请检查打印服务器IP和端口连接");
                }
            };
            connect();
        }
    }

    /**
     * 连接websocket
     */
    private void connect() {
        LogUtils.i("connect");
        new Thread() {
            @Override
            public void run() {
                try {
                    // connectBlocking多出一个等待操作，会先连接再发送，否则未连接发送会报错
                    mWebSocketClient.connectBlocking();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }.start();
    }

    /**
     * 断开websocket连接
     */
    public void closeConnect() {
        LogUtils.i("closeConnect");
        try {
            if (null != mWebSocketClient) {
                mWebSocketClient.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            mWebSocketClient = null;
        }
    }

    /**
     * 发送消息给服务端
     */
    public void sendMessage(Object msg) {
        LogUtils.i("sendMessage");
        if (mWebSocketClient != null && mWebSocketClient.isOpen()) {
            String jsonStr = GsonUtils.toJson(msg);
            mWebSocketClient.send(jsonStr);
        } else {
            ToastUtils.showShort("未开启websocket连接");
        }
    }
}

------------------

// 使用，在基类BaseActivity
protected WebSocketHelper mWebSocketHelper = WebSocketHelper.getInstance();
// onCreate
if (needWebSocket()) {
    // 连接服务器，websocket通信，"ws://127.0.0.1:10000"
    mWebSocketHelper.initSockect(address);
}
// onDestory
mWebSocketHelper.closeConnect();

// 默认所有页不启用websocket连接
protected boolean needWebSocket() {
    return false;
}

------------------

// 在子类，发消息
mWebSocketHelper.sendMessage(msg);

protected boolean needWebSocket() {
    return true;
}
```
> 参考：
https://www.jianshu.com/p/7b919910c892
https://github.com/yangxch/WebSocketClient