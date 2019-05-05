使用 RxJava 开发一个 EventBus: RxBus(普通版)
-------------

# 简介:
> 本文章是使用 Rxjava 来开发一个 RxJava 版的 RxBus, 本文的 RxBus 只是简单版, 不包含 `错误处理`, `背压` 等操作.

# 目录:
[1.什么是 EventBus](#1)

[2.为什么要使用 RxJava 来实现](#2)

[3.简易版 RxBus 的实现](#3)


# <span id = "1">**1.什么是 EventBus**</span>

### 传统的 EventBus:

&ensp;&ensp; 它是一种消息`发布--订阅的模式`, 它的工作机制类似于`观察者模式`, 通过通知者去注册观察者, 最后由通知者向观察者发布消息.

> EventBus 是`事件总线`框架.


### EventBus 的主要工作角色:

- Event: 
> 传递的事件对象
- Subscriber:
> 事件的订阅者
- Publisher:
> 事件的发布者
- ThreadMode:
> 定义函数在何种线程中执行.

### EventBus 的优缺点

- 优点:
> 优雅的实现模块之间的解耦

- 缺点:
> 在开发者很可能会定义非常多的 Event, 这也是被人诟病的最多的地方.(本人深有体会)

# <span id = "2">**2.为什么要使用 RxJava 来实现**</span>


![EventBus 的工作模式](https://raw.githubusercontent.com/greenrobot/EventBus/master/EventBus-Publish-Subscribe.png)

![RxJava 的工作模式](http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx4ay0hrg20ig08wk4q.gif)


### 原有:

- EventBus 的 **消息发布--订阅的模式**工作机制类似于`观察者模式`, 与 RxJava 的设计 观察者者--被观察者的模式高度相似, RxJava 使用的模式就是一种通用的`观察者模式`, 用 Rxjava 的`函数响应式编程`可以很轻松的实现 EventBus 的功能.
- 角色基本基本相似: RxJava 中的角色有 EventBus 中的角色功能 Rxjava 中都有.

# <span id = "3">**3.简易版 RxBus 的实现**</span>



`RxBus 主要代码`
```

import io.reactivex.Observable;
import io.reactivex.subjects.PublishSubject;
import io.reactivex.subjects.Subject;

/**
 * 一个普通的 RxBus
 *
 * @author 神经大条蕾弟
 * @date 2019/04/21 14:25
 */

public class RxBus_1 {

    private final Subject<Object> mBus;

    private RxBus_1(){
        //线程安全
        mBus = PublishSubject.create().toSerialized();
    }

    /**
     * 获取对象
     * @return 对象
     */
    public static RxBus_1 get(){
        return Holder.BUS;
    }

    /**
     * 发射事件
     * @param obj 事件对象
     */
    public void post(Object obj){
        mBus.onNext(obj);
    }

    /**
     * 获取Observable对象
     * @param tClass 过滤
     * @param <T>
     * @return
     */
    public <T> Observable<T> toObservable(Class<T> tClass){
        return mBus.ofType(tClass);
    }

    public Observable<Object> toObservable(){
        return mBus;
    }

    /**
     * 是否错误终止
     * @return true: 错误   false: 正常
     */
    public boolean hasObservers(){
        return mBus.hasObservers();
    }

    /**
     * 获取对象的holder方法
     */
    private static class Holder{
        private static final RxBus_1 BUS = new RxBus_1();
    }

}


```

`客户端使用`
```

//定义一个可主动解除订阅的被观察者
private Disposable disposable;

//注册 RxBus
private void registerEvents() {

        disposable = rxBus.toObservable(CrossActivityEvent.class)
                .subscribe(new Consumer<CrossActivityEvent>() {
            @Override
            public void accept(@NonNull CrossActivityEvent event) throws Exception {
                Toast.makeText(MainActivity.this,"来自MainActivity的Toast",Toast.LENGTH_SHORT).show();
            }
        });
}


@Override
    protected void onDestroy() {
        super.onDestroy();	
		//在页面销毁时取消订阅, 避免内存溢出.
        if (disposable!=null && !disposable.isDisposed()) {

            disposable.dispose();
        }
}

//在需要的地方发射事件
rxBus.post(new CrossActivityEvent());


```

