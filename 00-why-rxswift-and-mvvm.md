# Why RxSwift and MVVM？

## Old MVC
不知你是否跟我一样已经厌倦了 MVC：臃肿的 ViewController 中充斥着大量的业务逻辑代码，各个模块间耦合严重，直接导致的后果是难以维护。

然而其实呢，MVC 是 Apple 推荐的设计模式：[Model-View-Controller](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)。下图是推荐的（也是我们理想中的）MVC：
![ExpectedMVC.png](https://i.loli.net/2017/08/31/59a82af4a5d43.png)

在实际使用中，经常会发展成这个样子：

![Reality.png](https://i.loli.net/2017/08/31/59a82af531c9c.png)

View 和 Model 耦合在了一起（你可以检查一下自己的代码，View 是否和 Model 无依赖？）。经历过的人相信已经会心一笑了。

## New MVVM（feat.RxSwift）
MVVM 可以比较好的解决这一问题，它的模式是这样的：

![MVVM.png](https://i.loli.net/2017/08/31/59a829d908db8.png)

这里有一篇[介绍 MVVM](https://github.com/nixzhu/dev-blog/blob/master/2014-06-10-mvvm.md)的文章，翻译的很好。另外，入门推荐的视频还有 Swift Talk 上的[View Models at Kickstarter](https://talk.objc.io/episodes/S01E47-view-models-at-kickstarter)。

简单的来说，在 MVVM 中，ViewModel 在 View（包括 ViewController，下同） 和 Model 之间搭建起了一个“桥梁”。具体来说：
- ViewModel 是拥有 Model 的，那么当 Model 变化的时候，ViewModel 也会随之改变；而一旦 ViewModel 改变，View 也同样需要更新。
- View 产生的行为（例如用户的点击动作）也需要传递给 ViewModel。 

桥梁上要是有“动车”就好了：MVVM 配合一个绑定机制效果就好了。而在 Swift 领域，RxSwift 算是首选（备选有 ReactiveCocoa 等）。

如果你现在对 RxSwift 一无所知，这里有一些相关链接：

1. [RxSwift GitHub]（https://github.com/ReactiveX/RxSwift）
2. Ray Wenderlich 出的这本[RxSwift: Reactive Programming with Swift](https://store.raywenderlich.com/products/rxswift) 质量很高，推荐购买

以后的文章中开始细谈一些具体实践。
