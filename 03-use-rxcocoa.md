# 善于使用 RxCocoa

[RxCocoa](https://github.com/ReactiveX/RxSwift/tree/master/RxCocoa) 是 RxSwift 对 Cocoa 和 Cocoa Touch Framework （以下统称 Cocoa API） 的封装。

## 例子
先看一个具体的例子。譬如有一个搜索框 searchCityName（它是一个 UITextField），可以根据用户的最新的输入进行查询：

```swift
let search = searchCityName.rx.text
	  .filter { ($0 ?? "").characters.count > 0 }
	  .flatMapLatest { text in
		// 下面的代码对 text 发送 api 请求，并返回 Observable<Weather>
		return Api.shared.currentWeather(city: text ?? "Error")
		  .catchErrorJustReturn(Weather.empty)
	  }
    .observeOn(MainScheduler.instance)
```

我们得到的 search 的类型是一个 Observable<Weather>。
  
为什么使用 flatMapLatest？因为在一般情况下，用户在输入一个地完整的地名之前，往往都会输入其它的字符。那么我们需要的是获取最新的输入，并对其进行网络请求的封装。之后我们只关心这个网络请求的结果。

## bind(to:)
bind(to:) 其实就是 RxCocoa 里的 subscribe(\_:)。需要明确的是，在 RxCocoa 中的绑定都是**单向**的。在实际使用中，可以将一个 Observable 绑定到一个 Observer 上（准确的说其实是一个实现 ObserverType 协议的实体）上。

继续看上面的例子，我们对拿到的 search 进行处理，将放回的 cityName 绑定到 cityNameLabel 上：

```swift
search.map { $0.cityName }
  .bindTo(cityNameLabel.rx.text)
  .addDisposableTo(bag)
```

值得注意的是，这是一个往 UI 控件上的绑定行为，RxCocoa 会检测这一过程是否在主线程进行，如果不在主线程，那么将会闪退。

## Units

RxCocoa 中还有一些特别的 Observable，称为 Units。Units 更适合用来处理与 UI 相关的绑定，它有以下特点：

- Units 不会发出 error 事件
- Units 默认在主线程上被观察
- Units 默认在主线程上注册
- Units share side effects

总结来说，在使用 Units 时会更放心，因为你可能忘记的操作它都帮你做好了。

Units 主要分两种:

1. ControlProperty 和 ControlEvent 
2. Driver

ControlProperty 既是一个 ObservableType 也是 ObserverType，它主要对 Cocoa API 的属性进行封装，例如 UITextField.rx.text。ControlEvent 是一个 ObservableType，它封装了 Cocoa API 中的一些事件，例如按钮点击。

Driver 是一个 Observable，特别地，由于它是一个 Units，那么它就是一个具有 Units 特点的 Observable。

我们可以使用 asDriver(onErrorJustReturn:) 方法将一个 Observable 转成一个 Driver。onErrorJustReturn 参数接收一个当 Observable 发出 error 事件时的默认值。这样也就让 Driver 不再有可能发出 error 事件。

说了这么多，之前文中的代码可以这么优化：

1. 当用户点击 enter 键的时候再发请求
2. 使用 Units 精简代码

如下：

```swift
let search = searchCityName.rx.controlEvent(.editingDidEndOnExit).asObservable()
.map { self.searchCityName.text }
.flatMap { text in 
    return Api.shared.currentWeather(city: text ?? "Error")
}
.asDriver(onErrorJustReturn: Api.Weather.empty)
```

Driver 的绑定使用 drive(:)：
```swift
search.map { $0.cityName }
.drive(cityNameLabel.rx.text)
.addDisposableTo(bag)
```












