# 提到 Observable 和 Operator 时需要注意的

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

![Observable.png](https://i.loli.net/2017/09/03/59ac24b0d80fa.png)

值得注意的一点是，Observable 只有被 subscribe 之后才会真正地 emit event。另外，Observable 接收到 terminated 事件的时候，当前对应的 Subscription 会被 dispose 掉。

## Operator
[ReactiveX](http://reactivex.io/) 提供了众多 Operator 运算符，RxSwift 当然也不例外。这些运算符基本涵盖了日常开发时的运用场景，例如 map, filter, skip 等等，详细的介绍以及实例在[官网](http://reactivex.io/documentation/operators.html)上也有。在这里比较一下 map 和 flatMap 在 Swift 和 RxSwift 中的不同。

### Map
在 Swift 中，map 是 Sequence 协议中的方法，其作用是对 Sequence 中的元素按照转换函数分别进行映射，并返回映射后的 Sequence。拿 Array 举例：
```Swift
 [1,2,3].map { (num) -> String in
     return "\(num)"
 }
```
上述 map 函数将会返回 ["1","2","3"]。

在 RxSwift 中，map 将 Observable 中发出 next 事件中的 element 按照给定的转换函数进行映射，并返回映射后的 Observable。用图来解释就是：

![map.png](https://i.loli.net/2017/09/03/59ac24cb23029.png)

### FlatMap
关于 Swift 中 flatMap 是什么，我推荐看[flatMap 温顾知新 —— 参照 Swift 源码实现讲解](http://www.jianshu.com/p/7deadf85a5a5)这篇文章，解释地十分详细；进阶版解释可以看[Swift 烧脑体操（四） - map 和 flatMap](http://www.infoq.com/cn/articles/swift-brain-gym-map-and-flatmap#)。

这里着重提一下 RxSwift 中的 flatMap，ReactiveX 的官方文档是这么定义它的：

> FlatMap: transform the items emitted by an Observable into Observables, then flatten the emissions from those into a single Observable

也就是说，当一个 Observable 拥有一个 Observable 的子属性时，flatMap 会对子 Observable 的 element 进行变换，然后将变换后的子 Observable 重新组合成一个 Observable 并返回。举一个例子：

我们有一个 Student 模型，它的结构是这样的：
```swift
struct Student {
  var score: Variable<Int>
}
```
在下面的代码中，定义了两个学生 ryan 和 charlotte。而 student 这个 Subject 它是 Student 类型的（Subject 是什么在以后会提到，你现在只需要知道它既是一个 Observer 也是一个 Observable），并对其进行了 flatMap 以及 subscribe，其作用是打印出这个学生的成绩：
```swift
  let ryan = Student(score: Variable(80))
  let charlotte = Student(score: Variable(90))
  
  let student = PublishSubject<Student>()

  student.asObservable()
    .flatMap {
      $0.score.asObservable()
    }
    .subscribe(onNext: {
      print($0)
    })
    .addDisposableTo(disposeBag)
```
在之后的时间里，如果发生了下面的事件：
```swift
  student.onNext(ryan)
  ryan.score.value = 85

  student.onNext(charlotte)
  ryan.score.value = 95

  charlotte.score.value = 100
```
将会打印出 80,85,90,95,100。也就是说，最终的效果是，它同时可以“监听”多个 Observable 中的子 Observable 产生的变化，用图表示是这个样子（为了方便手绘了）：

![flatmap.png](https://i.loli.net/2017/09/04/59ac29331c790.png)

那么如果你想要只“监听”最新加入的 Observable 应该怎么办呢？RxSwift 提供了 flatMapLatest 方法，也就是说，改用 flatMapLatest 后上面的输入将会打印出 80,85,90,100。







