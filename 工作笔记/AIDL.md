# 作用
进程间通信

# RemoteCallbackList
用于**服务端**回调**客户端**的方法。

```java
interface ICloudService {
    void registerCallback(EventCallback callback);
    void unRegisterCallback(EventCallback callback);
}
```

```java
interface EventCallback {
	void onEvent(int state, String args);
}
```

```java
// 用于存储注册的客户端，需要时服务端会通过这个list向客户端发消息。
private static final RemoteCallbackList<EventCallback> remoteCallbackList = new RemoteCallbackList<>();

private static final ICloudService.Stub binder = new ICloudService.Stub() {
	@Override
	public void registerCallback(EventCallback callback) throws RemoteException {
		remoteCallbackList.register(callback);
	}

	@Override
	public void unRegisterCallback(EventCallback callback) throws RemoteException {
		remoteCallbackList.unregister(callback);
	}
};

// 此函数用于向客户端发送消息
public static void sendEvent(int event, String args) {
	int i = remoteCallbackList.beginBroadcast();
	while (i > 0) {
		i--;
		try {
			remoteCallbackList.getBroadcastItem(i).onEvent(event, args);
		} catch (RemoteException e) {
			// The RemoteCallbackList will take care of removing
			// the dead object for us.
		}
	}
	remoteCallbackList.finishBroadcast();
}
```

```java
// 客户端定义EventCallback
private final EventCallback mCloudEventCallback = new EventCallback.Stub() {
	@Override
	public void onEvent(int state, String args) throws RemoteException {
	}
};

// connected后，把上面定义的callback注册到服务端供其调用，disconnected时再把其反注册掉。
private final ServiceConnection serviceConnection = new ServiceConnection() {

	@Override
	public void onServiceConnected(ComponentName componentName, IBinder service) {
		synchronized (remoteLock) {
			remoteServer = ICloudService.Stub.asInterface(service);
		}
		registerRemoteCallback();
	}

	@Override
	public void onServiceDisconnected(ComponentName componentName) {
		unRegisterRemoteCallback();
		synchronized (remoteLock) {
			remoteServer = null;
		}
	}
};

private void registerRemoteCallback() {
	synchronized (remoteLock) {
		if (remoteServer != null) {
			try {
				remoteServer.registerCallback(mCloudEventCallback);
			} catch (RemoteException e) {
				throw new RuntimeException(e);
			}
		}
	}
}

private void unRegisterRemoteCallback() {
	synchronized (remoteLock) {
		if (remoteServer != null) {
			try {
				remoteServer.unRegisterCallback(mCloudEventCallback);
			} catch (RemoteException e) {
				throw new RuntimeException(e);
			}
		}
	}
}

```

# 参考连接
\[1\] https://developer.android.com/guide/components/aidl?hl=zh-cn
\[2\] [安卓 RemoteCallbackList 的使用 （应用篇）](https://www.cnblogs.com/SupperMary/p/14746477.html)