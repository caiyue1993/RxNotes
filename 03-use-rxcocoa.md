# 善于使用 RxCocoa

其实到这里，如果你熟悉 Observable 以及常见的 Operator 的使用方法，那么已经可以满足实际项目中大部分的需求场景了（其实垃圾回收也值得一提，将在之后的文章中进行阐述）。

但是呢，为了将 RxSwift 在实际使用中贯彻地更加纯粹，我们还可以使用 Rx 针对 Cocoa Framework 的封装 [RxCocoa](https://github.com/ReactiveX/RxSwift/tree/master/RxCocoa)。

# 例子
先从一个例子看看最常见的一个场景：根据用户输入进行查询。
```swift
searchCityName.rx.text
      .filter { ($0 ?? "").characters.count > 0 }
      .flatMapLatest { text in
        // 下面的代码对 text 发送 api 请求，并返回 Observable<ApiUtil.Weather>
        return ApiUtil.shared.currentWeather(city: text ?? "Error")
          .catchErrorJustReturn(ApiUtil.Weather.empty)
      }
```
上述代码返回的是一个 Observable<ApiUtil.Weather>。

为什么使用 flatMapLatest？因为在一般情况下，用户在输入一个地名之前，往往都会输入其它的字母。那么我们需要的是获取最新的输入并发送网络请求，而不是对之前的输入也发送请求。（还有疑问的建议回到上一节比较一下 flatMap 和 flatMapLatest 的区别。）

https://academy.realm.io/cn/posts/altconf-scott-gardner-reactive-programming-with-rxswift/








