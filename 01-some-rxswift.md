# 捋清 RxSwift 中的一些概念

关于 RxSwift 的入门学习，在[官方 GitHub 仓库](https://github.com/ReactiveX/RxSwift)上已经提供了很多文档。另外，同样有对应的[中文文档](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/)释出。

本篇是阐述我认为的几个比较重要的概念。

## Side effects 
Side effects 指的是更改了当前作用域之外的共享状态值。例如，你可能在实际项目里写这样的代码：
```swift
override func viewDidLoad(_ animated: Bool) {
  super.viewDidLoad(animated)
  
  setupUI()
  bindUI()
  listenForChanges()
}
```
在 bindUI 中，你可能给特定的 UI 控件绑定了相应的动作。那么，这就产生了一个 side effect：你的 app 在 bindUI 之前以及之后产生了不一样的行为。但是需要明确的是，产生 side effect 并非是不好的！毕竟我们就是需要这些 side effects 从而达到我们想要的效果。我们只是需要对这些 side effects 有一个合理的控制（in a controlled way）。

