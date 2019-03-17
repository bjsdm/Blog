RxJava 2.X 中的背压
-------------

# 简介:
> 本文章介绍的是 RxJava 2.X 中的压背.

# 目录:
[1.什么是背压](#1)

[2.背压的策略](#2)


# <span id = "1">**1.什么是背压**</span>

### 定义:

&ensp;&ensp;在 RxJava 中, 会遇到 `被观察者` 发送消息太快以至于它的操作符或者 `订阅者` 不能及时处理相关的消息, 这就是典型的 `背压(Back Pressure)`

> `Back Pressure` 经常被翻译为 `背压 `. 是指在异步场景下, `被观察者` 发送事件速度远快于 `观察者` 处理的速度, 从而导致下游的 buffer 溢出, 这种现象叫做背压.


### 注意点:

- 背压必须是在异步场景下才会出现.
> 即被观察者和观察者处于不同的线程中.

- RxJava 是基于 Push 模型的.
> 在 RxJava 中, 只要生产者数据准备好了就会发射出去. 如果生产者比较慢, 则消费者会等待新的数据到来. 如果生产者比较快, 则会有很多数据发射给消费者, 而不管消费者当前有没有能力处理数据, 这样就会导致背压.

- 在 RxJava 2.X 中, 只有新增的 Flowable 类型是支持背压的.
> Flowable 很多操作符内部都使用了背压策略, 从而避免过多的数据填满内部的队列. (Flowable 的默认队列是 128)


# <span id = "2">**2.背压的策略**</span>

### 背压策略种类:

- MISSING
> 此策略表示, 通过 Create 方法创建的 Flowable 没有指定背压策略, 不会对通过 OnNext 发射的数据做缓存或丢弃处理, 需要下游通过背压操作符 (onBackpressureBuffer()/onBackpressureDrop()/onBackpressureLatest()) 指定背压策略

- ERROR
> 此策略表示, 如果放入 Flowable 的异步缓存池中的数据超限了, 则会抛出 MissingBackpressureException 异常.

```

        Flowable.create(new FlowableOnSubscribe<Integer>() {

            @Override
            public void subscribe(FlowableEmitter<Integer> e) throws Exception {
                for (int i = 0; i<129; i++) {
                    e.onNext(i);
                }
            }
        }, BackpressureStrategy.ERROR)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        System.out.println(integer);
                    }
});


```


- BUFFER
> 此策略表示, Flowable 的异步缓存池同 Observable 的一样, 没有固定大小, 可以无限制添加数据, 不会抛出 MissingBackpressureException 异常, 但会导致 OOM.

```

Flowable.create(new FlowableOnSubscribe<Integer>() {

            @Override
            public void subscribe(FlowableEmitter<Integer> e) throws Exception {
                for (int i = 0;; i++) {
                    e.onNext(i);
                }
            }
        }, BackpressureStrategy.BUFFER)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        System.out.println(integer);
                    }
});

```


- DROP
> 此策略表示, 如果 Flowable 的异步缓存池满了, 则会丢掉将要放入缓存池中的数据.

```


        Flowable.create(new FlowableOnSubscribe<Integer>() {

            @Override
            public void subscribe(FlowableEmitter<Integer> e) throws Exception {
                for (int i = 0; i<129; i++) {
                    e.onNext(i);
                }
            }
        }, BackpressureStrategy.DROP)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        System.out.println(integer);
                    }
});


```


- LATEST
> 此策略表示, 如果缓存池满了, 会丢掉将要放入缓存池中的数据. 这一点与 DROP 策略一样, 不同的是, 不管缓存池的状态如果, LATEST 策略会将最后一条数据强行放入缓存池中.

```

        Flowable.create(new FlowableOnSubscribe<Integer>() {

            @Override
            public void subscribe(FlowableEmitter<Integer> e) throws Exception {
                for (int i = 0; i<1000; i++) {
                    e.onNext(i);
                }
            }
        }, BackpressureStrategy.LATEST)
                .subscribeOn(Schedulers.newThread())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Integer>() {
                    @Override
                    public void accept(Integer integer) throws Exception {
                        System.out.println(integer);
                    }
});

```



