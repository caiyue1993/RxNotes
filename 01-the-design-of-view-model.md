# View Model
一般来说，ViewModel 可以拆成四个部分：Inputs, Outputs, Init 和 Methods。

Inputs 用于接收外部的状态值；Outputs 大多是 Observable，暴露出来交给外面去监听后续事件；Init 是初始方法不必多说，它的作用是对当前 ViewModel 在初始化完成之赋予每个变量合适的初始值；最后一个是 Methods，可以对 Inputs 和 Outputs 之间进行相应的绑定操作。

附上一张实际开发的截图，可以发现，在 ViewModel 中我按顺序标上了 MARK：







