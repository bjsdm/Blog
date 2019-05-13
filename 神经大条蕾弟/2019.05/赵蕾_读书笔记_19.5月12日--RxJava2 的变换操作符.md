RxJava2 的变换操作符
-------------

# 内容简介:
> 本文章的内容是简单讲解和使用 RxJava2 中的变换操作符.

# 目录:
[1.变换操作符的使用场景](#1)

[2.变换操作符的分类](#2)

[3.简单使用](#3)


# <span id = "1">**1.变换操作符的使用场景**</span>

### 使用场景:

> &ensp;&ensp; RxJava 的变换操作符的作用是对 Observable 发射的数据按照一定规则做一些变换操作, 然后将变换后的数据发射出去.


# <span id = "2">**2.变换操作符的分类**</span>

|操作符|功能|
|:------:|:------:|
|buffer()|缓存，可以简单的理解为缓存，它定期从Observable收集数据到一个集合，然后把这些数据集合打包发射，而不是一次发射一个|
|map()|对序列的每一项都应用一个函数来变换Observable发射的数据序列|
|flatMap() , concatMap() , flatMapIterable()|将Observable发射的数据集合变换为Observables集合，然后将这些Observable发射的数据平坦化的放进一个单独的Observable|
|switchMap()|将Observable发射的数据集合变换为Observables集合，然后只发射这些Observables最近发射的数据|
|scan()|对Observable发射的每一项数据应用一个函数，然后按顺序依次发射每一个值|
|groupBy()|将Observable分拆为Observable集合，将原始Observable发射的数据按Key分组，每一个Observable发射一组不同的数据|
|window()| 	定期将来自Observable的数据分拆成一些Observable窗口，然后发射这些窗口，而不是每次发射一项|
|cast()|在发射之前强制将Observable发射的所有数据转换为指定类型|


# <span id = "3">**3.简单使用**</span>


`map()`
```

    private void map() {
        final String Host = "http://blog.csdn.net/";
        Observable.just("itachi85").map(new Func1<String, String>() {
            @Override
            public String call(String s) {
                return Host + s;
            }
        }).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.d(TAG, "map:" + s);
            }
        });
}


```



`flatMap()`
```


    private void flatMap() {
        final String Host = "http://blog.csdn.net/";
        List<String> mlist = new ArrayList<>();
        mlist.add("itachi85");
        mlist.add("itachi86");
        mlist.add("itachi87");
        mlist.add("itachi88");
        Observable.from(mlist).flatMap(new Func1<String, Observable<?>>() {
            @Override
            public Observable<?> call(String s) {
                return Observable.just(Host + s);
            }
        }).cast(String.class).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.d(TAG, "flatMap:" + s);
            }
        });
}



```


`concatMap()`
```

    private void concatMap() {
        final String Host = "http://blog.csdn.net/";
        List<String> mlist = new ArrayList<>();
        mlist.add("itachi85");
        mlist.add("itachi86");
        mlist.add("itachi87");
        mlist.add("itachi88");
        Observable.from(mlist).concatMap(new Func1<String, Observable<?>>() {
            @Override
            public Observable<?> call(String s) {
                return Observable.just(Host + s);
            }
        }).cast(String.class).subscribe(new Action1<String>() {
            @Override
            public void call(String s) {
                Log.d(TAG, "concatMap:" + s);
            }
        });
}


```




`flatMapIterable()`
```

    private void flatMapIterable() {
        Observable.just(1, 2, 3).flatMapIterable(new Func1<Integer, Iterable<Integer>>() {
            @Override
            public Iterable<Integer> call(Integer s) {
                List<Integer> mlist = new ArrayList<Integer>();
                mlist.add(s + 1);
                return mlist;
            }
        }).subscribe(new Action1<Integer>() {
            @Override
            public void call(Integer integer) {
                Log.d(TAG, "flatMapIterable:" + integer);
            }
        });
}


```





`buffer()`
```

    private void buffer() {
        Observable.just(1, 2, 3, 4, 5, 6)
                .buffer(3)
                .subscribe(new Action1<List<Integer>>() {
                    @Override
                    public void call(List<Integer> integers) {
                        for (Integer i : integers) {
                            Log.d(TAG, "buffer:" + i);
                        }
                        Log.d(TAG, "-----------------");
                    }
                });
}


```

