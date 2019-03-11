RxJava2.X 知识要点
-------------

# 简介:

> 这篇文章介绍的是 RxJava2.X 的知识要点. 不会对基础知识进行讲解. 

&ensp;&ensp;零基础的文章推荐:
- [给 Android 开发者的 RxJava 详解](https://gank.io/post/560e15be2dca930e00da1083)
- [这可能是最好的RxJava 2.x 教程（完结版）](https://www.jianshu.com/p/0cd258eecf60)

# 目录:
[1.RxJava 是什么?](#1)

[2.RxJava 的基本实现](#2)

[3.RxJava 的五种观察者模式](#3)

[4.监听 Observable 生命周期的回调](#4)

[5.SubJect 和 Processor 的作用](#5)

# <span id = "1">**1.RxJava 是什么?**</span>

### 定义:

&ensp;&ensp;RxJava 在 GitHub 主页上的自我介绍是 "a library for composing asynchronous and event-based programs using observable sequences for the Java VM"（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）。

> 一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库


### 优点:

> 随着程序逻辑变得越来越复杂，它依然能够保持简洁。

# <span id = "2">**2.RxJava 的基本实现**</span>

![RxJava 的观察者模式](http://ww3.sinaimg.cn/mw1024/52eb2279jw1f2rx46dspqj20gn04qaad.jpg)


### 三步走:

- 创建 Observable:
> Observe 的字面意思是被观察者, 使用 RxJava 时需要创建一个被观察者, 它会决定什么时候触发事件以及触发怎样的事件.

- 创建 Observer
> Observe 及被观察者, 它可以在不同的线程中执行任务. 这种任务模式可以极大的简化并发操作, 因为它创建了一个待命状态的观察者哨兵, 可以在未来某个时刻响应 Observeable 的通知.

- 使用 subscribe() 进行订阅
> 创建了 Observable 和 Observer 之后, 我们还需要使用 subscribe() 方法将它们连接起来, 实现链式调用.

# <span id = "3">**3.RxJava 的五种观察者模式**</span>

|被观察者|描述|被观察者|
|:------:|:------|:------:|
| Observable | 能够发射 0 或 n 个数据, 并以成功或错误事件终止. | Observer |
| Flowable | 能够发射 0 或 n 个数据, 并以成功或错误事件终止. 支持压背, 可以控制数据源发射的速度. | Subscriber |
| Single | 只发射单个数据或错误事件 | SingleObserver |
| Completable | 从来不发射数据, 值处理 onComplete 和 onError 事件. 可以看成 Rx 的 Runnable | CompletableObserver |
| Maybe | 能够发射 0 或者 1 个数据, 要么成功, 要么失败. | MaybeObserver |


# <span id = "4">**4.监听 Observable 生命周期的回调**</span>

### do 操作符:

> do 操作符可以给 Observable 的生命周期的各个阶段加上一系列的回调监听, 当 Observable 执行到这个阶段时, 这些回调就会触发. 

|操作符|用途|
|:------:|:------|
| doOnSubscribe | 一但观察者订阅了 Observable, 它就会回调 |
| doOnLifecycle | 可以在观察者订阅之后, 设置是否取消订阅 |
| doOnNext | 它产生的Observable 每发射一项数据就会调用它一次, 它的 Consumer 接受发射的数据项. 一般用于在 subscribe 之前对数据进行处理 |
| doOnEach | 它产生的 Observable 每发射一项数据就会调用它一次, 不仅包括 onNext, 还包括 onErrer 和 onCompleted |
| doAfterNext | 在 onNext 之后执行, 而doOnNext() 是在 onNext 之前执行 |
| doOnComplete | 当它产生的 Observable 在正常终止调用 onComplete 时会被调用 |
| doFinally | 在当它产生的 Observable 终止之后会被调用, 无论是正常终止还是异常终止. doFinally 优先于 doAfterTerminate 的调用 |
| doAfterTerminate | 注册一个 Action, 当 Observable 调用 onComplete 或 onError 时触发 |


# <span id = "5">**5.SubJect 和 Processor 的作用**</span>

### Subject 是一种特殊的存在:

> Subject 既是 Observable 又是 Observer. 官网称可以将 Subject 看作一个桥梁或者代理.

| Subject | 发射行为 |
|:------:|:------|
| AsyncSubject | 无论订阅发生在什么时候, 只发射最后一个数据 |
| BehaviorSubject | 发送订阅之前的一个数据和订阅之后的全部数据 |
| ReplaySubject | 不论订阅发生在什么时候, 都发射全部数据 |
| PublishSubject | 发送订阅之后全部数据 |

### Processor 比SubJect 多个支持压背:

> Processor 和 Subject 的作用相同. Processor 是 RxJava2.0 新增的功能, 它是一个接口, 继承自 Subscriber , Publisher, 能够支持压背 (Back Pressure) 控制. 这是 Processor 和 Subject 的最大区别.



