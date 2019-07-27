# Vainglory
## EventBus
### 加入项目
`implementation 'org.greenrobot:eventbus:3.1.1'`
### 基本使用
1. 注册和解注册总线
```java
//在Activity重写onStart
@Override
public void onStart() {
    super.onStart();
    //注册总线
    EventBus.getDefault().register(this);
}
@Override
public void onStop() {
    //解注册总线
    EventBus.getDefault().unregister(this);
    super.onStop();
}
```

2. 准备事件传递数据的类型
```java
public  class  MessageEvent  {
   public  final  String  message;
   public  MessageEvent(String  message)  {
     this.message  =  message;
   }
}
```

3. 定义接受信号执行者（订阅者）
```java
// 接收到MessageEvent信号在主线程执行此方法
@Subscribe(threadMode = ThreadMode.MAIN)
public void onMessageEvent(MessageEvent event) {
    Toast.makeText(getActivity(), event.message, Toast.LENGTH_SHORT).show();
}
//接受到SomeOtherEvent信号时默认线程模式执行此方法
@Subscribe
public void handleSomethingElse(SomeOtherEvent event) {
    doSomethingWith(event);
}
```

4. 发布信号（发布事件）
```java
EventBus.getDefault().post(new MessageEvent("Hello everyone!"));
```

### 线程模式
#### ThreadMode: MAIN
> 运行在主线程或UI线程
```java
// Called in Android UI's main thread
@Subscribe(threadMode = ThreadMode.MAIN)
public void onMessage(MessageEvent event) {
    textField.setText(event.message);
}
```
#### ThreadMode: POSTING
> 在哪个线程发布信号在哪个线程执行，默认模式
```java
// Called in the same thread (default)
// ThreadMode is optional here
@Subscribe(threadMode = ThreadMode.POSTING)
public void onMessage(MessageEvent event) {
    log(event.message);
}
```
#### ThreadMode: MAIN_ORDERED
> 最高优先级调用，如果之前已有一个事件发布，然后在调用此模式事件，此模式将无须等待，同步执行
```java
// Called in Android UI's main thread
@Subscribe(threadMode = ThreadMode.MAIN_ORDERED)
public void onMessage(MessageEvent event) {
    textField.setText(event.message);
}
```
#### ThreadMode: BACKGROUND
> 在后台线程调用，如果发布事件在主线程，那么开个子线程调用；反之在子线程中发布，则直接调用。事件总线使用单个后线程，它将按顺序传递所有事件
```java
// Called in the background thread
@Subscribe(threadMode = ThreadMode.BACKGROUND)
public void onMessage(MessageEvent event){
    saveToDisk(event.message);
}
```
#### ThreadMode:  ASYNC
> 此模式将在独立线程中调用，适用于耗时长的方法
```java
// Called in a separate thread
@Subscribe(threadMode = ThreadMode.ASYNC)
public void onMessage(MessageEvent event){
    backend.send(event.message);
}
```
### Sticky Event（粘性事件）
> 即让事件在发布后，然能保持最新状态
```java
// 发布粘性信号
EventBus.getDefault().postSticky(new MessageEvent("Hello everyone!"));
// sticky =true,可以接收粘性信号
@Subscribe(sticky = true, threadMode = ThreadMode.MAIN)
public void onEvent(MessageEvent event) {   
    textField.setText(event.message);
}
// 移除粘性事件
MessageEvent stickyEvent = EventBus.getDefault().getStickyEvent(MessageEvent.class);
// Better check that an event was actually posted before
if(stickyEvent != null) {
    // "Consume" the sticky event
    EventBus.getDefault().removeStickyEvent(stickyEvent);
    // Now do something with it
}
```
