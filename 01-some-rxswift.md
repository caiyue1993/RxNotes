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
