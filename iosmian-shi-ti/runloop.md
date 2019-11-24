# Runloop

---

> ## 深入理解Runloop

[https://blog.ibireme.com/2015/05/18/runloop/\#base](https://blog.ibireme.com/2015/05/18/runloop/#base)

> ## Runloop简介

runloop也是一个对象，这个对象是管理着它需要处理的事件和消息，并提供一个入口的开启函数。当线程调用了这个开启函数，这个对象就会一直处于『接受消息 - 等待 - 处理消息』的循环当中，直到循环处理结束，函数才会返回。

iOS平台下有两个这样的对象`NSRunLoop`和`CFRunLoopRef`：

* `NSRunLoop`：是基于`CFRunLoopRef`封装，提供的是面向对象的API，API是非线程安全的。
* `CFRunLoopRef`：是在`CoreFoundation`框架内，纯C的API，API是线程安全的。
* `CFRunLoopRef`源码地址：[https://opensource.apple.com/tarballs/CF/](https://opensource.apple.com/tarballs/CF/)

> ## Runloop和线程的关系

* 每条线程都有唯一的一个RunLoop对象
* RunLoop保存在一个全局的字典里，key = pthread\_t， value = CFRunLoopRef
* RunLoop会在线程结束的时候销毁
* 主线程的RunLoop默认开启，子线程的RunLoop需要手动开启

> ## Runloop内部结构

主要的类：

* **CFRunLoopRef**
* **CFRunLoopModeRef**
* **CFRunLoopSourceRef**
  * 事件发生的地方
  * **Source0**:
    * 只包含一个回调（函数指针），它不能主动触发事件
    * 常见的场景：
      * 触摸事件处理
      * `performSelector:onThread:`
  * **Source1**:
    * 包含一个mach\_port和一个回调（函数指针）
    * 被用于通过内核和其他线程互相发送消息
    * 这个source可以主动唤醒RunLoop
    * 常见的场景：
      * 基于Port的线程间通信
      * 系统事件的捕捉
* **CFRunLoopTimerRef**
  * 基于时间的触发器
  * 其包含了一个时间长度和一个回调，当加入RunLoop的时候，RunLoop会注册对应的时间点，当时间点到的时候，RunLoop会被唤醒处理对应的回调
  * 常见的场景：
    * NSTimer
    * `performSelector:withObject:afterDelay:`
* **CFRunLoopObserverRef**
  * 当RunLoop的状态发生变化的时候，观察者就可以接收到回调

**内部结构（精简）如下：**

```objectivec
typedef struct __CFRunLoop * CFRunLoopRef;

struct __CFRunLoop {
    pthread_t _pthread; // 所在的线程
    CFMutableSetRef _commonModes; // 带有commonMode标记的mode
    CFMutableSetRef _commonModeItems; // 标记了commonMode的时间
    CFRunLoopModeRef _currentMode; // 当前的mode
    CFMutableSetRef _modes; // 所有的mode列表
};

typedef struct __CFRunLoopMode *CFRunLoopModeRef;

struct __CFRunLoopMode {
    CFStringRef _name;
    CFMutableSetRef _sources0;
    CFMutableSetRef _sources1;
    CFMutableArrayRef _observers;
    CFMutableArrayRef _timers;
};
```

> ## Runloop的Mode

* CFRunLoopModeRef代表RunLoop的运行模式
* 一个RunLoop可以包含多个Mode，每个Mode又包含多个Source0/Source1/Timer/Observer
* 如果需要切换Mode，只能退出当前的Mode，重新再次进入
  * 好处：每个Mode里面的Source0/Source1/Timer/Observer可以区分开来，互不干扰
* 如果Mode里面没有任何Source0/Source1/Timer/Observer，RunLoop就会退出
* 苹果公开提供的 Mode 有两个：`kCFRunLoopDefaultMode (NSDefaultRunLoopMode)` 和 `UITrackingRunLoopMode`
* 苹果还提供了一个操作 Common 标记的字符串：`kCFRunLoopCommonModes (NSRunLoopCommonModes)`，这个不是真正的mode，只是作为一个标志符。被标记的item，可以在包含 Common 标记符的Mode下面运行，苹果公开的两个 Mode ，都属于带有 Common 标记的 Mode

> ## Runloop的运行步骤

1. 通知Observer：进入Loop
2. 通知Observer：即将处理Timers
3. 通知Observer：即将处理Sources
4. 处理Blocks
5. 处理Source0（如果有Block再次处理Blocks）
6. 如果存在Source1，就跳转到第8步
7. 通知Observer：开始休眠（等待消息唤醒）
8. 通知Observer：结束休眠（被某个消息唤醒）
   1. 处理Timer
   2. 处理GCD Async To Main Queue
   3. 处理Source1
9. 处理Blocks
10. 根据前面的执行结果，决定下一步的操作
    1. 回到第2步
    2. 退出Loop
11. 通知Observer：退出Loop

**涉及到方法：**

```objectivec
{
    /// 1. 通知Observers，即将进入RunLoop
    /// 此处有Observer会创建AutoreleasePool: _objc_autoreleasePoolPush();
    __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopEntry);
    do {

        /// 2. 通知 Observers: 即将触发 Timer 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeTimers);
        /// 3. 通知 Observers: 即将触发 Source (非基于port的,Source0) 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeSources);
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_BLOCK__(block);

        /// 4. 触发 Source0 (非基于port的) 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__(source0);
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_BLOCK__(block);

        /// 6. 通知Observers，即将进入休眠
        /// 此处有Observer释放并新建AutoreleasePool: _objc_autoreleasePoolPop(); _objc_autoreleasePoolPush();
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeWaiting);

        /// 7. sleep to wait msg.
        mach_msg() -> mach_msg_trap();


        /// 8. 通知Observers，线程被唤醒
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopAfterWaiting);

        /// 9. 如果是被Timer唤醒的，回调Timer
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_TIMER_CALLBACK_FUNCTION__(timer);

        /// 9. 如果是被dispatch唤醒的，执行所有调用 dispatch_async 等方法放入main queue 的 block
        __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__(dispatched_block);

        /// 9. 如果如果Runloop是被 Source1 (基于port的) 的事件唤醒了，处理这个事件
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE1_PERFORM_FUNCTION__(source1);


    } while (...);

    /// 10. 通知Observers，即将退出RunLoop
    /// 此处有Observer释放AutoreleasePool: _objc_autoreleasePoolPop();
    __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopExit);
}
```

> ## Runloop的休眠原理

![](/assets/2019112504.png)

* 用户态调用`mach_msg()`切换到内核态，内核态中内核实现的`mach_msg()`会完成实际的工作
* 等待消息
  * 没有消息就让线程休眠
  * 有消息就唤醒线程



