# Why RxSwift and MVVM？

## Old MVC
MVC 其实是 Apple 推荐的设计模式：[Model-View-Controller](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)：

![ExpectedMVC.png](https://i.loli.net/2017/08/31/59a82af4a5d43.png)

但是呢，在实际使用中，经常会发展成这个样子：

![Reality.png](https://i.loli.net/2017/08/31/59a82af531c9c.png)

1. View 常常持有 Model（你可以检查一下自己的代码，View 是否可以在别处直接复用？）
2. View 和 Controller 严重地绑在一起，直接导致在一个 ViewController 中充斥着大量的 UI 和业务逻辑代码

其后果是难以维护，最终失去控制。那么，是否存在一种更好的设计方案呢？

## New MVVM（feat.RxSwift）
MVVM 可以比较好的解决 MVC 中暴露出来的问题，它的模式是这样的：

![MVVM.png](https://i.loli.net/2017/08/31/59a829d908db8.png) 

其优点有：
1. UI 和业务逻辑解耦，代码均匀的分散，使用灵活
2. MVVM 提供了更好的封装性，业务逻辑放在了 ViewModel 中，View 只关心 UI（以及必要的渲染数据） 

其中比较核心的是 ViewModel ，它在 View/ViewController 和 Model 之间搭建起了一个“桥梁”。具体来说：
- ViewModel 是拥有 Model 的，那么当 Model 变化的时候，ViewModel 也会随之改变；而一旦 ViewModel 改变，View 也同样需要更新（值得注意的是，这是单向绑定，唐巧在他的[被误解的 MVC 和被神化的 MVVM](http://blog.devtang.com/2015/11/02/mvc-and-mvvm/)说这是双向绑定是有问题的）。
- View 产生的行为（例如用户的点击动作）也需要传递给 ViewModel 去处理。 
- 那么在哪里进行这些绑定呢？答案是：ViewController。

桥梁上要是有“动车”就好了：MVVM 配合一个绑定机制效果就好了。而在 Swift 领域，RxSwift 算是首选（备选有 ReactiveCocoa 等）。

RxSwift 的强大之处将在之后的文章中详细阐述，以下提供了相关链接供上手学习：

1. [RxSwift 官方 repo](https://github.com/ReactiveX/RxSwift)
2. [RxSwift: Reactive Programming with Swift](https://store.raywenderlich.com/products/rxswift) Ray Wenderlich 出的一本不可多得的好书
3. [View Models at Kickstarter](https://talk.objc.io/episodes/S01E47-view-models-at-kickstarter) 
4. [介绍 MVVM](https://github.com/nixzhu/dev-blog/blob/master/2014-06-10-mvvm.md) Nix 早期翻译的一篇关于 MVVM 的文章
5. [使用 RxSwift 进行响应式编程](https://academy.realm.io/cn/posts/altconf-scott-gardner-reactive-programming-with-rxswift/)
