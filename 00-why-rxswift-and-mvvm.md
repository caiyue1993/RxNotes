# 为什么使用 RxSwift 以及 MVVM？
先说后者。在最近公司项目的开发中，我越来越意识到 MVC 的不方便：大量业务逻辑代码放置在 ViewController 中，从而导致该部分过于庞大，难以维护。原本的 Model-View-Controller 很容易沦落成 Massive-View-Controller。

所以我选择了 MVVM，这里有一篇[介绍 MVVM](https://github.com/nixzhu/dev-blog/blob/master/2014-06-10-mvvm.md)的文章。

再说前者，为甚要使用 RxSwift 呢？先给出我的答案：MVVM 配合一个绑定机制效果最好，而在 Swift 领域，RxSwift 是首选。

接下来的文章中，将会介绍我在实际使用时的具体实践。