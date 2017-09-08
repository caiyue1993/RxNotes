# 我理解的 Subject 

再谈 Subject 是什么之前，先回头看一下 Observable。

## 再看 Observable
Observable，字面意思：“可观测的”。最常见的用例是：
```swift
anObservable.subscribe(onNext: { (element) in
        // Handle with element
    }, onError: { (error) in
        // Do something when error occured
    }, onCompleted: { 
        // Do something when completed
    }).addDisposableTo(bag)
```
Observable 通过 subscribe，在闭包中对相应事件（onNext, onError, onCompleted 等）进行处理。

那么问题来了，这个 Observable 是在什么时候发出这些事件的呢？

来看看 Observable 的创建方法 create：
```swift
Observable<String>.create { (observer) -> Disposable in
    observer.onNext("1")
    observer.onCompleted()
    observer.onNext("2")   
    return Disposables.create()
}
```
（在这里，observer 是 AnyObserver 类型（不是 Observer!），它可以将值加入到 Observable 的 event sequence 中）
通过上述代码，我们用了最原始的创建方法创建了一个 Observable。（当然被 Subscribe 了之后，第二个 next 事件是不会发出的，因为已经被 terminated）

通过看源码可以发现，Observable 的 just 以及 from 等一些快捷创建 Observable 的方法等，本质上都是对原始创建方法的封装。

## 还有 Observer 
Observer，“观察者”。它和 Observable 的关系是：

An observer subscribes to an Observable. 
(在 RxCocoa 中，An observable binds to an observer.)

> 通俗的来说，Observer 会根据 Observable 发出的相应事件做出相应的行为。

关于二者的关系，画了一个示意图：

![Image-1.jpg](https://i.loli.net/2017/09/08/59b16d006db04.jpg)

## Subject
如果理清了 Observer 和 Observable 的关系，那么就明白了什么是 Subject ：

**Subject 既是一个 Observer 也是一个 Observable。**

分开来看：因为 Subject 是 Observer，那么它可以订阅到（Subscribe to）一个或多个 Observable 上；又因为它是 Observable，那么它可以传递（emit）它观察到的事件或值（当 next 事件时）。

Subject 既是一个 Observer 也是一个 Observable 解决了什么问题呢？有了 Subject，那么可以它可以在运行时接收事件（Observer 的特性）;又可以传递观察到的事件，根据不同的事件做出相应的行为（Observable 的特性）。

在后续文章中将会结合 MVVM 设计模式以及具体的实例，再谈。

Stay tuned!

