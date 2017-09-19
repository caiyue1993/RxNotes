# 善于使用 RxCocoa

其实到这里，如果你熟悉 Observable 以及常见的 Operator 的使用方法，那么已经可以满足实际项目中大部分的需求场景了（其实*垃圾回收*也值得一提，将在之后的文章中进行阐述）。

但是呢，为了将 RxSwift 在实际使用中贯彻地更加纯粹，我们还可以使用 Rx 针对 Cocoa Framework 的封装: [RxCocoa](https://github.com/ReactiveX/RxSwift/tree/master/RxCocoa)。

# 例子
先从一个例子看看最常见的一个场景：根据用户输入进行查询（searchCityName 是一个 UITextField）。
```swift
let search = searchCityName.rx.text
	  .filter { ($0 ?? "").characters.count > 0 }
	  .flatMapLatest { text in
		// 下面的代码对 text 发送 api 请求，并返回 Observable<ApiUtil.Weather>
		return ApiUtil.shared.currentWeather(city: text ?? "Error")
		  .catchErrorJustReturn(ApiUtil.Weather.empty)
	  }
```
上述代码返回的是一个 Observable<ApiUtil.Weather>。

为什么使用 flatMapLatest？因为在一般情况下，用户在输入一个地名之前，往往都会输入其它的字符。那么我们需要的是获取最新的输入并对其进行网络请求的封装并观察。还有疑问的，建议回到上一节再研究一下 flatMap 和 flatMapLatest 的区别。

# bind to
首先需要明确的是，在 RxCocoa 中的绑定都是**单向**的。在实际使用中，可以将一个 Observable 绑定到一个 Observer 上（准确的说是实现 ObserverType 协议的实体）上。

在上面代码的例子中继续看：
```swift
search.map { $0.cityName }
  .bindTo(cityNameLabel.rx.text)
  .addDisposableTo(bag)
```
现在，从网络中拿到的 cityName 就可以顺利地绑定到 cityNameLabel 上了。值得注意的是，这是一个往 UI 控件上的绑定行为，RxCocoa 会检测这一过程是否在主线程进行，如果不在主线程，那么将会闪退。

其实，bindTo 就是一个特殊的 subscribe，特别的：调用 bindTo 时不会产生任何的 side effects。







