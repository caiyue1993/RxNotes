# 谈谈 Subject 

## 再看 Observable
再谈 Subject 是什么之前，先回头看一下 Observable。

Observable，字面意思也知道：“可观测的”。最常见的用例是：
```swift
anObservable.subscribe(onNext: { (element) in
        // Handle with element
    }, onError: { (error) in
        // Do something when error occured
    }, onCompleted: { 
        // Do something when completed
    }).addDisposableTo(bag)
```
Observable 通过 subscribe，并在闭包中对**接收**到的相应事件（next, error, completed 等）进行处理。

那么问题来了，Observable 是在什么时候接收到这些事件的呢？

来看看 Observable 的创建方法：
```swift
Observable<String>.create { (observer) -> Disposable in
    observer.onNext("1")
    observer.onCompleted()
    observer.onNext("2")   
    return Disposables.create()
}
```
（在这里，observer 是 AnyObserver 类型，它可以将值加入到 Observable sequence 中）
通过上述代码，我们用了最原始的创建方法创建了一个 Observable。（当然在 Subscribe 中，第二个 next 事件是不会发出的）

那么你应该也就知道了，Observable 的 just 以及 from 等一些快捷创建 Observable 的方法等，本质上都是对原始创建方法的封装。

## 还有 Observer 
Observer，“观察者”。它和 Observable 的关系是：

An observer subscribes to an Observable. 
(在 RxCocoa 中，An observable binds to an observer.)

> 通俗的来说，Observer 会根据 Observable 发出的事件做出相应的行为。

## Subject
如果理清了 Observer 和 Observable 的关系，那么就明白了 Subject ：

**Subject 既是一个 Observer 也是一个 Observable。**

分开来看：因为 Subject 是 Observer，那么它可以订阅（Subscribe）到一个或多个 Observable 上；又因为它是 Observable，那么它可以传递它观察到的事件或值（当 next 事件时）。

