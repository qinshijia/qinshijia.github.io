---
title: RxJava debounce操作符防止界面刷新过度
date: 2018-10-25 15:12:11
tags: RxJava
categories: RxJava
description: 有些场景下APP需要根据数据实时刷新界面，数据刷新过快，界面跟着刷新可能会出现界面卡顿的问题。特别是在界面显示的元素比较多绘制时间比较长、刷新界面需要执行动画等情况下，就会导致界面卡顿。刷新过快的时候，中间的一些数据是可以过滤掉，只要根据最后一个数据刷新界面就可以了。本文介绍如何通过使用同一个Observable发射事件并使用debounce过滤掉发射速率过快的数据项。
---

有些场景下APP需要根据数据实时刷新界面，数据刷新过快，界面跟着刷新可能会出现界面卡顿的问题。特别是在界面显示的元素比较多绘制时间比较长、刷新界面需要执行动画等情况下，就会导致界面卡顿。刷新过快的时候，中间的一些数据是可以过滤掉，只要根据最后一个数据刷新界面就可以了。

解决这个问题需要分为两个步骤：
1. 发射事件：收到数据时，使用同一个 `Observable` 发送更新事件
2. 过滤事件：过滤发射频率过快的数据，处理最后一项即可

### 发射事件
RxJava 发射事件的方式是通过 `subscriber.onNext()` ，每调用一次这个方法就会发射一个事件。
```java
Observable.create(subscriber -> subscriber.onNext("emit"));
```

将 `subscriber` 保存为一个全局变量，收到数据时调用`subscriber.onNext()`  即可发射事件通知刷新界面。
```java
private Subscriber<? super Object> mSubscribe;

private void initUIObservable() {
    Observable.create(subscriber -> mSubscribe = subscriber)
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(i -> Log.d(TAG,"refresh UI"));
}

private void receiveData() {
    mSubscribe.onNext("emit");
}
```

### 过滤事件
通过RxJava的操作符 `debounce` 过滤掉发射速率过快的数据项，防止界面刷新频率过快。

> `debounce` 产生一个新的 `Observable` ，这个 `Observable` 只发射原 `Observable` 中时间间隔小于指定阈值的最大子序列的最后一个元素。 参考资料：Debounce[参考资料：Debounce](https://link.juejin.im/?target=http%3A%2F%2Freactivex.io%2Fdocumentation%2Foperators%2Fdebounce.html)

通过例子看看 `debounce` 的实际作用。
```java
private void debounceTest() {
    Observable.interval(100, TimeUnit.MILLISECONDS)
            .filter(i -> i < 6)
            .doOnNext(i -> Log.d("RxJava", "emit num :" + i)) // 发射的数字
            .debounce(300, TimeUnit.MILLISECONDS)
            .subscribe(i -> Log.d("RxJava", "receive num :" + i)); // 接收到的数字
}
```

输出为：


![image.png | left | 570x265](https://cdn.nlark.com/yuque/0/2018/png/164396/1540459205381-88ed9553-703e-4ab3-9f15-d19f7596dd6d.png "")


这里例子中，每100ms发射一个数据，`debounce` 设置的时间阈值为300，这时 `subscribe` 只接收到了最后一个事件。

如果把 `interval` 的时间间隔改成500ms，`subscribe` 就可以接收到所有事件。


![image.png | left | 557x449](https://cdn.nlark.com/yuque/0/2018/png/164396/1540459464513-8975d733-87cb-4782-894c-b7a2f0483ccc.png "")


### 总结
把发射事件和过滤事件结合到一起，即可解决因为数据等原因导致界面刷新过快的问题。整理之后的代码如下：
```java
private Subscriber<? super Object> mSubscribe;

// 初始化界面观察者
private void initUIObservable() {
    Observable.create(subscriber -> mSubscribe = subscriber)
            .subscribeOn(Schedulers.io())
            .debounce(300, TimeUnit.MILLISECONDS)
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(i -> Log.d(TAG,"refresh UI"));
}

// 接收到数据，通知刷新界面
private void receiveData() {
    mSubscribe.onNext("emit");
}
```
