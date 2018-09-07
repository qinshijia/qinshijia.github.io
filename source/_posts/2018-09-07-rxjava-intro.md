---
title: RxJava 响应式编程
date: 2018-09-07 15:12:11
tags: RxJava
categories: RxJava
description: 传统的异步编程一般用回调函数的方式来完成更新操作，在遇到嵌套异步任务（例如嵌套的网络请求），往往会导致逻辑复杂、代码晦涩难懂、容易出现各种各样bug。多个层级的嵌套回调，很容易导致回调地狱。RxJava是基于事件流的响应式编程，就能很好的解决这种问题。
---

## RxJava 是什么

> a library for composing asynchronous and event-based programs using observable sequences for the Java VM.
>
> 一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库

简单来说，RxJava是基于事件流的响应式编程，下面结合例子说明一下响应式编程是什么。

```java
// init variables
int i, j, k;

// Init inputs
i = 1;
j = 2;

// Set output value
k = i + j;

// Update a dependent value
j = 4;
k = ?  // What should k be?
```

在上面这段代码中，`k = i + j` ，但是当 `j` 的值发生改变后，`k` 的值并不随 `j` 的改变而改变。而响应式编程就是一种能让 `k` 随着 `j` 的改变而改变的编程模式。传统的异步编程一般用回调函数的方式来完成更新操作，在遇到嵌套异步任务（例如嵌套的网络请求），往往会导致逻辑复杂、代码晦涩难懂、容易出现各种各样bug。多个层级的嵌套回调，很容易导致[回调地狱](http://callbackhell.com/)，响应式编程就能很好的解决这种问题。

## RxJava 的优点

* __简单__：能够轻松的应对异步任务、嵌套请求、嵌套回调的情况
* __线程调度清晰__：能轻松的指定任务执行的线程，两行代码就能实现后台执行耗时任务、前台显示的线程调度
* __统一的错误处理__：在嵌套的回调中往往需要在多处处理错误信息，RxJava只需在一个地方处理就可以
* __减少不必要的中间状态变量__：多个异步任务同时执行时，会需要一下中间状态变量（例如各种标志位）来控制执行的顺序，从而会导致各种奇怪的bug，而基于数据流的Rxjava则不需要这些变量，代码更加简洁，质量也会更高。

## 开始使用 RxJava

### RxJava 配置
首先需要添加配置导入RxJava 库 `app/build.gradle`:

```
dependencies {
  implementation 'io.reactivex:rxandroid:1.2.0'
  implementation 'io.reactivex:rxjava:1.1.4'
}
```

> 使用RxJava的时强烈推荐使用Lambda表达式，可以减少很多代码量。为简单起见，本文不使用Lambda表达式，可自行百度学习

### 被观察者与观察者
RxJava是建立在被观察者和观察者两者的关系之上，观察者订阅被观察者后，被观察者一旦发生变化就会通知观察者。

`observable` 表示被观察者，可以发射一系列事件。`observer` 是观察者，可以消耗 `observable` 发射出来的事件。当 `observer` 订阅  `observable` 后，`observable` 才开始发射事件。

```java
Observable.just(1, 2, 3)
        .subscribe(new Observer<Integer>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                e.printStackTrace();
            }

            @Override
            public void onNext(Integer integer) {
                System.out.println("onNext value：" + integer );
            }
        });

// 执行以上代码，会打印如下信息：
// onNext value：1
// onNext value：2
// onNext value：3
// onCompleted
```

以上代码中，被观察者 `Observables` 通过操作符 `just` 依次发射数据 `1,2,3` 。观察者 `Observer` 通过操作符 `subscribe` 订阅后，`observables` 开始发射数据， `Observer` 的 `onNext()` 方法就能接收并消耗掉数据，`observables` 发射结束后，就会收到             `onCompleted` 事件，整个过程结束。若这个过程中发生错误，就会收到 `onError` 事件，可在此处做同一的错误处理。

`Observables` 常用的发射操作符还有：
* __from__：将其它种类的对象和数据类型转换为Observable
* __interval__：创建一个按固定时间间隔发射整数序列的Observable
* __repeat__：创建一个发射特定数据重复多次的Observable
* __timer__：创建一个Observable，它在一个给定的延迟后发射一个特殊的值。

`Observer` 常用它的抽象类 `Subscriber` 代替，事实上 `Observer` 在订阅的时候也会先转化为 `Subscriber` ，下文将统一采用 `Subscriber` 。

### 线程调度
默认情况下，RxJava 是在哪订阅就在哪发射事件和消耗事件，即：都是在调用 `subsribe()` 线程内执行。若需要切换线程，就要使用线程调度 `Scheduler` 。

线程调度使用到以下两个操作符：
* subscribeOn：指定`subscribe()`所发生的线程，即事件产生的线程，仅一次有效。
* observeOn：指定`Subscriber`所运行在的线程，即事件消费的线程，可多次调用，达到多次切换线程的效果。

线程调度的类型：

| 类型 | 描述 |
| :--- | :--- |
| Schedulers.computation() | 计算型线程 |
| Schedulers.immediate() | 当前线程 |
| Schedulers.io() | IO线程，网络、数据存取等 |
| Schedulers.newThread() | 新的线程 |
| AndroidSchedulers.mainThread() | Android主线程 |

其中Android主线程的参数 `AndroidScheduler.mainThread()`
需要添加以下依赖：
```java
compile 'io.reactivex:rxandroid:1.2.1'    
```

在上面的例子中加上 `subscribeOn(Schedulers.io()` 、 `observeOn(AndroidSchedulers.mainThread())` 两行代码，即可完成在后台执行耗时操作，在主线程更新界面的线程切换。

```java
Observable.just(1, 2, 3)
        .subscribeOn(Schedulers.io()) // 在IO线程上调用，执行耗时操作
        .observeOn(AndroidSchedulers.mainThread()) // 在主线程回调
        .subscribe(new Subscriber<Integer>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                e.printStackTrace();
            }

            @Override
            public void onNext(Integer integer) {
                System.out.println("onNext value：" + integer );
            }
        });

// 执行以上代码，会打印如下信息：
// onNext value：1
// onNext value：2
// onNext value：3
// onCompleted
```

### 变换
RxJava 是基于事件流的响应式编程，事件流的变化是其核心功能之一。
变换，就是对输入的事件进行加工处理后输出另一个事件。例如上面例子输入的事件是 `1` ，`2` ，`3` ，需要输出其对应的英文单词 `one` ，`two` ，`three` ，将 `1` 转为 `one` 的过程就叫做变换。

RxJava 常用的变换操作符有 `map` 和 `flatMap` 。

* __map__ ：对 `Observable` 发射的每一项数据应用一个函数，执行变换操作，然后返回一个发射这些结果的`Observable`
* __flatMap__：将一个发射数据的 `Observable` 变换为多个（也可以是一个，只返回一个效果等同于map） `Observable` ，然后将它们发射的数据合并后放进一个单独的 `Observable` ，只需订阅最后一个`Observable` 即可。

看不懂？没关系，接着往下看。

map 示例：
```java
Observable.just(1, 2, 3)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .map(new Func1<Integer, String>() { // 将数字变换为英文字符串
            @Override
            public String call(Integer integer) {
                String en = "";
                switch (integer) {
                    case 1:
                        en = "one";
                        break;
                    case 2:
                        en = "two";
                        break;
                    case 3:
                        en = "three";
                        break;
                }
                return en;
            }
        })
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                e.printStackTrace();
            }

            @Override
            public void onNext(String word) {
                System.out.println("onNext value：" + word);
            }
        });

// 执行以上代码，会打印如下信息：
// onNext value：one
// onNext value：two
// onNext value：three
// onCompleted
```

flatMap 示例：
```java
Observable.just("Hello world")
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())

        // 输入的一个事件 “Hello world”,输入两个事件：“hello”和“world”
        .flatMap(new Func1<String, Observable<String>>() {
            @Override
            public Observable<String> call(String s) {
                return Observable.from(s.split(" "));
            }
        })
        .subscribe(new Subscriber<String>() {
            @Override
            public void onCompleted() {
                System.out.println("onCompleted");
            }

            @Override
            public void onError(Throwable e) {
                e.printStackTrace();
            }

            @Override
            public void onNext(String word) {
                System.out.println("onNext value：" + word);
            }
        });
// 执行以上代码，会打印如下信息：
// onNext value：Hello
// onNext value：world
// onCompleted
```

通过两个示例可以看出，`map` 操作符返回的是具体是事件（例子中返回的是String），`flatMap` 操作符返回的是`Observable`（例子中是返回Observable）。

### 防止内存泄露
RxJava在订阅 `Observable` 时会保存一个引用，如果没有在合适的时机取消订阅，则可能会导致内存泄露。例如在Activity里执行网络请求，那么应该在 `onPause()` 中取消订阅。

取消订阅的方法如下：
```java
// 订阅时返回 Subscription，可用于取消订阅
Subscription subscription = Observable.just(1).subscribe();

// 取消订阅前，先判断是否还是订阅状态
if (subscription != null && !subscription.isUnsubscribed()) {
    subscription.unsubscribe();
}
```


第三方库 [RxLifecycle](https://github.com/trello/RxLifecycle) 提供与Activity、Fragment生命周期相关的方法，自动取消订阅，能够有效的防止RxJava未取消订阅而导致的内存泄露。

## 有用的库

结合这些库使用，RxJava能变的更加强大。

* [Retrofit](https://square.github.io/retrofit/)__：__http请求库
* [RxLifecycle](https://github.com/trello/RxLifecycle)：根据Android生命周期管理RxJava
* [RxBinding](https://github.com/JakeWharton/RxBinding)：对Android View事件的扩展，它使得你可以对View事件使用RxJava的各种操作
* [RxPermissions](https://github.com/tbruyelle/RxPermissions)：RxJava的方式动态申请权限
* [更多的库...](https://github.com/ReactiveX/RxAndroid/wiki#extensions)

## 参考资料

* [给 Android 开发者的 RxJava 详解](https://gank.io/post/560e15be2dca930e00da1083)
* [ReactiveX/RxJava文档中文版](https://mcxiaoke.gitbooks.io/rxdocs/content/)
* [RxJava Intro](https://guides.codepath.com/android/RxJava)
