
原文链接：[NSRunLoop简介][1]

#### RunLoop的作用

- 程序持续运行的保证，如果RunLoop不存在了，程序也就结束运行了。
- 在循环中处理各种事件，如触摸事件、定时器事件、Selector事件。
- 节省CPU的资源，在需要执行任务的时候被唤醒，当没有任务执行的时候进入休眠状态。

#### 程序启动的流程

- 执行main函数
- 执行UIApplicationMain函数
	- 指定UIApplication对象
	- 指定UIApplication的代理
- 创建UIApplication对象，并且指定它的代理。
- 创建一个主运行循环(RunLoop)，这是一个死循环，保证程序的持续运行。
- 加载配置了所有应用程序信息的info.plist文件
- 应用程序启动完毕

#### NSRunLoop和CFRunLoopRef

- CFRunLoopRef是在CoreFoundation框架中的，它的内部API以及实现，都是纯C语言编写，这些API都是线程安全的。
- NSRunLoop是基于CFRunLoopRef的封装，它提供了面向对象的API，但是这些API不是线程安全的。
- 这两个对象的地址不同，因为它们的对象来自于完全不同的类。
- CFRunLoop可以调用getCfRunLoop方法，将NSRunLoop转化为CFRunLoop。

#### RunLoop与线程

- 每一条线程，都有一个与之相对应的RunLoop对象，负责处理线程中的任务。
- 主线程和RunLoop：RunLoop在程序已经启动的时候就创建好了
- 子线程和RunLoop：
	- 子线程会单独开启RunLoop去执行任务
	- 子线程和RunLoop是一一对应的关系，每个子线程都有自己的RunLoop（但需要主动创建）
	- 创建子线程的RunLoop：`[NSRunloop currentRunLoop] `
	- 子线程的RunLoop需要手动开启：` [[NSRunLoop currentRunLoop] run] `

#### RunLoopMode

- RunLoop的运行模式
	- 一个RunLoop至少要指定一个运行模式，当运行模式指定之后，至少有一个Source或者Timer任务在执行。
	- RunLoop启动之后，只能指定一个运行模式，可以使用currentMode来获取。
	- 如果要切换RunLoop的运行模式，就要先退出当前的RunLoop，重新指定Mode再次进入运行。

- 系统默认注册的5个Mode
	- kCFRunLoopDefaultMode App的默认Mode，通常主线程是在这个Mode下运行的。
	- UITrackingRunLoopMode 界面跟踪Mode，用于ScrollView/TableView等追踪触摸滑动，保证界面滑动的时候不受其他Mode影响。
	- UIInitializationRunLoopMode 当App启动时，第一个进入的Mode，启动完成之后就不会再使用这个Mode。
	- GSEventReceiveRunLoopMode 接收系统事件的内部Mode，通常由系统自动管理。
	- kCFRunLoopCommonMode 一个类似于占位的Mode，并不是一个真正的Mode。

#### CFRunLoopSourceRef：事件源、事件、输入等都属于事件源，它有两个分类：

- Source0 非基于Port的事件，用户触发的事件，例如点击事件等。
- Source1 基于Port的事件，他用于系统内部与线程之间交互。

#### CFRunLoopTimerRef：定时器事件

- NSTimer
	- 如果将NSTimer添加到子线程中，需要先创建一个RunLoop，然后再启动RunLoop。
	- NSTimer受到RunLoop的影响，一般会有一些轻微的误差，所以对于精密计时，GCD定时器较为精准。
- GCD定时器：精确到纳秒，较为精准。
	- GCD定时器的创建步骤：创建定时器 -\> 设置定时器 -\> 设置定时器的回调方法 -\> 恢复定时器。
	- GCD定时器一定要添加一个强引用，否则会被立即释放。

#### CFRunLoopObserverRef：观察者

- 观察者可以观察到RunLoop不同的运行状态
- 通过判断RunLoop的运行状态，可以执行一些操作。

[1]:	http://www.jianshu.com/p/8cd7a5dffc09