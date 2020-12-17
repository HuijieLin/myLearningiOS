# 卡顿监控

[http://www.tanhao.me/code/151113.html/](http://www.tanhao.me/code/151113.html/)

> ## 监控思路

* 使用`CFRunLoopObserverRef`给runloop添加观察者，观察runloop的所有状态变化
* 另外开辟一个子线程来计算`kCFRunLoopBeforeSourcesh`和`kCFRunLoopAfterWaiting`这两个状态区域之间的耗时是否达到阀值来判断是否卡顿
* 为了主线程的状态变更能够及时通知到子线程，可以使用dispatch\_semaphore\_t来处理
* 在主线程开启监控的方法里创建信号量，并且使用`dispatch_semaphore_wait`来进行加锁同时设置超时时间，如果触发超时就代表卡顿，`dispatch_semaphore_wait`在超时的情况下是返回`!=0`，然后在runloop的状态变化的时候调用`dispatch_semaphore_signal`来解锁
* 卡顿的情况包括连续多次小卡顿和一次大卡顿，所以在`dispatch_semaphore_wait`设置的超时时间为一次小卡顿时间（50ms），大卡顿暂时设置为（250ms），所以只要统计连续触发5次超时就表示有卡顿发生
* 堆栈的收集使用的是PLCrashReport

新方案

[https://mp.weixin.qq.com/s/vMRQ0VuHLxpaY9oCNd5G8w](https://mp.weixin.qq.com/s/vMRQ0VuHLxpaY9oCNd5G8w)

