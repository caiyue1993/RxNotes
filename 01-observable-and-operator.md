# 关于 Observable 和 Operator 的一些认识

关于 RxSwift 的入门学习，在[官方 GitHub 仓库](https://github.com/ReactiveX/RxSwift)上已经提供了很多文档。另外，同样有对应的[中文文档](https://beeth0ven.github.io/RxSwift-Chinese-Documentation/)释出。

本篇是谈一下我对 Observable 和 Operator 的一些认识。先提一点题外的，关于 side effects。

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

再延伸一点，在 **命令式编程(Imperative Programming)** 中，常会更改外部的可变状态值。而在**函数式编程**中，是不会更改外部的可变状态值的，从而也就不会产生 side effects。而在 RxSwift 中，结合了二者的优点，通过其典型的注册（Subscribe）的方式，以有顺序、声明式的方式对不可更改的数据做处理，提供了解决异步编程难题的一种优雅方案。在深入探讨 RxSwift 之前，需要对 Observable 和 Operator 有个清晰的认识。

## Observable
Observable 应当是 RxSwift 里最核心的概念。它可以发出（emit）三种事件（event）：

![Observable.png](https://i.loli.net/2017/09/03/59ac24b0d80fa.png)

值得注意的一点是，Observable 只有被 subscribe 之后才会真正地发出事件。另外，Observable 接收到 terminated 事件的时候，当前对应的 Subscription 会被 dispose 掉。

## Operator
[ReactiveX](http://reactivex.io/) 提供了众多 Operator 运算符，RxSwift 当然也不例外。这些运算符基本涵盖了日常开发时的运用场景，例如 map, filter, skip 等等，详细的介绍以及实例在[官网](http://reactivex.io/documentation/operators.html)上也有。在这里着重比较一下 map 和 flatMap 在 Swift 和 RxSwift 中的不同。
### Map & FlatMap
#### Map
在 Swift 中，map 是 Sequence 协议中的方法，其作用是对 Sequence 中的元素按照转换函数分别进行映射，并返回映射后的 Sequence。拿 Array 举例：
```Swift
 [1,2,3].map { (num) -> String in
     return "\(num)"
 }
```
上述 map 函数将会返回 ["1","2","3"]。

在 RxSwift 中，map 将 Observable 中发出 next 事件中的 element 按照给定的转换函数进行映射，并返回映射后的 Observable。用图来解释就是：

![map.png](https://i.loli.net/2017/09/03/59ac24cb23029.png)

#### FlatMap
关于 Swift 中 flatMap 是什么，我推荐看[flatMap 温顾知新 —— 参照 Swift 源码实现讲解](http://www.jianshu.com/p/7deadf85a5a5)这篇文章，解释地十分详细；进阶版解释可以看[Swift 烧脑体操（四） - map 和 flatMap](http://www.infoq.com/cn/articles/swift-brain-gym-map-and-flatmap#)。

个人觉得 RxSwift 中的 flatMap 稍微复杂了一点，ReactiveX 的官方文档是这么定义它的：

> FlatMap: transform the items emitted by an Observable into Observables, then flatten the emissions from those into a single Observable

比方说，当一个 Observable 拥有一个 Observable 的子属性时，flatMap 会对子 Observable 的 element 进行变换，然后将变换后的子 Observable 重新展平（flatten）成一个 Observable 并返回（根据定义，当然也可以是一个 Observable 发出的普通的值在 flatMap 中被转换成 Observable，然后这些 Observable 被展平成一个 Observable）。举一个例子：

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
将会打印出 80,85,90,95,100。也就是说，最终的效果是，它同时可以“监听”多个 Observable 中的子 Observable 产生的变化，用图表示是这个样子（为了方便手绘）：

![flatmap.png](https://i.loli.net/2017/09/04/59ac29331c790.png)

那么如果你想要只“监听”最新加入的 Observable 的子 Observable 应该怎么办呢？RxSwift 提供了 flatMapLatest 方法，也就是说，改用 flatMapLatest 后上面的输入将会打印出 80,85,90,100。

特别的，flatMap 还适合解决异步返回的问题：（例子来源于[靛青K](https://medium.com/@DianQK/rxswift-%E4%B8%8B%E7%9A%84-map-%E4%B8%8E-flatmap-d0b319aef819)）
```swift
Observable.just(1)
		.map { $0 * 2 }
		.flatMap { value -> Observable<String> in
			return Observable.create { observer in
				DispatchQueue.main.async {
					observer.onNext(String(value))
					observer.onCompleted()
				}
			}
		}
```
这也是为什么在 RxSwift 的实际使用中，flatMap 出镜率比较高的原因之一。

### CombineLatest & Zip
比较完了 RxSwift 中的 flatMap 和 Map，再看另一对出场率很高的 combineLatest 以及 zip。在实际开发过程中，常会遇到这样的情况：我们得等到两个或两个以上的 Observable 接受到输入之后，再进行输出。
#### CombineLatest
先看 CombineLatest 的示例图：
![combineLatest.png](https://i.loli.net/2017/09/06/59af441b5e43e.png)

分别拿到两个 Observable 最新发出的 element，并通过 CombineLatest 中的函数（也就是下面代码中的 resultSelector）转成另一个值，最后返回包含这个值的 Observable。（从图中可以知道，只要有最新的 element 都会发出事件）

直接看 CombineLatest 的源码定义：
```swift
public static func combineLatest<O1: ObservableType, O2: ObservableType>
        (_ source1: O1, _ source2: O2, resultSelector: @escaping (O1.E, O2.E) throws -> E)
            -> Observable<E> {
        return CombineLatest2(
            source1: source1.asObservable(), source2: source2.asObservable(),
            resultSelector: resultSelector
        )
    }
```
#### Zip
直接用一张图解释区别吧。（在这时真的感受到一图胜千言...）

![zip.png](https://i.loli.net/2017/09/06/59af468078ad1.png)


