# 为什么使用 RxSwift 以及 MVVM？
先说后者。在最近公司项目的开发中，我越来越对 MVC 接受不能：大量业务逻辑代码放置在 ViewController 中，导致该部分过于庞大，难以维护。原本的 Model-View-Controller 最终都沦落成 Massive-View-Controller。

（在这里放上 Keynote 里的那张图：苹果推荐的和实际的）

再说前者，为甚要使用 RxSwift 呢？
先放一个链接，nixzhu 写的一篇文章 ：[关于Rx与测试驱动开发的不成熟的小感悟](https://spacehub.duodian.com/spaces/sfd0a19516c/post/49b946e57e4da3230237488f16fe5bc5)。在该文的末尾，作者提出了这样一个感想：“若只是用Rx替换一些Target-Action模式或通知之类的实在是大材小用（这也是我保持观望的原因之一）。”

而在这里我想回答一下，RxSwift 如果和 MVVM 合作，威力可以发挥到最大。下面细谈原因。