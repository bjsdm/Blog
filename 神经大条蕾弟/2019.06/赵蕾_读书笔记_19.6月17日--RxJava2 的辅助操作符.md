RxJava2 的辅助操作符
-------------

# 内容简介:
> 本文章的内容是简单讲解和使用 RxJava2 中的辅助操作符.

# 目录:
[1.辅助操作符的使用场景](#1)

[2.辅助操作符的分类](#2)

[3.简单使用](#3)


# <span id = "1">**1.辅助操作符的使用场景**</span>

### 使用场景:

> &ensp;&ensp; RxJava 的辅助操作符即用于处理Observable的操作符，例如延迟、定时等。


# <span id = "2">**2.辅助操作符的分类**</span>

|操作符|功能|
|:------:|:------:|
|materialize()|将Observable转换成一个通知列表|
|dematerialize()|将上面的结果逆转回一个Observable|
|timestamp()|给Observable发射的每个数据项添加一个时间戳|
|serialize()|强制Observable按次序发射数据并且要求功能是完好的|
|cache()|记住Observable发射的数据序列并发射相同的数据序列给后续的订阅者|
|observeOn()|指定观察者观察Observable的调度器|
|subscribeOn()|指定Observable执行任务的调度器|
|doOnEach()|注册一个动作，对Observable发射的每个数据项使用|
|doOnCompleted()|注册一个动作，对正常完成的Observable使用|
|doOnError()|注册一个动作，对发生错误的Observable使用|
|doOnTerminate()|Observable终止之前会被调用，无论是正常还是异常终止|
|doOnSubscribe()|注册一个动作，在观察者订阅时使用|
|doOnUnsubscribe()|注册一个动作，在观察者取消订阅时使用|
|doOnNext()|在onNext前执行|
|doAfterNext()|在onNext之后执行|
|doAfterTerminate()|终止发送时候调用|
|doOnLifecycle()|可以在订阅之后 设置是否取消订阅|
|doFinally|在最后执行|
|finallyDo()|注册一个动作，在Observable完成时使用|
|delay()|延时发射Observable的结果|
|delaySubscription()|延时处理订阅请求|
|timeInterval()|转换获取数据发送的时间间隔|
|using()|创建一个只在Observable生命周期存在的资源|
|single()|强制返回单个数据，否则抛出异常|
|singleOrDefault()|如果Observable完成时返回了单个数据，就返回它，否则返回默认数据|
|toFuture(),toIterable(),toList()|将Observable转换为其它对象或数据结构|



# <span id = "3">**3.简单使用**</span>


`Delay 操作符`
```


Observable<Long> observable = Observable.interval(1, TimeUnit.SECONDS).take(5);
/*
 * Delay操作符让原始Observable在发射每项数据之前都暂停一段指定的时间段。
 * 效果是Observable发射的数据项在时间上向前整体平移了一个增量
 *
 * 注意：delay不会平移onError通知，它会立即将这个通知传递给订阅者，同时丢弃任何待发射的onNext通知。
 * 然而它会平移一个onCompleted通知。
 */
observable
        .delay(3, TimeUnit.SECONDS)
        .subscribe(new Subscriber<Long>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }
            @Override
            public void onError(Throwable e) {
                System.out.println("onError");
            }
            @Override
            public void onNext(Long value) {
                System.out.println("delay onNext value :"+ value);
            }
        });

/*
 * delaySubscription:延迟订阅原始Observable
 */
observable
        .delaySubscription(3, TimeUnit.SECONDS)
             .subscribe(new Subscriber<Long>() {
                 @Override
                 public void onCompleted() {
                     System.out.println("onCompleted");
                 }
                 @Override
                 public void onError(Throwable e) {
                    System.out.println("onError");
                 }
                 @Override
                 public void onNext(Long value) {
                    System.out.println("delaySubscription onNext value :"+ value);
                 }
             });



```



`Do 操作符`
```


Observable.just(1,2,3,4,5,6)
        .doOnEach(new Action1<Notification<? super Integer>>() {

            @Override
            public void call(Notification<? super Integer> t) {
                System.out.println("doOnEach value = " + t.getValue());
            }
        })
        .doOnError(new Action1<Throwable>() {

            @Override
            public void call(Throwable t) {
                System.out.println("doOnError " + t);

            }
        })
        .subscribe(new Subscriber<Integer>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                System.out.println("onError: " + e.getMessage());
            }

            @Override
            public void onNext(Integer integer) {
                System.out.println("onNext: " + integer);
            }
        });



```


`Materialize/Dematerialize 操作符`
```

Observable.just(1,2,3,4,5,6).materialize()
        .subscribe(new Action1<Notification>() {

            @Override
            public void call(Notification t) {
                //这里，onComplete也被meterialize包装后发射了出来，onError也同样。
                System.out.println("meterialize:" + t.getValue() + "   type " + t.getKind());               
            }
        });
        System.out.println("-------------------------");    

        //将Notification逆转为普通消息发射
        Observable<Integer> observable = Observable.just(1,2,3,4,5,6).materialize().dematerialize();
        observable.subscribe(new Action1<Integer>() {

            @Override
            public void call(Integer value) {
                System.out.println("onNext: " + value);
            }
        });


```




`ObserveOn/SubscribeOn 操作符`
```

Observable<Integer> observable = Observable.just(1,2);

        observable.subscribeOn(Schedulers.io())
        .map(new Func1<Integer, Integer>() {

            @Override
            public Integer call(Integer t) {
                System.out.println("map thread : " + Thread.currentThread().getName());
                return t * 10;
            }
        })
        .observeOn(Schedulers.newThread())//对应android是：AndroidSchedulers.mainThread()
        .subscribe(new Action1<Integer>() {

            @Override
            public void call(Integer value) {
                System.out.println("subscribe thread : " + Thread.currentThread().getName());
                System.out.println("onNext: " + value);
            }
        });


```


`TimeInterval 操作符`
```

Observable.create(new Observable.OnSubscribe<Integer>() {
            @Override
            public void call(Subscriber<? super Integer> subscriber) {
                for (int i = 3; i <= 7; i++) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    subscriber.onNext(i);
                }
                subscriber.onCompleted();
            }
        })
        .subscribeOn(Schedulers.io())
        .timeInterval()
        .subscribe(new Subscriber<TimeInterval<Integer>>() {

            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
            }

            @Override
            public void onNext(TimeInterval<Integer> t) {
                System.out.println("onNext: " + t.getValue() + " - " + t.getIntervalInMilliseconds());
            }
        });


```



`Timeout 操作符`
```

Observable<Integer> observable = Observable.create(new Observable.OnSubscribe<Integer>() {
            @Override
            public void call(Subscriber<? super Integer> subscriber) {
                for (int i = 0; i < 10; i++) {
                    try {
                        Thread.sleep(i*100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    subscriber.onNext(i);
                }
                subscriber.onCompleted();
            }
        });

        observable
        .timeout(200, TimeUnit.MILLISECONDS, Observable.just(100,200))
        .subscribe(new Subscriber<Integer>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                System.out.println("onError: " + e.getMessage());
            }

            @Override
            public void onNext(Integer integer) {
                System.out.println("onNext: " + integer);
            }
        });


```



`Timestamp 操作符`
```

Observable.just(1,2,3)
        .timestamp()
        .subscribe(new Subscriber<Timestamped<Integer>>() {
            @Override
            public void onCompleted() {
            }

            @Override
            public void onError(Throwable e) {
            }

            @Override
            public void onNext(Timestamped<Integer> t) {
                System.out.println("onNext: " + t.getValue() + " ,time:" + t.getTimestampMillis());
            }
        });


```


`Using 操作符`
```

Observable.from(studentList)
        .using(new Func0<Integer>() {

            @Override
            public Integer call() {
                 return new Random().nextInt(100);
            }
        }, new Func1<Integer, Observable<String>>() {

            @Override
            public Observable<String> call(Integer t) {
                return Observable.just("西部世界接待员-" + t);

            }
        }, new Action1<Integer>() {

            @Override
            public void call(Integer t) {
                t = null;
            }
        }).subscribe(new Action1<String>() {

            @Override
            public void call(String t) {
                System.out.println("call : " + t);
            }
        });


```


`To 操作符`
```

Observable.interval(500, TimeUnit.MILLISECONDS).take(3)
        .toList()
        .subscribe(new Subscriber<List<Long>>() {
            @Override
            public void onCompleted() {

            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onNext(List<Long> longs) {
                System.out.println("onNext : " + longs);
            }
        });


```