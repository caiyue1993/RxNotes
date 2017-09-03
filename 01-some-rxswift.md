# 捋清 RxSwift 中的一些概念

关于 RxSwift 的入门学习，在[官方 GitHub 仓库](https://github.com/ReactiveX/RxSwift)上已经提供了很多文档。另外，同样有对应的[中文文档](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/)释出。

本篇是阐述我认为的几个比较重要的概念。

## Side effects 
Side effects 指的是由于更改了当前作用域之外的共享状态值，从而产生的“副作用”。例如，你可能在实际项目里写这样的代码：
```swift
override func viewDidLoad(_ animated: Bool) {
  super.viewDidLoad(animated)
  
  setupUI()
  bindUI()
  listenForChanges()
}
```
在 bindUI 中，你可能给特定的 UI 控件绑定了相应的动作。那么，这就产生了一个 side effect：你的 app 在 bindUI 之前以及之后产生了不一样的行为。但是需要明确的是，产生 side effect 并非是不好的！毕竟我们就是需要这些 side effects 从而达到我们想要的效果。我们只是需要对这些 side effects 有一个合理的控制（in a controlled way）。

再延伸一点，在**命令式编程(Imperative Programming)**中，常会更改外部的共享状态值。而在**函数式编程**中，是不会更改外部的共享状态值的，从而也就不会产生 side effects。而在 RxSwift 中，结合了二者的优点，通过其典型的注册（Subscribe）的方式，以有顺序、声明式的方式对不可更改的数据做处理，提供了一种解决异步编程的优雅方案。

## Observable
Observable 应当是 RxSwift 里最核心的概念。它可以 emit 三种事件（Event）：

附图。

值得注意的一点是，Observable 只有被 subscribe 之后才会真正地 emit event。另外，Observable 接收到 terminated 事件的时候，当前对应的 Subscription 会被 dispose 掉。

## Operator
[ReactiveX](http://reactivex.io/) 提供了众多 Operator 运算符，RxSwift 当然也不例外。这些运算符基本涵盖了日常开发时的运用场景，例如 map, filter, skip 等等，详细的介绍以及实例在[官网](http://reactivex.io/documentation/operators.html)上也有。在这里比较一下 map 和 flatMap 在 Swift 和 RxSwift 中的不同。

### Map
在 Swift 中，Map 是 Sequence 协议中的方法，其作用是对 Sequence 中的元素按照给定的规则分别进行映射，并返回映射后的 Sequence。拿 Array 举例：
```Swift
 [1,2,3].map { (num) -> String in
     return "\(num)"
 }
```
上述 map 函数将会返回 ["1","2","3"]。

在 RxSwift 中，

